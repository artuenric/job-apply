# Índice da Documentação (`docs/index.md`)

Este documento é o sumário central de toda a documentação técnica contida na pasta `docs/`. Ele cataloga os padrões de governança, decisões arquiteturais e regras operacionais do projeto, organizados conforme o padrão estabelecido em [`AGENTS.md`](AGENTS.md).

---

## 1. Governança Documental

- **[`AGENTS.md`](AGENTS.md) — Padrão de Organização e Governança Documental:**
  - Diretrizes para criação e manutenção de documentos técnicos.
  - Estrutura hierárquica: Pastas -> Arquivos de Contexto -> Regras/Decisões.
  - Template obrigatório para itens registrados (ID único, contexto/motivação, diretriz, impacto).

---

## 2. Decisões Arquiteturais (`adr/`)

Registros formais de decisões técnicas de arquitetura agrupados por contexto operacional:

### 📁 [`adr/browser-connection.md`](adr/browser-connection.md) — Conexão e Controle do Navegador
| ID | Título | Foco Principal | Status |
| :--- | :--- | :--- | :--- |
| **`ADR-001`** | Conexão com Google Chrome via Daemon Contínuo (`browser-use`) | Furtividade anti-bot, preservação de cookies e canal único CDP sem popups | Aceito |
| **`ADR-002`** | Inspeção por Árvore de Acessibilidade (AX Tree) e Cliques por Coordenadas | Automação sem injeção de scripts no DOM e resiliência a classes dinâmicas | Aceito |
| **`ADR-003`** | Uso de Sessão Real e Perfil Autenticado do Usuário | Reaproveitamento de histórico, eliminação de CAPTCHAs e dispensa de credenciais em código | Aceito |

---

## 3. Regras Operacionais (`rules/`)

Regras mandatórias de execução, restrições e limites de atuação do agente:

### 📁 [`rules/operational-rules.md`](rules/operational-rules.md) — Regras Operacionais e Diretrizes de Execução
| ID | Título | Foco Principal | Severidade |
| :--- | :--- | :--- | :--- |
| **`RULE-001`** | Supervisão Humana Mandatória (*Human-in-the-Loop*) em Etapas Críticas | Pausa mandatória para 2FA, envio final de formulários e knockout questions com dúvida | Bloqueante |
| **`RULE-002`** | Arquitetura de Cérebro Único sem Dependências Externas de LLM | Proibição de scripts com chaves de API pagas ou servidores locais (Ollama/vLLM) | Mandatória |
| **`RULE-003`** | Execução de Automação Estritamente via Browser Harness CDP | Toda ação deve ocorrer via interface `browser-use` conectada ao Chrome local | Mandatória |
| **`RULE-004`** | Estrutura Obrigatória de Dados de Vagas nas Tasks do Kanban | Uso mandatório do modelo `job` com descrição, requisitos, diferenciais e prazos | Mandatória |

---

## 4. Estrutura de Pastas e Módulos Previstos

Subpastas reservadas para expansão dentro de `docs/`:
- **`docs/browser/`**: Guias aprofundados de interação CDP, comandos de acessibilidade e estratégias de bypass anti-bot.
- **`docs/ats/`**: Regras, especificidades e padrões de preenchimento para cada plataforma (Gupy, Greenhouse, Lever, Workday).
