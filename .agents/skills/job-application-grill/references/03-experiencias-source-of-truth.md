# Fase 3: Extracao de Experiencias e Source of Truth

## Objetivo da Fase

Construir a fonte unica da verdade (*Source of Truth*) sobre a carreira do usuario, garantindo que o agente tenha material rico para adaptar curriculos e responder perguntas discursivas de formularios sem jamais inventar metricas, inflar tecnologias ou entrar em contradicao com o perfil publico do LinkedIn.

**Modo do Entrevistador**: **Investigativo e Rigoroso**. Questione descricoes genericas ("participei do projeto X") até extrair o problema real, a acao individual do usuario, as ferramentas utilizadas e o resultado mensuravel ou observavel. Exija anonimizacao de dados protegidos por NDA.

## Arvore de Decisao (Ordem de Entrevista)

### Ramo 1: Identidade, Links Oficiais e Consistencia com o LinkedIn

1. **Dados de contato e links verificados**:
   - Colete nome completo, e-mail dedicado para candidaturas, telefone com DDI, cidade/estado, URL do LinkedIn, GitHub, Portfolio/Site pessoal e links de curriculos base em PDF (PT e EN).
2. **Trava de espelhamento com o LinkedIn**:
   - Verifique com o usuario se os nomes das empresas, titulos de cargos e meses/anos de inicio e fim que entrarao no JSON sao **identicos** aos que estao visiveis hoje no perfil publico do LinkedIn dele.

### Ramo 2: Sabatina Profunda por Experiencia Profissional

Para cada experiencia profissional (da mais recente para a mais antiga), conduza o *grilling* com as seguintes perguntas antes de passar para a proxima empresa:

1. **Metadados imutaveis**:
   - Empresa, cargo oficial no LinkedIn, mes/ano de inicio, mes/ano de termino (ou atual) e modelo de trabalho.
2. **Contexto e escopo real**:
   - Qual era o produto/area, tamanho do time ou volume da operacao?
3. **Conquistas e metricas defensaveis (Formato STAR / XYZ)**:
   - Quais foram as 3 a 5 maiores entregas nesse cargo?
   - Para cada entrega: Qual era a dor inicial? O que voce fez diretamente? Qual foi o resultado numerico ou qualitativo real que voce consegue defender em uma entrevista tecnica?
4. **Stack e ferramentas realmente utilizadas**:
   - Quais tecnologias, metodologias e ferramentas voce usou na pratica dentro dessa empresa especifica (para que o agente nao atribua uma ferramenta de 2025 a um emprego de 2020)?
5. **Filtro de NDA e Confidencialidade**:
   - Ha algum nome de cliente interno, faturamento confidencial ou propriedade intelectual nesse relato que precise ser reescrito de forma anonimizada (ex.: *"grande varejista nacional"* em vez do nome restrito)?

### Ramo 3: Projetos Relevantes, Formacao e Narrativas para Formularios

1. **Projetos independentes, open-source ou academicos**:
   - Colete projetos praticos com link verificavel que possam ser citados quando uma vaga pedir comprovacao em uma tecnologia especifica.
2. **Narrativas-base para perguntas abertas classicas de ATS**:
   - Entreviste o usuario para coletar fatos reais (sem clichês de IA) para responder as 5 perguntas abertas mais comuns em formularios:
     - *Resumo profissional em 3 linhas (Elevator Pitch)*.
     - *Maior desafio tecnico/operacional resolvido na carreira*.
     - *Exemplo de falha, conflito ou mudanca de rota e o que aprendeu*.
     - *Motivacao real para o momento atual de carreira*.
     - *Template modular de "Por que esta empresa / esta vaga"* (onde o agente so preenche a conexao entre um fato real da empresa e uma entrega real do usuario).

## Saidas Obrigatórias da Fase 3

Ao concluir todos os ramos acima, gere e salve os dois arquivos abaixo:

### 1. Arquivo JSON (`03-profile-source-of-truth.json`)

```json
{
  "phase": "03-profile-source-of-truth",
  "personal_info": {
    "full_name": "",
    "email": "",
    "phone_e164": "",
    "location_city_state": "",
    "country": "Brazil",
    "links": {
      "linkedin": "",
      "github": "",
      "portfolio": ""
    },
    "resume_files": {
      "pt_br_pdf_path": "",
      "en_us_pdf_path": ""
    }
  },
  "anti_hallucination_rules": {
    "never_alter_company_names_or_dates": true,
    "never_claim_unlisted_tools": true,
    "nda_sanitized": true
  },
  "work_experiences": [
    {
      "id": "exp_01",
      "company": "",
      "official_linkedin_title": "",
      "allowed_title_framing_variants": [],
      "start_date": "YYYY-MM",
      "end_date": "Present",
      "is_current": true,
      "summary_context": "",
      "verified_achievements": [
        {
          "theme": "",
          "action": "",
          "measurable_impact": "",
          "tools_used": [],
          "bullet_pt": "",
          "bullet_en": ""
        }
      ],
      "skills_proven_in_this_role": []
    }
  ],
  "projects": [
    {
      "name": "",
      "url": "",
      "description_pt": "",
      "description_en": "",
      "tech_stack": []
    }
  ]
}
```

### 2. Arquivo Markdown (`03-profile-source-of-truth.md`)

Estruture o arquivo `.md` para servir como banco de respostas adaptaveis conforme a pergunta do formulario:

- **Regras de Ouro de Escrita (Anti-Cliche de IA)**:
  - Lista de palavras e aberturas proibidas (ex.: *"Venho por meio desta"*, *"Sou um profissional apaixonado e sinergico"*, *"I am thrilled to apply"*) e instrucoes de tom direto, humano e ancorado em fatos.
- **Banco de Historias STAR por Tema**:
  - Historias reais do usuario catalogadas por competencia (ex.: *Lideranca / Autonomia*, *Otimizacao de Performance / Custos*, *Resolucao de Problemas Complexos*, *Trabalho sob Pressao*) em portugues e ingles para o agente adaptar em campos discursivos de 100 a 300 palavras.
- **Estrutura de Resposta para "Por que voce quer trabalhar aqui?"**:
  - Molde enxuto em 2 paragrafos que cruza 1 dado real da descricao da vaga com 1 conquista verificada do JSON, sem bajulacao generica.
