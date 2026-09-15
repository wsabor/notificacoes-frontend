# PEND — Planejamento do 4º Semestre (2º sem/2026)

**UC:** Programação Front-End (PEND) — 150h
**Turma:** 2-2026-SESI_DEV_OC_1
**Docente:** Wagner de Campos Sabor Junior
**Período:** 28/07/2026 a 08/12/2026 — 20 terças × 5 aulas = **100 aulas**
**Projeto integrador:** Interface React para a **API de Notificações** construída pela turma no 3º semestre

---

# 📅 CRONOGRAMA

*Atualizado conforme `PEND.xlsx`. Esta seção é a referência diária — o restante do documento é contexto de apoio.*

### Bloco 1 — Retomada e Design Responsivo (encontros 1–2 · 10 aulas)

| # | Data | Status | Conteúdo | Estratégia | Avaliação |
|---|---|---|---|---|---|
| 1 | 28/07 | ✅ | Abertura do semestre · Contrato da API e validação das 8 APIs · Design Responsivo I: viewport, unidades relativas (%, rem, em, vw/vh) | Exposição dialogada + Atividade prática | Diagnóstica: validação contra o contrato coletivo |
| 2 | 04/08 | ✅ | Design Responsivo II: media queries, mobile-first, Flexbox e Grid, breakpoints | Situação-problema | Formativa: layout fluido validado em 3 resoluções |

### Bloco 2 — UX e UI aplicados (encontros 3–4 · 10 aulas)

| # | Data | Status | Conteúdo | Estratégia | Avaliação |
|---|---|---|---|---|---|
| 3 | 11/08 | ✅ | UX: definição aplicada, diagramas, fluxos, jornada do usuário da interface de notificações | Estudo dirigido + Trabalho em grupo | Formativa: fluxograma de navegação |
| 4 | 18/08 | ✅ | UI: usabilidade, heurísticas de Nielsen, affordance/signifier/mapeamento, hierarquia visual, geometria do design, tipografia e cor · Protótipo de alta fidelidade · Mini design system | Workshop (Figma) | Formativa: protótipo navegável |

### Bloco 3 — Backend (JWT) + Frameworks / React (encontros 5–10 · 30 aulas)

| # | Data | Status | Conteúdo | Estratégia | Avaliação |
|---|---|---|---|---|---|
| 5 | 25/08 | ✅ | **Autenticação JWT no back-end** — implementação ao vivo na API de Notificações (registro, login, middleware, rota protegida) | Live coding conjunto (professor + turma) | Formativa: login retornando token válido, testado no Postman |
| 6 | 01/09 | ✅ | Frameworks: definição e tipos · Instalação (Vite) · JSX e primeiro componente · Configuração do Tailwind CSS | Exposição dialogada + Atividade prática | Formativa: repositório de front-end criado, projeto rodando e versionado |
| 7 | 08/09 | ✅ | Componentes, props e composição · Utilitários do Tailwind · Estado (`useState`), listas, formulários controlados e elevação de estado | Atividade prática + Situação-problema | Formativa: biblioteca de componentes + formulário funcionando com dados locais |
| 8 | 15/09 | ⬜ | Recapitulação ativa dos encontros 6–7 (componentes, props, composição, estado, elevação de estado, formulários controlados) até o entregável do encontro 7 estar consolidado · Em seguida: início do encontro 8 — `useEffect`, `fetch`, tratamento de erro, CORS | Recall ativo (reconstrução sem copiar) + Atividade prática | Formativa: aluno reconstrói componente + estado do zero e explica o porquê; listagem vinda da API iniciada |
| 9 | 22/09 | ⬜ | Conclusão do encontro 8 · Finalização e consolidação da 1ª parte do front-end em React — biblioteca de componentes + estado + consumo da API com dados reais funcionando ponta a ponta. **Prazo final deste bloco.** | Fechamento de bloco / Situação-problema | Checkpoint formativo (não-somativo): 1ª parte do front-end React concluída |
| 10 | 29/09 | ⬜ | **Em revisão** — Rotas (`react-router-dom`), Context API consumindo o JWT do encontro 5 e CRUD completo (conteúdo do antigo encontro 9) e/ou **Avaliação Somativa Parcial**. Escopo e formato a definir. | A definir | **Somativa — em revisão** |

### Bloco 4 — Acessibilidade (encontros 11–12 · 10 aulas)

| # | Data | Status | Conteúdo | Estratégia | Avaliação |
|---|---|---|---|---|---|
| 11 | 06/10 | ⬜ | Acessibilidade: definição, recursos, categorias de deficiência · Auditoria com Lighthouse e axe DevTools | Estudo de caso | Formativa: relatório de auditoria |
| 12 | 13/10 | ⬜ | ARIA (roles, states, properties) · Navegação por teclado · Contraste · Correção do projeto | Atividade prática | Formativa: reauditoria com nota superior |

