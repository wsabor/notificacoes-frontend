# Guia de Referência — Tailwind CSS v4

**Material de apoio para consulta durante todo o Bloco 3 (React)**

Este guia não substitui a aula — é para consultar quando bater a dúvida "como eu escrevia isso em CSS puro?". Tudo aqui parte do que vocês já sabem do bloco de Design Responsivo (encontros 1 e 2).

---

## O que é Tailwind, em uma frase

Em vez de escrever uma classe CSS e definir suas regras em um arquivo `.css` separado, o Tailwind te dá **classes prontas, uma para cada propriedade**, que você aplica direto no HTML/JSX.

```html
<!-- CSS puro (o que vocês já sabem) -->
<div class="cartao">...</div>
<style>
  .cartao {
    padding: 1rem;
    background-color: white;
    border-radius: 0.5rem;
  }
</style>

<!-- Tailwind (mesmo resultado) -->
<div class="p-4 bg-white rounded-lg">...</div>
```

Não existe mágica nova aqui — é o mesmo CSS, só que a "classe" já vem com o valor embutido no nome, em vez de vocês inventarem um nome (`cartao`) e definirem as regras em outro lugar.

---

## Instalação (v4 + Vite)

```bash
npm install tailwindcss @tailwindcss/vite
```

Não precisa instalar PostCSS nem Autoprefixer separados — a v4 já inclui os dois.

### Onde cada coisa entra

| Arquivo | O que muda |
|---|---|
| `vite.config.js` | Adicionar o plugin `tailwindcss()` |
| `src/index.css` | Uma linha: `@import "tailwindcss";` |
| Qualquer `.jsx` | Usar `className="..."` normalmente |

```js
// vite.config.js
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'
import tailwindcss from '@tailwindcss/vite'

export default defineConfig({
  plugins: [react(), tailwindcss()],
})
```

```css
/* src/index.css */
@import "tailwindcss";
```

Depois disso, qualquer `className` no projeto já reconhece as classes do Tailwind. Não precisa reiniciar nada toda vez que usar uma classe nova — o Vite detecta sozinho.

> ⚠️ Se encontrarem tutorial ou vídeo com `tailwind.config.js` e `npx tailwindcss init -p`, é conteúdo da **v3**. O conceito é o mesmo, mas o arquivo de configuração migrou de JavaScript para CSS na v4.

---

## Tabela de conversão: CSS puro → Tailwind

Esta é a parte para colar no caderno. Vocês já escreveram cada linha da esquerda no bloco de Design Responsivo.

### Espaçamento

| CSS puro | Tailwind |
|---|---|
| `padding: 1rem;` | `p-4` |
| `padding: 0.5rem 1rem;` | `py-2 px-4` |
| `margin: 0 auto;` | `mx-auto` |
| `margin-bottom: 1.5rem;` | `mb-6` |
| `gap: 1rem;` | `gap-4` |

> A escala do Tailwind é em múltiplos de `0.25rem`. `p-4` = `4 × 0.25rem` = `1rem`. Não é number aleatório — é o grid de espaçamento que vocês já usaram no encontro 4 (UI), só que o Tailwind faz a conta para vocês.

### Tipografia

| CSS puro | Tailwind |
|---|---|
| `font-size: 0.875rem;` | `text-sm` |
| `font-size: 1.5rem;` | `text-2xl` |
| `font-weight: 700;` | `font-bold` |
| `text-align: center;` | `text-center` |
| `color: #14201E;` | `text-[#14201E]` ou uma cor customizada (ver seção própria) |

### Cores de fundo e borda

| CSS puro | Tailwind |
|---|---|
| `background-color: white;` | `bg-white` |
| `border: 1px solid #E0E2DC;` | `border border-gray-200` |
| `border-radius: 0.5rem;` | `rounded-lg` |
| `border-radius: 9999px;` | `rounded-full` |

### Flexbox — igual ao que vocês fizeram no encontro 2

