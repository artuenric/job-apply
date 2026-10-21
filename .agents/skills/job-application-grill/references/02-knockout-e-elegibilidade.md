# Fase 2: Blindagem de Knockout Questions e Elegibilidade

## Objetivo da Fase

Mapear todas as respostas binarias, numericas e de elegibilidade legal que sistemas de ATS (Workday, Greenhouse, Taleo, iCIMS, LinkedIn, Gupy) utilizam para reprovar candidatos automaticamente (*Auto-Reject* / *Disqualification*).

**Modo do Entrevistador**: **Estrito e Factual (Sem Recomendacoes de Dados)**. Nesta fase, **nunca** sugira respostas padrao sobre autorizacao de trabalho, visto, certificacoes ou anos de experiencia. Se uma resposta estiver ambigua, faca perguntas de verificacao ate obter o fato exato.

## Arvore de Decisao (Ordem de Entrevista)

### Ramo 1: Autorizacao Legal de Trabalho vs. Patrocinio de Visto

Explique ao usuario que ATS separam duas perguntas classicas e que confundir ambas gera reprovacao imediata. Colete a resposta exata para cada jurisdicao que o usuario selecionou na Fase 1 (ex.: Brasil, EUA, Uniao Europeia, Reino Unido, Canada):

1. **Pergunta Tipo A - Autorizacao Legal Atual**:
   - *"Voce esta legalmente autorizado a trabalhar neste pais atualmente?"* (Considerar se a vaga e remota como Contractor/B2B do Brasil ou se exige residencia fiscal local).
2. **Pergunta Tipo B - Necessidade de Visa Sponsorship**:
   - *"Voce precisara, agora ou no futuro, de patrocinio de visto (ex.: H-1B, Work Permit) para trabalhar?"*
3. **Cidadanias, vistos vigentes e modalidade B2B internacional**:
   - Pergunte se o usuario possui dupla cidadania, visto ativo ou empresa aberta (CNPJ/MEI/LTDA) para faturar contratos internacionais (*Contractor*) sem necessidade de visto de trabalho local.

### Ramo 2: Logistica, Disponibilidade e Verificacoes Legais

1. **Aviso previo e data de inicio (*Notice Period*)**:
   - Pergunte quantos dias ou semanas o usuario precisa para iniciar em um novo emprego (ex.: imediato, 15 dias, 30 dias) e qual data colocar quando o formulario exigir formato `DD/MM/AAAA`.
2. **Background Check, CNH e restricoes contratuais**:
   - Pergunte se o usuario concorda com *background check* / verificacao de antecedentes, se possui CNH (e categoria), se ja trabalhou anteriormente em alguma empresa-alvo e se possui clausula de *non-compete* ativa.
3. **Formacao academica e certificacoes exigidas**:
   - Pergunte o nivel de escolaridade concluido (Graduacao, Pos/MBA, Mestrado, Ensino Medio/Tecnico), status (Concluido vs. Em andamento), mes/ano de conclusao e certificacoes oficiais ativas.

### Ramo 3: Matriz Exata de Anos de Experiencia por Competencia

ATS usam perguntas numericas como *"How many years of work experience do you have with [Tool]?"* como corte automatico.

1. **Anos totais de experiencia profissional**:
   - Pergunte o numero exato de anos de experiencia profissional na area-alvo.
2. **Anos de experiencia por tecnologia, ferramenta ou habilidade**:
   - Liste todas as ferramentas, linguagens, frameworks e metodologias do usuario e pergunte o numero inteiro exato de anos de experiencia comprovavel para cada uma.
   - Defina a **Regra de Fallback**: O que o agente deve fazer quando um formulario perguntar sobre uma ferramenta que nao esta mapeada na matriz (ex.: responder `0` se nunca usou ou pausar a execucao e chamar o humano).

### Ramo 4: Campos Demograficos e Diversidade (EEOC / Auto-Identificacao)

1. **Politica de preenchimento**:
   - Pergunte se o usuario deseja preencher dados opcionais de diversidade/genero/etnia/PCD ou se prefere que o agente selecione sempre *"Prefiro nao informar"* (*Decline to self-identify*) quando o campo for obrigatorio.

## Saidas Obrigatórias da Fase 2

Ao concluir todos os ramos acima, gere e salve os dois arquivos abaixo:

### 1. Arquivo JSON (`02-knockout-rules.json`)

```json
{
  "phase": "02-knockout-rules",
  "work_authorization": {
    "Brazil": {
      "legally_authorized_to_work": true,
      "requires_visa_sponsorship_now_or_future": false
    },
    "United_States": {
      "legally_authorized_local_employment": false,
      "authorized_as_remote_foreign_contractor_b2b": true,
      "requires_visa_sponsorship_now_or_future": false
    },
    "European_Union": {
      "legally_authorized_local_employment": false,
      "authorized_as_remote_foreign_contractor_b2b": true,
      "requires_visa_sponsorship_now_or_future": false
    },
    "citizenships": ["Brazilian"],
    "has_active_b2b_entity_cnpj": true
  },
  "logistics": {
    "notice_period_days": 15,
    "notice_period_weeks": 2,
    "immediate_start_available": false,
    "willing_to_undergo_background_check": true,
    "has_drivers_license": false,
    "has_active_non_compete": false
  },
  "education": {
    "highest_level": "",
    "degree_name": "",
    "institution": "",
    "graduation_year": null,
    "completed": true,
    "active_certifications": []
  },
  "years_of_experience": {
    "total_professional_years": 0,
    "by_skill_or_tool": {},
    "unlisted_skill_policy": "PAUSE_AND_ASK_HUMAN"
  },
  "eeoc_and_diversity_defaults": {
    "disability_status": "Prefer not to answer",
    "veteran_status": "I am not a protected veteran",
    "gender_and_ethnicity_policy": "Prefer not to answer"
  }
}
```

### 2. Arquivo Markdown (`02-knockout-rules.md`)

Estruture o arquivo `.md` contendo:

- **Guia de Desambiguacao de Perguntas Eliminatorias**:
  - Explicacao clara de como responder variacoes de perguntas sobre visto e contrato internacional (ex.: quando o formulario pergunta *"Are you legally authorized to work in the US?"* em uma vaga *Global Remote Contractor* vs. em uma vaga presencial W-2).
- **Tabela de Anos de Experiencia e Sinonimos**:
  - Agrupamento de tecnologias equivalentes (ex.: se o formulario perguntar sobre *PostgreSQL / SQL / Bancos Relacionais*, qual valor numerico usar e qual justificativa curta anexar caso haja campo de texto).
- **Respostas Padrao para Logistica e Aviso Previo**:
  - Frases prontas em portugues e ingles explicando disponibilidade de inicio, transicao de projetos e modalidade de contratacao (CLT ou PJ/B2B).
