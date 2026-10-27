# Regras Operacionais e Diretrizes de Execução (`docs/rules/operational-rules.md`)

Este arquivo define as regras operacionais mandatórias para a execução do **Agente CLI**, limites de atuação autônoma e salvaguardas de integridade do sistema e do perfil do usuário.

---

## RULE-001: Supervisão Humana Mandatória (Human-in-the-Loop) em Etapas Críticas

- **Contexto / Motivação:** Candidaturas automáticas sem revisão humana podem enviar dados desatualizados, responder incorretamente a perguntas eliminatórias (*knockout questions*) ou disparar submissões indesejadas em vagas fora do perfil. Além disso, etapas de 2FA e SMS exigem ação manual do titular da conta.
- **O que faz / Diretriz:** O Agente CLI **DEVE** pausar e solicitar validação explícita do usuário antes de:
  1. Submeter formulários finais de candidatura (*Submit application*).
  2. Responder perguntas eliminatórias (*knockout questions*) que não constem com certeza absoluta na sua base de dados (*Source of Truth*).
  3. Preencher faixas salariais ou pretensão de remuneração sem confirmação prévia.
  4. Prosseguir em telas de desafio de segurança (2FA, SMS, CAPTCHA).
- **Impacto / Consequências:** Garante 100% de controle do usuário, evita desqualificações automáticas por respostas errôneas e protege a reputação do candidato.

---

## RULE-002: Arquitetura de Cérebro Único sem Dependências Externas de LLM

- **Contexto / Motivação:** Scripts Python autônomos contendo chamadas secundárias para APIs de LLM pagas (OpenAI, Gemini, Anthropic) introduzem custos desnecessários, vazamentos de chave e fragilidade de manutenção. Servidores de inferência locais (Ollama, vLLM) consomem RAM/VRAM massiva, travando o computador pessoal do usuário.
- **O que faz / Diretriz:** Toda inteligência, interpretação semântica de formulários, tomada de decisão e geração de conteúdo para respostas de vagas são operadas **exclusivamente pelo Agente CLI** no ambiente interativo. É estritamente proibido criar scripts auxiliares que instanciem clientes de LLM ou exijam chaves de API externas.
- **Impacto / Consequências:** Operação com custo zero de API em scripts, leveza computacional no ambiente local e total transparência das decisões no terminal do chat.

---

## RULE-003: Execução de Automação Estritamente via Browser Harness CDP

- **Contexto / Motivação:** Scripts tradicionais de automação (Selenium, Playwright standalone em modo headless) injetam variáveis de ambiente reconhecidas por sistemas anti-bot (Cloudflare, Datadome, LinkedIn Shield), resultando em bloqueios ou *shadowban*.
- **O que faz / Diretriz:** Toda automação de navegação deve ser executada através da interface e dos comandos do `browser-use` (`browser-harness`), conectando-se via CDP à instância real do Google Chrome do usuário já em execução.
- **Impacto / Consequências:** Máxima semelhança a comportamento humano orgânico, reaproveitamento da sessão autenticada e observabilidade visual direta pelo usuário.

---

## RULE-004: Estrutura Obrigatória de Dados de Vagas nas Tasks do Kanban (Memorycard)

- **Contexto / Motivação:** Um dos maiores riscos em processos automatizados é a perda de contexto ou "amnésia" em entrevistas técnicas (não saber o que a empresa pedia, qual versão do currículo foi enviada ou quais diferenciais foram destacados). Além disso, a esteira de triagem precisa de dados estruturados para rastrear prazos e follow-ups.
- **O que faz / Diretriz:** Toda oportunidade descoberta, triada ou aplicada pelo agente deve ser registrada como uma task no `.memorycard` utilizando obrigatoriamente o modelo de projeto `job` ([`.memorycard/models/job.md`](../../.memorycard/models/job.md)). Cada task deve conter de forma estruturada:
  1. **Metadados da Vaga & Empresa**: Título oficial do cargo, nome da empresa, modalidade (Remoto/Híbrido/Presencial), link da publicação original, origem (post/comentário/easy apply) e percentual de aderência (*Match Score*).
  2. **Descrição Completa**: Texto ou escopo principal da oportunidade extraído no momento da triagem.
  3. **Requisitos Obrigatórios vs. Desejáveis/Diferenciais**: Divisão explícita em formato de checklist Markdown.
  4. **Prazos & Follow-up**: Data de descoberta, data de submissão da candidatura, data limite para expiração da vaga e data sugerida para follow-up com recrutador.
  5. **Checklist Operacional (`## Todo`)**: Acompanhamento do ciclo de vida da aplicação (triagem de match, knockout questions, preenchimento, upload de PDF e submissão).
  6. **Histórico de Ocorrências (`## Comments`)**: Registro obrigatório de motivos de pausa (ex.: "Pausada por solicitação de SMS Dalia", "Exige pergunta inédita sobre certificação X").
- **Impacto / Consequências:** Rastreabilidade auditável fim a fim, eliminação de candidaturas duplicadas ou cegas, e suporte a consultas rápidas durante entrevistas de triagem com recrutadores.
