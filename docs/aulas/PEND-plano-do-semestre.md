# PEND — Planejamento do 4º Semestre (2º sem/2026)

**UC:** Programação Front-End (PEND) — 150h
**Turma:** 2-2026-SESI_DEV_OC_1
**Docente:** Wagner de Campos Sabor Junior
**Período:** 28/07/2026 a 08/12/2026 — 20 terças × 5 aulas = **100 aulas**
**Projeto integrador:** Interface React para a **API de Notificações** construída pela turma no 3º semestre

---

# 📅 CRONOGRAMA

_Atualizado conforme `PEND_2026-09-30.xlsx` (realizado até 29/09). Esta seção é a referência diária — o restante do documento é contexto de apoio._

### Bloco 1 — Retomada e Design Responsivo (encontros 1–2 · 10 aulas)

| #   | Data  | Status | Conteúdo | Estratégia | Avaliação |
| --- | ----- | ------ | -------- | ---------- | --------- |
| 1   | 28/07 | ✅ | Abertura do semestre · Contrato da API e validação das 8 APIs · Design Responsivo I: viewport, unidades relativas (%, rem, em, vw/vh, ch) | Exposição dialogada + Situação-problema | Diagnóstica: validação contra o contrato coletivo |
| 2   | 04/08 | ✅ | Design Responsivo II: media queries, breakpoints, mobile-first, Flexbox e Grid · Demonstração do produto-alvo · Atividade extra: mobile-first na prática | Situação-problema | Formativa: layout validado em 360, 768 e 1280 px |

### Bloco 2 — UX e UI aplicados (encontros 3–4 · 10 aulas)

| #   | Data  | Status | Conteúdo | Estratégia | Avaliação |
| --- | ----- | ------ | -------- | ---------- | --------- |
| 3   | 11/08 | ✅ | UX: aplicação, diagramas, fluxos, jornada do usuário e arquitetura da informação da central de notificações | Estudo dirigido + Trabalho em grupo | Formativa: fluxograma de navegação e wireframe |
| 4   | 18/08 | ✅ | UI: affordance/signifier/mapeamento, heurísticas de Nielsen, hierarquia visual, geometria do design, tipografia e cor · Mini design system · Ícones (Font Awesome) e ilustrações (Undraw) | Workshop (Figma) | Formativa: protótipo navegável |

### Bloco 3 — Backend (JWT) + Frameworks / React (encontros 5–11 · 35 aulas)

| #   | Data  | Status | Conteúdo | Estratégia | Avaliação |
| --- | ----- | ------ | -------- | ---------- | --------- |
| 5   | 25/08 | ✅ | **Extra — Autenticação JWT no back-end**: bcrypt, registro, login, middleware, rota protegida | Live coding conjunto | Formativa: teste triplo no Postman |
| 6   | 01/09 | ✅ | Frameworks: definição e tipos · Vite · JSX e primeiro componente · Tailwind CSS v4 · Repositório de front-end | Exposição dialogada + Atividade prática | Formativa: projeto React rodando e versionado |
| 7   | 08/09 | ✅ | Componentes, props e composição · Utilitários do Tailwind · `useState`, listas, formulários controlados, elevação de estado | Atividade prática + Situação-problema | Formativa: componentes e formulário com dados locais |
| 8   | 15/09 | ✅ | *Continuação do encontro 7* — conclusão dos componentes, listagem com filtros e formulário | Situação-problema | Formativa: Pull Request mergeado |
| 9   | 22/09 | ✅ | `useEffect` · Consumo da API com `fetch`, estados de carregamento e erro · CORS | Atividade prática | Formativa: listagem vinda da API |
| 10  | 29/09 | ✅ | Rotas (`react-router-dom`) · Context API + JWT · Rotas protegidas · CRUD | Estratégia desafiadora | Formativa: fluxo ponta a ponta |
| 11  | 06/10 | ⬜ | **Avaliação Somativa I** — fechamento do CRUD e entrega da interface React integrada à API | Situação-problema | **Somativa**: níveis de desempenho |

### Bloco 4 — Acessibilidade (encontro 12 · 5 aulas)

| #   | Data  | Status | Conteúdo | Estratégia | Avaliação |
| --- | ----- | ------ | -------- | ---------- | --------- |
| 12  | 13/10 | ⬜ | Acessibilidade: definição, recursos, categorias, WCAG · ARIA (roles, states, properties) · HTML semântico, teclado, foco e contraste | Estudo de caso + Atividade prática | Formativa: auditoria → correção → reauditoria |

