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
