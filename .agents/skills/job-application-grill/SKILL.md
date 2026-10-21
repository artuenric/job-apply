---
name: job-application-grill
description: Conduz uma entrevista estruturada em 4 fases (estilo grill-me) para mapear preferencias de vagas, blindar knockout questions, extrair experiencias reais (Source of Truth) e configurar o nivel de autonomia antes de rodar automacoes de candidatura com IA e browser-use. Use sempre que o usuario pedir para configurar, iniciar, entrevistar ou atualizar suas preferencias de candidatura automatizada a vagas, estruturar seu perfil base para ATS ou preparar os arquivos JSON e Markdown para o agente de aplicacao.
---

# Job Application Grill

Conduz uma entrevista em arvore de decisao dividida em 4 fases independentes para construir a base de dados e as regras de operacao de um agente de candidatura a vagas com IA (como `browser-use`), gerando ao final de cada fase um arquivo `.json` (para consumo direto pelo codigo e copiar/colar) e um arquivo `.md` (para respostas abertas e adaptacoes dinamicas de formularios).

## When to Use

- O usuario quer configurar do zero suas preferencias, filtros, salários e plataformas antes de automatizar candidaturas a vagas.
- O usuario pede para ser entrevistado (estilo `grill-me`) para mapear *knockout questions*, elegibilidade legal, visto ou anos de experiencia por tecnologia.
- O usuario precisa extrair e padronizar seu historico profissional (*Source of Truth*) alinhado ao LinkedIn e anonimizar projetos sob NDA para evitar alucinacoes em formularios de ATS.
- O usuario deseja definir limites de envio (*rate limits*), regras de pausa humana (*Human-in-the-Loop*) e fluxos de operacao do `browser-use`.

## Steps

1. **Verifique o estado atual no diretorio do projeto**:
   Inspecione a pasta de configuracao do projeto (por padrao `./job-agent-config/`) para identificar quais pares de arquivos (`.json` e `.md`) das fases `01` a `04` ja foram concluidos. Nunca carregue todas as fases de uma vez na janela de contexto para evitar perda de foco (*dumb zone*).

2. **Carregue apenas o sub-arquivo da fase ativa**:
   Leia exclusivamente o arquivo correspondente a fase que sera executada na sessao atual:

   - **Fase 1 (Funil, Plataformas e Criterios de Corte)**: Leia `references/01-funil-e-plataformas.md`.
   - **Fase 2 (Knockout Questions e Elegibilidade)**: Leia `references/02-knockout-e-elegibilidade.md`.
   - **Fase 3 (Experiencias e Source of Truth)**: Leia `references/03-experiencias-source-of-truth.md`.
   - **Fase 4 (Autonomia, Rate Limits e Operacao)**: Leia `references/04-autonomia-e-operacao.md`.

3. **Execute a entrevista percorrendo a arvore de decisao ramo a ramo**:
   Faca perguntas em blocos curtos (1 a 3 perguntas da mesma fronteira de decisao por turno). So destrave perguntas filhas depois que a resposta da pergunta-pai estiver definida. Respeite o modo de recomendacao de cada fase:

   - Nas **Fases 1 e 4** (decisoes de estrategia, funil e arquitetura do robo), apresente sempre uma resposta recomendada baseada em boas praticas anti-bloqueio e de conversao em ATS.
   - Nas **Fases 2 e 3** (dados factuais, autorizacao de trabalho, visto, anos de experiencia, salarios minimos e metricas de carreira), **nunca** sugira valores padrao para o usuario apenas concordar. Exija dados exatos do usuario para impedir alucinacoes.

4. **Consolide e salve a dupla saida (JSON + Markdown) ao fechar cada fase**:
   Assim que todos os ramos da fase atual estiverem resolvidos, valide os dados com o usuario e grave os dois arquivos daquela fase antes de avancar:

   - **Arquivo `.json`**: Dados estritos, tipados e prontos para copiar/colar em formularios ou carregar via script no `browser-use`.
   - **Arquivo `.md`**: Guia estruturado com templates de respostas discursivas, variacoes por tipo de formulario/ATS e instrucoes de adaptacao contextual.

5. **Valide a consistencia cruzada antes de encerrar**:
   Confirme que os nomes de cargos, datas e tecnologias do arquivo `03-profile-source-of-truth` batem com os anos de experiencia declarados no `02-knockout-rules` e com os filtros definidos no `01-funnel-preferences`.

## Gotchas

- **Passividade em dados factuais**: Jamais preencha anos de experiencia em uma linguagem, status de visto ou pretensao salarial por conta propria. Se o usuario der uma resposta vaga (ex.: "tenho bastante tempo de Python"), pergunte o numero exato de anos comprovaveis no curriculo.
- **Sobrecarga de contexto**: Nunca misture perguntas de fases diferentes no mesmo turno. Termine e salve os arquivos `.json` e `.md` da fase atual antes de abrir o sub-arquivo da proxima fase.
- **Divergencia com o perfil publico do LinkedIn**: Alerte o usuario sempre que uma adaptacao sugerida para o curriculo alterar datas de inicio/fim ou titulos de cargos em relacao ao perfil publico do LinkedIn, pois recrutadores cruzam ambos os lados.
