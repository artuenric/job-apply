# Especificação do Projeto: Agente de Candidatura Automatizada

> **Objetivo:** Construir um agente de candidatura a vagas com alta taxa de conversão e operação segura, utilizando `browser-use`, evitando detecção por anti-bot, bloqueios de conta (shadowban), reprovações automáticas em ATS e inconsistências de dados.

---

## 1. Problemas e Riscos que Você Precisa Mapear

| Categoria | Problema / Risco Oculto | Como Isso Afeta Sua Candidatura |
| :--- | :--- | :--- |
| **Bloqueio e Anti-Bot** | **Shadowban e limite comportamental no LinkedIn** | Disparar dezenas de *Easy Apply* em poucos minutos ativa penalidades invisíveis (*shadowban*): seu perfil para de aparecer nas buscas de recrutadores e suas candidaturas perdem prioridade, além do risco de suspensão por violação dos Termos de Uso (*LiftmyCV*). O limite técnico falha perto de ~100 envios/dia em contas antigas (e bem menos em contas novas), mas acima de 25/dia o risco de *flag* já dispara (*LiftmyCV*). |
| **Bloqueio e Anti-Bot** | **Fingerprinting de navegador e redimensionamento via CDP** | Instâncias padrão de Chromium/Playwright expõem assinaturas de automação (Canvas, WebGL, fontes, navigator) e o simples ato de redimensionar a janela via protocolo CDP facilita a detecção por Cloudflare, PerimeterX e Akamai (*Browser Use Stealth Docs*). |
| **Barreiras de ATS** | **Verificação humana upstream (SMS / OTP) e contas por empresa** | Ferramentas anti-bot como o Dalia exigem confirmação de código via SMS em tempo real antes de deixar a candidatura entrar no ATS. Além disso, sistemas como Workday exigem criar uma conta própria por empresa com validação de e-mail, travando agentes autônomos. |
| **Reprovação Automática** | **Erro de interpretação em Knockout Questions** | Workday (*Condition Rules*), Greenhouse (*Auto-Reject*), Taleo, iCIMS, LinkedIn (*Must-have questions*) e Indeed descartam o currículo automaticamente se o agente errar uma única pergunta eliminatória (*ResumeAdapter*). LLMs costumam confundir "autorização legal para trabalhar" com "necessidade futura de patrocínio de visto", informar endereço atual em vez de disponibilidade presencial ou somar anos totais de carreira quando a pergunta pede anos específicos em uma tecnologia como SQL (*ResumeAdapter*). |
| **Qualidade e IA** | **Alucinação de experiência e inconsistência com o LinkedIn** | Sem travas rígidas, a IA inventa projetos, infla métricas ou adiciona ferramentas que você nunca usou, além de criar divergências de datas e cargos entre o PDF anexado e o seu perfil público do LinkedIn — o que leva recrutadores a descartarem o candidato por quebra de confiança (*Sprad*). |
| **Qualidade e IA** | **Cartas e respostas com "cara de ChatGPT"** | Cerca de 42% das cartas de apresentação geradas por IA começam com introduções quase idênticas cheias de clichês corporativos, fazendo com que recrutadores descartem a leitura em 6 a 8 segundos (*Sprad*). |
| **Plataformas Locais** | **Tentativa de prompt injection e campos vazios na Gupy** | A IA de ordenação da Gupy não é um modelo generativo baseado em prompts, mas uma rede neural que cruza dados estruturados (Experiência, Formação, Certificados, Distância e, na Candidatura Rápida, Salário). Inserir comandos ocultos no texto não sobe a nota, deixar campos estruturados em branco derruba a aderência e o módulo Smart Vagas conta com detecção de textos gerados por IA (*Gupy Suporte*). |
| **Funil e Operação** | **Auto-sabotagem na mesma empresa e amnésia em entrevistas** | Aplicar sem filtro para várias vagas da mesma empresa em minutos faz recrutadores bloquearem manualmente o remetente por spam (*LiftmyCV*). Além disso, até 30% dos candidatos que usam automação em massa esquecem para qual vaga aplicaram ou qual versão de currículo enviaram quando recebem a ligação de triagem (*Sprad*). |
| **Segurança e Custo** | **Vazamento de NDA e loops infinitos de tokens** | Colar descrições de projetos reais com nomes de clientes ou métricas internas em APIs públicas de LLM viola acordos de confidencialidade (NDA) e leis de privacidade (*Sprad*). No Browser Use, formulários longos ou iframes quebrados podem prender o agente em loops de cliques que drenam créditos de API rapidamente. |