### Bloco 5 — Web Apps / PWA (encontros 13–14 · 10 aulas)

| #   | Data  | Status | Conteúdo | Estratégia | Avaliação |
| --- | ----- | ------ | -------- | ---------- | --------- |
| 13  | 20/10 | ⬜ | PWA: manifest, Service Worker, Cache API, estratégias de cache, funcionamento offline | Exposição dialogada + Atividade prática | Formativa: app instalável e operante offline |
| 14  | 27/10 | ⬜ | Notificações: Notification API, permissões, notificação via Service Worker · Push API e Background Sync (conceito + demonstração do professor) | Atividade prática + Demonstração | Formativa: notificação do sistema a partir de evento da aplicação |

### Bloco 6 — Canvas e Performance (encontros 15–16 · 10 aulas)

| #   | Data  | Status | Conteúdo | Estratégia | Avaliação |
| --- | ----- | ------ | -------- | ---------- | --------- |
| 15  | 03/11 | ⬜ | Canvas API: contexto 2D, coordenadas, formas e texto · Painel de métricas dentro de componente React | Atividade prática | Formativa: gráfico funcional e responsivo |
| 16  | 10/11 | ⬜ | Carregamento: `preload`, `prefetch`, `dns-prefetch` · Build de produção · Deploy no Nginx da sala e na Vercel | Workshop | Formativa: aplicação publicada nos dois ambientes |

### Bloco 7 — Projeto e Encerramento (encontros 17–20 · 20 aulas)

| #   | Data  | Status | Conteúdo | Estratégia | Avaliação |
| --- | ----- | ------ | -------- | ---------- | --------- |
| 17  | 17/11 | ⬜ | Projeto Integrador: consolidação e correção de pendências da rubrica | Mentoria por grupo | Formativa: checklist de conformidade |
| 18  | 24/11 | ⬜ | Projeto Integrador: refinamento, README e ensaio da apresentação | Trabalho em grupo | Formativa: documentação no repositório |
| 19  | 01/12 | ⬜ | **Avaliação Somativa II** — apresentação do projeto em banca | Situação-problema: defesa com demonstração ao vivo | **Somativa**: níveis de desempenho |
| 20  | 08/12 | ⬜ | Encerramento leve — retrospectiva do semestre e panorama de carreira | Roda de feedback, autoavaliação e devolutiva individual | Formativa: autoavaliação |

---

> **Nota de replanejamento (30/09):** a fusão de "componentes/props" e "estado/formulários" em um único encontro (08/09) não coube e se estendeu a 15/09. Com isso, `useEffect`/`fetch` e Rotas/Context/CRUD foram realizados uma semana depois do previsto (22/09 e 29/09), e a somativa parcial de 29/09 foi retirada. Para recompor: a **Somativa I passa para 06/10**, avaliando o produto do Bloco 3 com o fechamento do CRUD em sala, e a **Acessibilidade foi condensada em um único encontro (13/10)**. De 20/10 em diante, as datas originais estão preservadas. O Push de servidor (chaves VAPID, `web-push`, assinatura persistida) virou demonstração do professor, sem entrega dos grupos.

> **A partir de 17/11 não há conteúdo técnico novo.** Os encontros 17 e 18 são trabalho no projeto; 19 é a banca; 20 é deliberadamente leve.

---

## Decisões de escopo

| Item | Decisão |
| ---- | ------- |
| Design Responsivo (pendência do 3º sem.) | Compacto — 2 encontros (10 aulas) |
| Framework | **React + Vite** + `react-router-dom` |
| Estilização | **Tailwind CSS v4** (plugin do Vite + `@theme` no CSS) |
| Repositório | **Repositório separado** para o front-end; fluxo com branch individual e Pull Request escolhido pelo grupo |
| Deploy | **1 container LXC com Nginx**, 8 builds estáticos nas portas 8081–8088 + comparação com Vercel |
| Autenticação | JWT implementado ao vivo em 25/08 e consumido pelo front-end em 29/09 |
| Somativas | **I** em 06/10 (produto do Bloco 3) · **II** em 01/12 (banca do projeto final) |
| Push Notifications | Notificação via Service Worker como entrega; push de servidor como demonstração |

## Restrições do contexto