### Bloco 5 — Web Apps / PWA (encontros 13–14 · 10 aulas)

| # | Data | Status | Conteúdo | Estratégia | Avaliação |
|---|---|---|---|---|---|
| 13 | 20/10 | ⬜ | PWA: manifest, Service Worker, Cache API, estratégias de cache, funcionamento offline | Exposição dialogada + Atividade prática | Formativa: app instalável |
| 14 | 27/10 | ⬜ | Push Notifications (Notification API + Push API) integrado à API de Notificações · Background Sync | Estratégia desafiadora | Formativa: notificação recebida com app fechado |

### Bloco 6 — Canvas e Performance (encontros 15–16 · 10 aulas)

| # | Data | Status | Conteúdo | Estratégia | Avaliação |
|---|---|---|---|---|---|
| 15 | 03/11 | ⬜ | Canvas API: contexto 2D, formas, texto · Dashboard de métricas de notificações dentro de componente React | Atividade prática | Formativa: gráfico funcional |
| 16 | 10/11 | ⬜ | Carregamento da página: `preload`, `prefetch`, `dns-prefetch` · Build de produção · Deploy (Vercel/Netlify) · Consolidação | Workshop | Formativa: aplicação publicada |

### Bloco 7 — Projeto e Encerramento (encontros 17–20 · 20 aulas)

| # | Data | Status | Conteúdo | Estratégia | Avaliação |
|---|---|---|---|---|---|
| 17 | 17/11 | ⬜ | Projeto Integrador: consolidação da aplicação e correção de pendências da rubrica | Mentoria por grupo: revisão dos critérios de responsividade, acessibilidade, web app e integração com a API | Formativa: checklist de conformidade com a rubrica |
| 18 | 24/11 | ⬜ | Projeto Integrador: refinamento da interface e documentação técnica da solução | Trabalho em grupo: README, decisões de projeto, ensaio da apresentação | Formativa: documentação técnica publicada no repositório |
| 19 | 01/12 | ⬜ | **Avaliação Somativa Final** — apresentação do projeto em banca | Situação-problema: defesa com demonstração ao vivo e justificativa das decisões técnicas | **Somativa**: níveis de desempenho |
| 20 | 08/12 | ⬜ | Encerramento — retrospectiva do semestre e panorama de carreira | Roda de feedback: autoavaliação, avaliação por pares, devolutiva individual | Formativa: autoavaliação e resumo do desempenho na UC |

---

> **Nota de sincronização (08/09):** o bloco 3 original previa "Componentes/props/composição" (encontro 6) e "Estado/formulários" (encontro 7) em dias separados. Os dois foram fundidos no encontro 7 para reabsorver uma semana extra que a inserção do encontro 5 (JWT) havia gerado — sem essa fusão, o semestre terminaria em 15/12 em vez de 08/12. Fusão viável porque a turma já tem `useState` e composição de componentes.jsx de PPDM (React Native).

> **Nota de sincronização (08/09 — após rodar o encontro 7):** o material dos encontros 6–7 foi reformulado para ficar mais didático e menos "copiar e colar", mas o encontro 7 de hoje rodou sobre a versão antiga. Para não avançar com a turma só copiando código, **15/09 abre com recapitulação ativa dos encontros 6–7** e só depois inicia o encontro 8; **22/09 passa a ser conclusão do encontro 8 + consolidação da 1ª parte do front-end React** (prazo do bloco), no lugar do conteúdo original de rotas/Context/CRUD. A alocação do conteúdo do antigo encontro 9 e o formato da Somativa Parcial de **29/09** ficam **em revisão** — decisão adiada. Os arquivos `NN-atividade-alunos.md` mantêm a numeração por *conteúdo* (Encontro 6, 7, 8, 9); as linhas do cronograma acima numeram por *data* (8ª, 9ª, 10ª terça). Os dois deixam de casar a partir daqui.

---

## Decisões de escopo

| Item | Decisão |
|---|---|
| Design Responsivo (pendência do 3º sem.) | Compacto — 2 encontros (10 aulas) |
| Framework | **React + Vite** + `react-router-dom` |
| Estilização | **Tailwind CSS v4** |
| Repositório | **Novo repositório separado** para o front-end (API permanece intocada) |
| Deploy | **1 container LXC com Nginx**, 8 builds estáticos em portas 8081–8088 + comparação com Vercel |
| Autenticação | JWT implementado ao vivo no encontro 5, consumido pelo front-end no encontro 9 |

## Restrições do contexto