| CSS puro | Tailwind |
|---|---|
| `display: flex;` | `flex` |
| `flex-direction: column;` | `flex-col` |
| `justify-content: space-between;` | `justify-between` |
| `align-items: center;` | `items-center` |
| `flex-wrap: wrap;` | `flex-wrap` |

### Grid — igual ao que vocês fizeram no encontro 2

| CSS puro | Tailwind |
|---|---|
| `display: grid;` | `grid` |
| `grid-template-columns: repeat(3, 1fr);` | `grid-cols-3` |
| `gap: 1rem;` | `gap-4` |

### Media queries — igual ao que vocês fizeram no encontro 2

Isto é o ponto mais importante da conversão. Vocês já escreveram:

```css
/* mobile-first, como vocês aprenderam */
.lista {
  grid-template-columns: 1fr;
}

@media (min-width: 768px) {
  .lista {
    grid-template-columns: repeat(2, 1fr);
  }
}

@media (min-width: 1024px) {
  .lista {
    grid-template-columns: repeat(3, 1fr);
  }
}
```

Em Tailwind, cada breakpoint vira um **prefixo** antes da classe:

```html
<div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3">
```

| Prefixo | Equivale a | Breakpoint |
|---|---|---|
| (nenhum) | sem media query | base — mobile |
| `sm:` | `@media (min-width: 640px)` | — |
| `md:` | `@media (min-width: 768px)` | igual ao que vocês usaram |
| `lg:` | `@media (min-width: 1024px)` | igual ao que vocês usaram |
| `xl:` | `@media (min-width: 1280px)` | — |

**É mobile-first, exatamente como vocês aprenderam.** A classe sem prefixo vale para todas as telas; cada prefixo *acrescenta* uma regra a partir daquela largura — nunca o contrário.

### Estados (hover, foco) — algo que CSS puro faz com `:hover`

| CSS puro | Tailwind |
|---|---|
| `.botao:hover { background: #0a3b36; }` | `hover:bg-teal-900` |
| `.campo:focus { outline: 2px solid; }` | `focus:outline-2` |

---

## Cores customizadas do projeto

Cada grupo tem sua paleta, definida no Figma no encontro 4. Para usar essas cores no Tailwind, elas entram em `src/index.css`, dentro de um bloco `@theme`:

```css
@import "tailwindcss";

@theme {
  --color-marca: #0F4D46;
  --color-destaque: #FF4A26;
}
```

A partir daí, usem normalmente:

```html
<button class="bg-marca text-white">Confirmar</button>
<button class="bg-destaque text-white">Excluir</button>
```

> Troquem `marca` e `destaque` pelos nomes e cores reais do sistema de cada grupo. O que importa é: **nunca usem as cores padrão do Tailwind (`bg-blue-500`, `bg-red-500`) em elementos que já têm identidade definida no design system de vocês.**

---

## Perguntas que vão aparecer

**"Isso não deixa o HTML poluído, cheio de classe?"**
Sim, é a crítica mais comum ao Tailwind. A troca é: menos arquivos CSS para gerenciar, menos risco de uma classe genérica (`.card`) ser usada em dois lugares com efeitos diferentes sem querer. Cada projeto (e cada empresa) tem opinião sobre esse trade-off — não existe resposta certa.

**"Preciso decorar todas as classes?"**
Não. A [documentação oficial](https://tailwindcss.com/docs) tem busca. No começo, vocês vão consultar toda hora — isso é normal, não é sinal de estar indo mal.

**"Dá para misturar Tailwind com CSS normal?"**
Dá, e às vezes é necessário (uma animação complexa, por exemplo). Mas para tudo que a tabela acima cobre, usem Tailwind — é o padrão combinado para o projeto.

---

## Resumo de uma linha

Tailwind não é uma linguagem nova. É o CSS que vocês já sabem, com nomes de classe que já vêm com o valor dentro — e prefixos de breakpoint no lugar de `@media`.