---

## 2. O Que Fazer Para Dar Certo (Plano de Implementação)

Para transformar o `browser-use` em um assistente de alta conversão em vez de um "canhão de spam", implemente estas seis camadas no projeto:

### 1. Configure Sessão Persistente e Navegação Furtiva (Stealth)
- Conecte o Browser Use diretamente ao seu perfil real do Chrome local usando `Browser.from_system_chrome()` para reaproveitar cookies autenticados sem logins repetitivos, ou utilize o navegador em nuvem (`Browser(use_cloud=True)` com `cloud_profile_id`), que já inclui Chromium blindado, randomização de fingerprint, proxies residenciais e resolução de CAPTCHAs (*Browser Use Stealth Docs*).
- Mantenha o tamanho da janela fixo (não ative `allowResizing: true`).
- Programe um rate limit estrito de no máximo **10 a 25 candidaturas por dia no LinkedIn**, com delays aleatórios de **1 a 3 minutos** entre submissões (*LiftmyCV*).

### 2. Separe a Triagem de Vagas da Automação do Navegador
- Construa um funil em duas etapas: um script leve avalia a descrição da vaga antes de abrir o navegador e só aprova vagas em que você cumpra pelo menos **70% a 80% dos requisitos obrigatórios** (*Sprad*).
- Configure o coletor para priorizar vagas publicadas nas **últimas 24 horas** (que possuem taxa de resposta muito superior).
- Crie uma *blocklist* das suas 10 a 20 empresas dos sonhos para aplicar manualmente com pesquisa aprofundada, deixando o agente focado na cobertura sistemática do mercado (*LoopCV*).

### 3. Use o MCP Oficial na Gupy em vez de Raspar a Tela
- Para vagas hospedadas na Gupy, evite automação de navegador pesada na busca e triagem.
- Conecte o servidor oficial de MCP para Candidatos da Gupy (`https://candidates.mcp.api.gupy.io/mcp`) ao seu ambiente para buscar vagas com alta aderência e diagnosticar a estrutura do currículo dentro das regras da plataforma.

### 4. Crie um Arquivo JSON/YAML como "Fonte da Verdade" (Source of Truth)
- Nunca deixe o LLM adivinhar dados no formulário.
- Passe no contexto um arquivo estruturado e imutável com respostas exatas para *knockout questions*:
  - Autorização de trabalho atual vs. necessidade futura de visto;
  - Disponibilidade presencial/híbrida e mudança;
  - Pretensão salarial mínima e desejada;
  - Anos exatos de experiência comprovados por tecnologia (*ResumeAdapter*).
- Use o parâmetro `extend_system_message` do Browser Use para proibir o agente de inventar métricas, cargos ou tecnologias fora desse arquivo, anonimizando qualquer menção a clientes sob NDA (*Sprad*).

### 5. Implemente Modo Copilot (Human-in-the-Loop) com `@tools.action`
- Crie funções customizadas no Browser Use com o decorator `@tools.action` para pausar e solicitar intervenção humana (via terminal ou notificação):
  - Ao encontrar solicitação de código SMS/2FA (*Dalia*);
  - Ao deparar com perguntas discursivas inéditas.
- Nas primeiras semanas, configure o agente no modo semi-automático: preenche os campos, anexa o currículo e pausa antes do envio final para você revisar em poucos segundos e confirmar (*LiftmyCV*).

### 6. Salve o Log Completo e Gere um "Cheat Sheet" Pós-Aplicação
- Adicione uma ação final no script para registrar em planilha ou banco local:
  - Nome da empresa e cargo;
  - Link da vaga e data de envio;
  - Cópia exata do PDF/currículo enviado;
  - Respostas preenchidas no formulário.
- Gere automaticamente um resumo de 5 tópicos destacando os pontos fortes enfatizados para aquela oportunidade, permitindo consulta rápida caso o recrutador entre em contato para triagem (*Sprad*).