| Restrição | Consequência no planejamento |
| --------- | ---------------------------- |
| **Sem tarefa de casa** — tudo é feito em sala | Todo entregável fecha antes do fim do encontro. Não há sprint fora de aula. |
| **Uso de celulares** | Celulares pessoais não entram em sala, mas os **celulares da escola podem ser usados a qualquer momento**. No dia a dia, DevTools (modo responsivo) e extensão Mobile Simulator. |
| **APIs já em produção** no Proxmox (1 LXC por grupo) | Sem setup local. A API é consumida por URL desde o primeiro dia. |
| **APIs padronizadas** entre os 8 grupos | Contrato único da turma, 8 front-ends distintos sobre a mesma base. |
| **Turma concluinte** (SENAI + ensino médio SESI) | Carga pesada concentrada até outubro. Aplicação publicada até 10/11; sem conteúdo novo a partir de 17/11. |

## Capacidades técnicas a desenvolver

- **1.6** Canvas
- **2.** Design Responsivo — definição, aplicação, media queries
- **3.** Frameworks — definição, tipos, instalação/config, bibliotecas de estilos, funcionalidades, ciclos de vida, aplicação
- **4.** Acessibilidade — definição, recursos, categorias, ARIA
- **5.** Web Apps — service worker, cache API, push notifications, background sync, carregamento (preload/prefetch/dns-prefetch)
- **6.** UX design — definição, aplicação, diagramas, fluxos
- **7.** UI design — definição, aplicação, usabilidade

## Princípio norteador

Nenhum tópico é ensinado isolado. Cada bloco é uma **camada aplicada sobre a mesma interface**:

```
Camada 1  Layout responsivo        →  a tela existe e se adapta
Camada 2  UX/UI                    →  a tela faz sentido para o usuário
Camada 3  React                    →  a tela é componentizada e consome a API
Camada 4  Acessibilidade           →  a tela serve para todo mundo
Camada 5  PWA                      →  a tela funciona offline e notifica
Camada 6  Canvas + performance     →  a tela informa e carrega rápido
```

## Estratégia de deploy (encontro 16 · 10/11)

React + Vite gera **arquivos estáticos** — HTML, CSS e JS. Não há processo Node em execução, o que permite servir os 8 front-ends de um único container.

```
LXC "frontends"  (1 container, Nginx)
  :8081 → /var/www/grupo1/dist
  :8082 → /var/www/grupo2/dist
  ...
  :8088 → /var/www/grupo8/dist

LXC "api-grupoN" (8 containers já existentes, Node + MySQL)
```

| Critério | Ganho |
| -------- | ----- |
| Recursos | 1 container em vez de 8 |
| Conceito | Servidor estático × servidor de aplicação |
| CORS | Origem distinta da API — mesmo cenário vivido em 22/09 |
| Comparação com Vercel | "A Vercel faz isto, com CDN global, HTTPS e domínio automáticos" |

**Portas, não subpastas.** Servir em `/grupo1/` exigiria configurar `base` no `vite.config.js` e `basename` no react-router.

**Sequência:** build local → deploy no Nginx da sala → deploy na Vercel → comparação lado a lado.

## Alertas de planejamento

**1. Critérios de avaliação do Plano de Ensino.** As seções C e G do plano de referência listam capacidades de outra UC. Como agora a Somativa I é em **06/10**, os critérios corretos de PEND precisam existir antes dessa data. _(Decisão registrada: não editar o documento original — usar documento próprio.)_

**2. Ritmo do bloco React.** A compressão prevista pela experiência com React Native não se confirmou integralmente: um encontro escorreu. A Somativa I de 06/10 já inclui tempo para fechar o CRUD — se houver grupos muito atrasados, avaliar o que está funcionando em vez de estender o prazo, para não comprometer o restante do semestre.

**3. Feriados.** Nenhuma terça do período cai em feriado nacional. Confirmar apenas eventos internos (provas do SESI, formatura).

**4. ENEM.** Cai nos primeiros domingos de novembro, sobre os encontros 15 e 16 — por isso Canvas e deploy (mais visuais e práticos) ficaram ali.

**5. Turma concluinte.** A partir de 17/11 não entra conteúdo novo. A banca de 01/12 é o último compromisso formal; 08/12 é encerramento leve.

**6. Tailwind CSS v4.** Configuração via plugin do Vite e bloco `@theme` no CSS — atenção se algum material futuro for adaptado de tutorial da v3.
