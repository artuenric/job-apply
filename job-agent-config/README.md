# Configuração do Agente de Vagas (`job-agent-config/`)

Este diretório contém a base de conhecimento (*Source of Truth*), regras de elegibilidade, filtros de funil e preferências operacionais do agente.

> **Nota de Privacidade**: Seus arquivos reais de perfil (`01-*.json`, `02-*.json`, etc.) são ignorados pelo `.gitignore` para garantir que seus dados pessoais e histórico profissional nunca sejam versionados acidentalmente no Git.

---

## Como Configurar Seu Perfil

Você pode configurar o seu perfil de duas formas:

### Opção 1: Via Entrevista Interativa no Chat (Recomendado)
Peça ao agente:
> *"Inicie a entrevista com a skill `job-application-grill`."*

O agente percorrerá as 4 fases em árvore de decisão no chat e gerará os arquivos `.json` e `.md` automaticamente nesta pasta.

### Opção 2: Manualmente via Templates de Exemplo
Copie os arquivos da pasta [`examples/`](examples/) para a raiz de `job-agent-config/`, renomeando-os para remover o sufixo `.example`:
1. `01-funnel-preferences.example.json` -> `01-funnel-preferences.json`
2. `01-funnel-preferences.example.md` -> `01-funnel-preferences.md`
3. `02-knockout-rules.example.json` -> `02-knockout-rules.json`
4. `02-knockout-rules.example.md` -> `02-knockout-rules.md`
5. `03-profile-source-of-truth.example.json` -> `03-profile-source-of-truth.json`
6. `03-profile-source-of-truth.example.md` -> `03-profile-source-of-truth.md`
7. `04-autonomy-and-operation.example.json` -> `04-autonomy-and-operation.json`
8. `04-autonomia-e-operacao.example.md` -> `04-autonomia-e-operacao.md`

Preencha os campos com seus dados reais, anos de experiência e links de contato.