| Restrição | Consequência no planejamento |
|---|---|
| **Sem tarefa de casa** — tudo é feito em sala | Todo entregável fecha antes do fim do encontro. Não há sprint fora de aula. |
| **Celular proibido em sala** | Testes por DevTools (modo responsivo) e extensão Mobile Simulator. PWA e push rodam no Chrome desktop. Celulares da escola só para demonstração final. |
| **APIs já em produção** no Proxmox (1 LXC por grupo) | Sem setup local. A API é consumida por URL desde o primeiro dia. |
| **APIs padronizadas** entre os 8 grupos | Existe um contrato único da turma. 8 front-ends distintos sobre a mesma base. |
| **Turma concluinte** (SENAI + ensino médio SESI) | Carga pesada concentrada em ago/set. Aplicação publicada até o encontro 16. |

## Capacidades técnicas a desenvolver

Conteúdos formativos que restam do plano da UC:

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

## Estratégia de deploy (encontro 16)

React + Vite gera **arquivos estáticos** — HTML, CSS e JS. Não há processo Node em execução. Isso permite servir os 8 front-ends de um único container.

**Arquitetura recomendada:**

```
LXC "frontends"  (1 container, Nginx)
  :8081 → /var/www/grupo1/dist
  :8082 → /var/www/grupo2/dist
  ...
  :8088 → /var/www/grupo8/dist

LXC "api-grupoN" (8 containers já existentes, Node + MySQL)
```

**Por que essa forma:**

| Critério | Ganho |
|---|---|
| Recursos | 1 container em vez de 8 |
| Conceito | Ensina servidor estático × servidor de aplicação |
| CORS | Origem distinta da API — o erro acontece de verdade no encontro 8 |
| Comparação com Vercel | "A Vercel faz isto, com CDN global, HTTPS e domínio automáticos" |

**Portas, não subpastas.** Servir em `/grupo1/` exigiria configurar `base` no `vite.config.js` e `basename` no react-router — ruído desnecessário. Portas eliminam o problema.

**Sequência do encontro 16:** build local → deploy no Nginx da sala → deploy na Vercel → comparação lado a lado (tempo de deploy, HTTPS, domínio, cache, CI a partir do Git).

### Consumo antecipado das APIs do servidor (a partir do encontro 8)

As 8 APIs do 3º semestre já rodam no servidor da escola, uma porta por grupo (`IP_DO_SERVIDOR:8201` a `:8208`, grupo 1 ao 8). Dá para apontar o front-end para elas já no encontro 8, sem esperar o deploy do encontro 16 — assim o CORS "de verdade" (origem distinta) aparece antes, e os alunos param de depender de subir a API no notebook.

O passo a passo está em **`docs/aulas/extra-consumir-api-do-servidor.md`**: centralizar a URL base numa variável `VITE_API_URL` (arquivo `.env.local` + `src/config.js`), trocar os `fetch`, e — o ponto crítico — **habilitar o `cors` na API do 3º semestre e reimplantar o container**, já que essas APIs foram escritas antes de existir um front-end. Material opcional; usar quando a rede da sala estiver disponível.

## Alertas de planejamento

**1. Critérios de avaliação do Plano de Ensino estão errados.**
As seções **C** e **G** do plano de referência (elaborado pelo Prof. Irineu) listam capacidades de outra UC — "levantamento de necessidades do cliente", "requisitos funcionais e não funcionais", "práticas ágeis", "Design Thinking", "ferramentas de metodologias ágeis". Nenhuma pertence a PEND. Precisam ser substituídas pelas capacidades técnicas reais da UC antes da somativa de 29/09. *(Decisão registrada: não editar o documento original — criar um documento próprio à parte quando houver tempo.)*

**2. Feriados.** Nenhuma terça do período cai em feriado nacional (07/09, 12/10 e 02/11 são segundas; 20/11 é sexta; 25/12 é sexta). As 20 datas estão íntegras — confirmar apenas SAEP, semana de provas do SESI e formatura.

**3. ENEM.** Cai nos dois primeiros domingos de novembro, sobre os encontros 15 e 16. Por isso os tópicos mais leves e visuais (Canvas, performance) ficaram ali, e a carga técnica pesada foi concentrada em agosto e setembro.

**4. Turma concluinte.** Os alunos finalizam SENAI e ensino médio no SESI simultaneamente. Prever queda de disponibilidade a partir de novembro — o encontro 16 deve deixar a aplicação **publicada e funcional**, para que os encontros 17–20 sejam refinamento, não construção. Os encontros 19 e 20 já são deliberadamente leves (banca de apresentação e roda de feedback, sem conteúdo técnico novo).

**5. Tailwind CSS v4.** A configuração mudou da v3 (`tailwind.config.js`, `npx tailwindcss init -p`) para um plugin do Vite + bloco `@theme` no CSS. Os materiais do encontro 6 já refletem a v4 — atenção redobrada se algum material futuro for adaptado de tutorial antigo.
