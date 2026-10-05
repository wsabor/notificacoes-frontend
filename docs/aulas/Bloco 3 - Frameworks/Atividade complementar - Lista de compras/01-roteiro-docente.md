# Roteiro docente — Atividade complementar, Lista de compras

**PEND — Programação Front-End** · 06/10/2026 · depois da somativa do Bloco 3

> Material do professor. Não distribuir. Os alunos recebem `02-atividade-alunos.md`. O gabarito está em `03-gabarito-referencia.md`.

---

## Objetivo e tempo

Fixar os fundamentos do React com uma página pequena, individual e sem novidades: componente com props, `useState` e lista com `.map`/`key`. A atividade fecha o Bloco 3, então o tom é de consolidação, não de avaliação nova.

Tempo sugerido: 45 a 50 minutos. Quem terminar antes faz o bônus. Quem não terminar não perde nada além da nota dos passos que faltaram.

## Antes da aula

- Testar o passo 1 num computador de teste: `npm create vite@latest`, instalação do Tailwind, `vite.config.js`, commit e **Publish to GitHub**. Se o login do GitHub der problema no VSCode da escola, o plano B é criar o repositório pelo site e fazer `git remote add` manualmente.
- Confirmar que o VSCode dos alunos tem a extensão de Source Control ativa (vem por padrão, mas vale checar).
- Ter o gabarito à mão, para comparar telas.

## Como acompanhar

Circule pela sala depois dos passos 3 e 5, que são os dois que mais geram dúvida. No passo 1, quase todo problema é login do GitHub ou Tailwind sem estilo. Confira no VSCode se a pasta `node_modules` não entrou no commit, porque o `.gitignore` do Vite cobre isso.

## Erros esperados e o que responder

- **Tailwind não funciona (página sem estilo):** falta o `vite.config.js` ou o `@import "tailwindcss";` no `index.css`. Peça para conferir os dois arquivos antes de reinstalar qualquer coisa.
- **Warning de `key`:** o `.map` está sem `key`, ou com índice. Lembrem do que foi dito: um `id` único.
- **Remover apaga tudo, ou dispara sozinho:** o `onRemover` está chamando a função na renderização. Peça para encontrar o `()` que falta, da mesma forma que vimos no recap do encontro 7.
- **O aluno usa `push` e a tela não atualiza:** o mesmo erro do encontro 7. Peça para trocar por `[...atual, novo]` ou `filter`.

## Checklist de nota

Seis itens, um ponto cada, na mesma ordem dos passos. A nota é a quantidade de itens concluídos dividida por 6. Os critérios estão no fim de `02-atividade-alunos.md`.

Um aluno que faz o passo 1 completo, incluindo o repositório publicado, já garante um ponto. Não penalize a forma de publicar, desde que o repositório exista no GitHub.

## Depois da aula

Verificar os repositórios publicados no GitHub. Confirmar que o item 1 foi marcado só quando o repositório estiver visível na conta do aluno. Registrar a nota no diário de classe no formato combinado.
