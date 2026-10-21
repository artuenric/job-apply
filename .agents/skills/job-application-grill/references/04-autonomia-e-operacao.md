# Fase 4: Nivel de Autonomia, Human-in-the-Loop e Operacao

## Objetivo da Fase

Definir exatamente como o agente com `browser-use` vai operar no navegador, quais limites de seguranca (*rate limits* e *stealth*) estarao ativos, em quais situacoes ele deve pausar para chamar o humano e como cada candidatura sera registrada para consulta antes das entrevistas.

**Modo do Entrevistador**: **Arquitetural e Preventivo**. Para cada decisao operacional, apresente a configuracao recomendada para evitar bloqueio de conta, falhas em formularios e perda de controle sobre as vagas aplicadas.

## Arvore de Decisao (Ordem de Entrevista)

### Ramo 1: Sessao do Navegador, Stealth e Rate Limits

1. **Modo de execucao do navegador no `browser-use`**:
   - Pergunte se o usuario vai rodar conectando ao perfil local do Chrome (`Browser.from_system_chrome()`) ou usando perfil persistente em nuvem (`use_cloud=True` com `cloud_profile_id`).
   - *Recomendacao para apresentar*: Usar `Browser.from_system_chrome()` localmente com janela de tamanho fixo (sem redimensionamento dinamico via CDP) ou `use_cloud=True` com stealth ativo para aproveitar cookies existentes sem fazer logins repetidos.
2. **Limites diarios e cadencia de envio por plataforma**:
   - Pergunte o volume maximo diario por plataforma e o intervalo entre candidaturas.
   - *Recomendacao para apresentar*: No LinkedIn Easy Apply, limitar entre 10 e 25 candidaturas por dia, com pausa aleatoria (*jitter*) de 60 a 180 segundos entre submissoes e limite de `max_iterations` por formulario (ex.: 25 passos) para evitar loops infinitos de tokens.

### Ramo 2: Nivel de Autonomia e Gatilhos de Pausa (*Human-in-the-Loop*)

1. **Escolha do modo de submissao**:
   - Pergunte qual nivel de autonomia o usuario deseja para cada plataforma:
     - **Modo 1 - Copilot (Revisao antes do envio)**: O agente preenche todo o formulario, anexa o PDF, valida os campos e pausa antes de clicar no botao final (*Submit* / *Enviar*) para o humano aprovar.
     - **Modo 2 - Hibrido por Confianca**: Envia sozinho quando 100% das perguntas do formulario tem correspondencia exata no JSON das Fases 1 a 3; pausa e chama o humano apenas se surgir campo desconhecido ou pergunta discursiva nova.
     - **Modo 3 - Autonomo com Guardrails**: Envia automaticamente dentro dos limites diarios, pausando apenas em barreiras tecnicas (CAPTCHA, 2FA/SMS, criacao de conta).
   - *Recomendacao para apresentar*: Comecar na primeira semana no **Modo 1 (Copilot)** para calibrar os seletores e respostas, migrando depois para o **Modo 2 (Hibrido por Confianca)**.

2. **Canal de notificacao e intervencao humana (`@tools.action`)**:
   - Pergunte como o agente deve alertar o usuario quando precisar de um codigo SMS/OTP (ex.: Dalia, Greenhouse verification code, login do Workday) ou revisao de resposta (ex.: alerta sonoro no terminal local, mensagem via bot do Telegram/Slack ou fila de pendencias).

### Ramo 3: Rastreamento Pos-Aplicacao e Cheat Sheet de Entrevista

1. **Registro de auditoria de cada candidatura**:
   - Pergunte onde salvar o log de aplicacoes (ex.: arquivo JSONL/CSV local, SQLite ou planilha) e quais artefatos guardar por vaga (link, descricao completa da vaga salva offline, versao exata do PDF enviado e lista de perguntas/respostas preenchidas).
2. **Geracao automatica de Cheat Sheet para entrevistas**:
   - Pergunte se o agente deve gerar um resumo rapido em Markdown para cada vaga aplicada, destacando os 5 pontos do curriculo enfatizados naquela aplicacao para consulta rapida caso o recrutador ligue.

## Saidas Obrigatórias da Fase 4

Ao concluir todos os ramos acima, gere e salve os dois arquivos abaixo:

### 1. Arquivo JSON (`04-agent-autonomy.json`)

```json
{
  "phase": "04-agent-autonomy",
  "browser_config": {
    "mode": "local_system_chrome",
    "allow_window_resizing": false,
    "max_steps_per_application": 25,
    "save_screenshots_on_error": true
  },
  "rate_limits": {
    "linkedin": {
      "max_daily_applications": 20,
      "min_delay_seconds": 60,
      "max_delay_seconds": 180
    },
    "external_ats": {
      "max_daily_applications": 25,
      "min_delay_seconds": 45,
      "max_delay_seconds": 120
    }
  },
  "autonomy_and_hitl": {
    "default_submission_mode": "COPILOT_PAUSE_BEFORE_SUBMIT",
    "platform_overrides": {
      "workday": "COPILOT_PAUSE_BEFORE_SUBMIT",
      "linkedin": "HYBRID_CONFIDENCE_THRESHOLD"
    },
    "mandatory_pause_triggers": [
      "SMS_OR_EMAIL_OTP_REQUIRED",
      "CAPTCHA_DETECTED",
      "UNMAPPED_KNOCKOUT_QUESTION",
      "OPEN_ENDED_QUESTION_LOW_CONFIDENCE",
      "SALARY_BELOW_MINIMUM_FLOOR"
    ],
    "human_notification_channel": "terminal_prompt"
  },
  "logging_and_tracking": {
    "log_directory": "./applications_log",
    "save_job_description_snapshot": true,
    "save_submitted_pdf_copy": true,
    "save_form_qa_transcript": true,
    "generate_interview_cheat_sheet_md": true
  }
}
```

### 2. Arquivo Markdown (`04-agent-autonomy.md`)

Estruture o arquivo `.md` contendo:

- **Protocolo Operacional do Agente (`extend_system_message`)**:
  - Bloco de instrucoes pronto para ser injetado no `Agent(..., extend_system_message=...)` do `browser-use`, detalhando passo a passo como navegar no formulario, como consultar os arquivos JSON/MD das Fases 1 a 3 e quando acionar as `@tools.action` de pausa humana.
- **Arvore de Decisao para Excecoes em Formularios**:
  - O que fazer quando um campo obrigatorio nao aceita o formato esperado (ex.: telefone sem DDI, CEP americano exigido em vaga remota, upload de carta de apresentacao obrigatorio).
- **Template do Cheat Sheet Pos-Aplicacao**:
  - Modelo em Markdown que o agente deve preencher e salvar apos cada candidatura enviada (Resumo da Empresa, Requisitos-Chave da Vaga, Experiencias Destacadas, Respostas Dadas no Formulario e Perguntas Sugeridas para Fazer ao Recrutador).
