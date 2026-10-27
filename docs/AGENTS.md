# Padrão de Organização e Governança Documental (`docs/AGENTS.md`)

Este documento estabelece o padrão arquitetural e editorial para a criação, organização e manutenção de toda a documentação do projeto.

---

## 1. Princípio de Organização: Pastas -> Arquivos de Contexto -> Regras/Decisões

Para evitar fragmentação excessiva (ter um arquivo minúsculo para cada micro-regra), a documentação é organizada em três níveis hierárquicos:

1. **Pastas (Grandes Módulos e Categorias):**
   - Agrupam grandes blocos do sistema ou tipologias de documentos.
   - Exemplos:
     - `docs/adr/` (Decisões de Arquitetura).
     - `docs/browser/` (Interações CDP, automação de navegador e acessibilidade).
     - `docs/rules/` (Limitações operacionais, comandos proibidos e diretrizes de execução).
     - `docs/ats/` (Comportamento e estratégias para plataformas de vagas e ATS).

2. **Arquivos (Agrupamento por Contexto / Subárea):**
   - Cada arquivo **não** é restrito a uma única regra isolada.
   - Ele reúne regras, decisões e diretrizes de uma **mesma subárea temática ou contexto operacional**.
   - Exemplos:
     - `docs/adr/browser-connection.md` (Todas as decisões relacionadas à conexão agente-navegador).
     - `docs/adr/forbidden-patterns-and-limits.md` (Decisões sobre comandos proibidos, limitações de SO e anti-padrões).
     - `docs/rules/documentation-standards.md` (Padrões de escrita, formatação e convenções de código).

3. **Itens / Regras / Decisões (Dentro de cada arquivo):**
   - Cada decisão ou regra interna segue uma estrutura padronizada, concisa e orientada a bullet points.

---

## 2. Estrutura Obrigatória de Cada Regra / Decisão

Dentro dos arquivos de contexto, cada item registrado deve obrigatoriamente possuir:

- **ID Único:** Prefixo padronizado por contexto + numeração sequencial (ex: `ADR-001`, `RULE-001`, `LIMIT-001`).
- **Título:** Claro, direto e autoexplicativo.
- **Descrição em Bullet Points:** Concisa e objetiva, detalhando o que faz, a motivação e as implicações práticas.

### Template Padrão

```markdown
### [ID] Título da Decisão / Regra (ID é definido como NomeDoArquivo-NúmeroSequencial)
- **Contexto / Motivação:** Motivo pelo qual a regra/decisão existe.
- **O que faz / Diretriz:** Ação exata executada ou conduta mandatória.
- **Impacto / Consequências:** Benefício prático e efeitos colaterais esperados.
```

---

## 3. Exemplo Prático de Aplicação

### Exemplo em `docs/adr/browser-connection.md`:

```markdown
# Decisões de Arquitetura: Conexão e Controle do Navegador

## ADR-001: Conexão com Chrome via Daemon Contínuo de browser-use

- **Contexto:** Chrome 124+ exibe popups frequentes de depuração remota se reconectado a cada comando.
- **O que faz:** Mantém um serviço contínuo (daemon) em background sustentando canal único CDP.
- **Vantagem anti-bot:** Chrome roda sem flags suspeitas (`navigator.webdriver = false`) e sem portas fixas expostas.
- **Restrição:** Requer manter o daemon ativo durante toda a sessão de trabalho do agente.

## ADR-002: Inspeção por Árvore de Acessibilidade (AX Tree) e Cliques por Coordenadas

- **Contexto:** Seletores CSS tradicionais são voláteis e scripts pesados de injeção alertam anti-bot.
- **O que faz:** Inspeciona elementos via `Accessibility.getFullAXTree` e aciona cliques via coordenadas físicas (`DOM.getBoxModel`).
- **Impacto:** Menor overhead de CPU e zero injeção de scripts suspeitos no DOM da página.
```

---

## 4. Diretrizes para o Agente CLI ao Criar ou Atualizar Documentos

- **Agrupar antes de criar novo arquivo:** Sempre verifique se o contexto já possui um arquivo correspondente antes de criar um novo.
- **Concisão e Objetividade:** Evite parágrafos longos ou texto narrativo desnecessário; priorize bullet points densos em informação útil.
- **Rastreabilidade de IDs:** Mantenha IDs consistentes para facilitar citações cruzadas em logs, commits e respostas no chat.
- **Atualização do Sumário Central:** Toda nova pasta ou arquivo temático criado deve ser registrado em [`docs/index.md`](index.md).
