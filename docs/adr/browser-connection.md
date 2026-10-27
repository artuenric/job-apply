# Decisões de Arquitetura: Conexão e Controle do Navegador (`docs/adr/browser-connection.md`)

Este arquivo reúne as decisões de arquitetura e padrões técnicos relacionados à conexão do **Agente CLI** com o navegador e aos métodos de automação via Chrome DevTools Protocol (CDP).

---

## ADR-001: Conexão com Google Chrome via Daemon Contínuo (`browser-use`)

- **Contexto / Motivação:** Conexões de depuração disparadas pontualmente no Chrome 124+ ativam repetidamente o popup de segurança (*"Allow remote debugging?"*), causando fadiga de aprovação. Além disso, expor flags na inicialização ou portas fixas (ex: `--remote-debugging-port=9222`) facilita a detecção por anti-bot via port scan local.
- **O que faz / Diretriz:** Mantém um serviço contínuo (*daemon*) do `browser-use` em background sustentando um canal único CDP com a instância aberta do Chrome do usuário. O usuário autoriza a conexão uma única vez no início da sessão.
- **Impacto / Consequências:**
  - **Furtividade anti-bot:** O Chrome é iniciado organicamente, sem flags suspeitas no processo do SO, sem portas fixas expostas e mantendo `navigator.webdriver = false`.
  - **Preservação de sessão:** Mantém cookies, histórico e login ativo (LinkedIn, ATS, e-mail).
  - **Eficiência:** Resposta instantânea dos comandos do Agente CLI sem novas interrupções de popup.
  - **Restrição:** Requer manter o daemon ativo em segundo plano durante toda a sessão de trabalho do agente.

---

## ADR-002: Inspeção por Árvore de Acessibilidade (AX Tree) e Cliques por Coordenadas

- **Contexto / Motivação:** Seletores CSS tradicionais em plataformas dinâmicas (como LinkedIn, Greenhouse, Workday) são ofuscados e mudam frequentemente. Injeção de scripts no DOM (`document.querySelector` ou injeções JS de automação) ativa alertas e heurísticas de anti-bot.
- **O que faz / Diretriz:** O Agente CLI inspeciona o estado visual e interativo primariamente via árvore de acessibilidade nativa (`Accessibility.getFullAXTree`) e realiza interações e cliques através de coordenadas físicas reais obtidas via caixa delimitadora (`DOM.getBoxModel` -> `click_at_xy`).
- **Impacto / Consequências:**
  - **Zero injeção de scripts suspeitos:** Não executa scripts ruidosos no contexto da página.
  - **Resiliência a mudanças de layout:** Independência de classes CSS e IDs dinâmicos ou ofuscados.
  - **Eficiência:** Baixo consumo de CPU e memória em comparação a dumps completos de HTML.

---

## ADR-003: Uso de Sessão Real e Perfil Autenticado do Usuário

- **Contexto / Motivação:** Perfis anônimos, navegadores efêmeros ou sessões limpas acionam desafios imediatos de segurança (Cloudflare Turnstile, CAPTCHA e bloqueios de acesso a vagas no LinkedIn). Além disso, autenticar contas com e-mail/senha via scripts expõe credenciais sensíveis e aumenta riscos de vazamento.
- **O que faz / Diretriz:** O agente opera diretamente sobre a janela aberta do Google Chrome do usuário com seus logins e cookies já autenticados (LinkedIn, Gmail, portais ATS), aproveitando o histórico natural de navegação.
- **Impacto / Consequências:**
  - **Confiança de reputação:** Reutiliza o trust score acumulado da sessão do usuário sem disparar alertas de novo dispositivo.
  - **Segurança de credenciais:** O usuário nunca fornece senhas a scripts ou arquivos locais de configuração.
  - **Continuidade:** Formulários pré-preenchidos e contas salvas no navegador aceleram o processo de candidatura.
