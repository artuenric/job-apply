# Diretrizes e Roteador do Agente (`AGENTS.md`)

> **Roteador Central de Contexto:** Toda a documentação detalhada, especificações de requisitos, regras operacionais e decisões de arquitetura (ADRs) estão centralizadas em **[`docs/index.md`](docs/index.md)**. A governança de novos documentos segue **[`docs/AGENTS.md`](docs/AGENTS.md)**.

---

## 1. Princípios de Operação

- **Cérebro Único no CLI:** O Agente CLI é o orquestrador e tomador de decisão exclusivo. Não são permitidas chaves de API pagas em scripts secundários nem servidores de inferência locais pesados.
- **Automação Orgânica:** Conexão direta ao Google Chrome autenticado do usuário via `browser-use` (CDP) em daemon contínuo, preservando sessões e cookies.
- **Supervisão Humana Mandatória (*Human-in-the-Loop*):** Qualquer ação irreversível ou crítica (2FA/SMS, envio final de candidaturas, dúvidas em knockout questions) exige autorização prévia do usuário.

---

## 2. Roteador de Contexto (Regras e Decisões)

Antes de executar automações ou criar arquivos, consulte os contextos específicos:

### 📌 Regras Operacionais ([`docs/rules/operational-rules.md`](docs/rules/operational-rules.md))
- **[`RULE-001`](docs/rules/operational-rules.md#rule-001-supervisao-humana-mandatoria-human-in-the-loop-em-etapas-criticas):** Supervisão Humana Mandatória (*Human-in-the-Loop*) em Etapas Críticas.
- **[`RULE-002`](docs/rules/operational-rules.md#rule-002-arquitetura-de-cerebro-unico-sem-dependencias-externas-de-llm):** Arquitetura de Cérebro Único sem Dependências Externas de LLM.
- **[`RULE-003`](docs/rules/operational-rules.md#rule-003-execucao-de-automacao-estritamente-via-browser-harness-cdp):** Execução de Automação Estritamente via Browser Harness CDP.
- **[`RULE-004`](docs/rules/operational-rules.md#rule-004-estrutura-obrigatoria-de-dados-de-vagas-nas-tasks-do-kanban-memorycard):** Estrutura Obrigatória de Dados de Vagas nas Tasks do Kanban (Memorycard).

### 🏛️ Decisões Arquiteturais ([`docs/adr/browser-connection.md`](docs/adr/browser-connection.md))
- **[`ADR-001`](docs/adr/browser-connection.md#adr-001-conexao-com-google-chrome-via-daemon-continuo-browser-use):** Conexão com Google Chrome via Daemon Contínuo (`browser-use`).
- **[`ADR-002`](docs/adr/browser-connection.md#adr-002-inspecao-por-arvore-de-acessibilidade-ax-tree-e-cliques-por-coordenadas):** Inspeção por Árvore de Acessibilidade (AX Tree) e Cliques por Coordenadas.
- **[`ADR-003`](docs/adr/browser-connection.md#adr-003-uso-de-sessao-real-e-perfil-autenticado-do-usuario):** Uso de Sessão Real e Perfil Autenticado do Usuário.

### 🎯 Skills e Roteiros
- **[`job-application-grill`](.agents/skills/job-application-grill/SKILL.md):** Roteiro em 4 fases para alinhamento de funil, blindagem de knockout questions, extração de experiências reais e nível de autonomia.
