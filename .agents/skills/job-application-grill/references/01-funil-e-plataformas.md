# Fase 1: Funil de Vagas, Plataformas e Criterios de Corte

## Objetivo da Fase

Definir com precisao onde o agente vai buscar vagas, quais vagas entram no funil de aplicacao e quais devem ser descartadas ou reservadas para aplicacao manual.

**Modo do Entrevistador**: **Consultivo**. Para cada pergunta sobre estrategia de funil, filtros de recencia e escolha de plataformas, apresente uma recomendacao clara baseada nas melhores praticas de conversao e seguranca contra bloqueios.

## Arvore de Decisao (Ordem de Entrevista)

Percorra os ramos abaixo em sequencia, resolvendo uma fronteira por vez:

### Ramo 1: Cargos-Alvo, Senioridade e Idiomas

1. **Titulos de cargo principais e variacoes**:
   - Pergunte quais sao os titulos exatos de vaga que o usuario busca (em portugues e/ou ingles) e quais palavras no titulo da vaga devem provocar descarte imediato (ex.: "Estagio", "Senior" se for Pleno, "Suporte", "Vendas").
   - *Recomendacao para apresentar*: Separar os titulos em `primary_titles` (alta prioridade) e `acceptable_synonyms`, alem de uma lista `title_negative_keywords` para evitar aplicar em vagas de areas vizinhas na mesma empresa.

2. **Senioridade e escopo de atuacao**:
   - Pergunte quais niveis de senioridade sao aceitos (Junior, Pleno/Mid, Senior, Staff, Lead/Manager) e se vagas de contribuinte individual (IC) e gestao devem ser misturadas ou separadas.

3. **Idiomas das vagas e dos formularios**:
   - Pergunte em quais idiomas o agente pode aplicar e qual versao base do curriculo deve ser usada para cada idioma.

### Ramo 2: Modalidade de Trabalho e Geografia

1. **Regime de trabalho aceito**:
   - Pergunte se o usuario aceita `100% Remoto`, `Hibrido` e/ou `Presencial`.
   - **Condicional (Se aceitar Hibrido ou Presencial)**: Destrave as perguntas de Cidade/Estado base, raio maximo de deslocamento em quilometros, limite de dias presenciais por semana e disponibilidade para mudanca de cidade/pais.
   - **Condicional (Se aceitar Remoto Internacional)**: Destrave a pergunta sobre fusos horarios permitidos (ex.: UTC-3 +/- 3 horas) e tipos de contrato aceitos (CLT, PJ/B2B, Contractor via Deel/Remote, W-2).

### Ramo 3: Pisos Salariais e Remuneracao

1. **Piso salarial por moeda e tipo de contrato**:
   - Pergunte o valor minimo aceitavel e a faixa ideal para cada combinacao de moeda (BRL, USD, EUR) e modalidade (CLT vs. PJ/Contractor).
   - Pergunte qual estrategia adotar quando o formulario exigir um numero unico obrigatorio em campo numerico vs. quando aceitar texto livre.
   - *Recomendacao para apresentar*: Definir um `minimum_walk_away` (abaixo do qual o agente descarta a vaga), um `target_number` (para campos numericos obrigatorios em ATS) e uma faixa textual para campos abertos.

### Ramo 4: Plataformas, Score de Corte e Empresas Tier 1

1. **Plataformas habilitadas no fluxo**:
   - Pergunte quais plataformas entrarao na automacao (ex.: LinkedIn Easy Apply, Gupy via MCP oficial de candidatos, Greenhouse, Lever, Ashby, Workday).
   - *Recomendacao para apresentar*: Usar o MCP oficial da Gupy para triagem/diagnostico na Gupy, focar o `browser-use` em LinkedIn, Greenhouse, Lever e Ashby, e tratar Workday apenas em modo semi-automatico (*Copilot*) devido a criacao de conta por empresa.

2. **Criterio de aderencia minima e janela de publicacao**:
   - Pergunte qual porcentagem minima dos requisitos obrigatorios da vaga o perfil precisa atingir para o agente prosseguir e qual a idade maxima do anuncio.
   - *Recomendacao para apresentar*: Exigir no minimo 75% de aderencia aos requisitos obrigatorios, priorizar vagas publicadas nas ultimas 24 a 72 horas e aplicar *cooldown* de 30 dias por empresa (maximo de 1 vaga ativa por empregador).

3. **Blocklist e Empresas Tier 1 (Sonho)**:
   - Pergunte: (a) empresas onde o usuario **nunca** quer aplicar (empregador atual, ex-empregadores especificos, consultorias indesejadas) e (b) as 10 a 20 empresas "Tier 1" onde o robo deve apenas alertar a vaga, deixando a aplicacao 100% manual e personalizada.

## Saidas Obrigatórias da Fase 1

Ao concluir todos os ramos acima, gere e salve os dois arquivos abaixo na pasta de configuracao:

### 1. Arquivo JSON (`01-funnel-preferences.json`)

```json
{
  "phase": "01-funnel-preferences",
  "roles": {
    "primary_titles": [],
    "acceptable_synonyms": [],
    "title_negative_keywords": [],
    "seniority_levels": [],
    "languages": ["pt-BR", "en-US"]
  },
  "location_and_work_model": {
    "accepted_models": ["remote", "hybrid"],
    "base_city": "",
    "base_country": "Brazil",
    "max_commute_km": 0,
    "max_onsite_days_per_week": 0,
    "willing_to_relocate": false,
    "accepted_timezones": []
  },
  "compensation": {
    "BRL_CLT": {
      "minimum_monthly": 0,
      "target_monthly": 0,
      "currency": "BRL"
    },
    "BRL_PJ": {
      "minimum_monthly": 0,
      "target_monthly": 0,
      "currency": "BRL"
    },
    "USD_CONTRACTOR": {
      "minimum_monthly": 0,
      "target_monthly": 0,
      "currency": "USD"
    }
  },
  "funnel_rules": {
    "min_match_score_percent": 75,
    "max_job_age_hours": 72,
    "max_active_applications_per_company": 1,
    "company_cooldown_days": 30,
    "enabled_platforms": ["linkedin", "greenhouse", "lever", "gupy_mcp"],
    "blacklisted_companies": [],
    "tier_1_manual_only_companies": []
  }
}
```

### 2. Arquivo Markdown (`01-funnel-preferences.md`)

Estruture o arquivo `.md` contendo:

- **Resumo de Triagem Rapida**: Lista direta de criterios de aprovacao e descarte imediato para o LLM avaliador ler antes de abrir o navegador.
- **Respostas Padrao sobre Pretensao Salarial (por tipo de campo)**:
  - *Campo numerico estrito (mensal/anual)*: Valor exato a colar em BRL, USD ou EUR.
  - *Campo de texto curto*: Frase curta informando a faixa pretendida e abertura a negociacao conforme pacote de beneficios.
  - *Quando a vaga exige pretensao fora do piso*: Regra de acao (descartar vaga vs. pausar para revisao humana).
- **Respostas Padrao sobre Modalidade e Localizacao**:
  - Texto pronto para perguntas abertas sobre disponibilidade para regime hibrido, fuso horario e viagens eventuais.
