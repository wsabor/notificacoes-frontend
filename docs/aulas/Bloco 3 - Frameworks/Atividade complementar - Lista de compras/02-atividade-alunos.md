# Atividade complementar — Lista de compras

**PEND — Programação Front-End** · individual · 45 a 50 minutos · nota pelo checklist

---

## O que vocês vão fixar

- **Componente com props:** o `ItemLista` recebe o texto de fora, pela prop `texto`.
- **Estado com `useState`:** a lista de itens fica guardada em estado. Quando ela muda, a tela atualiza sozinha.
- **Lista com `.map` e `key`:** cada item do array vira um componente na tela.

Lembrem do encontro 7: nunca mexam direto no array com `push`. Criem um array novo, como `[...atual, novo]`.

---

## Passo 1 — Criar o projeto e publicar no GitHub

1. No VSCode, crie uma pasta chamada `lista-compras` e abra essa pasta no VSCode.
2. No terminal, dentro da pasta, rode: `npm create vite@latest . -- --template react` e depois `npm install`.
3. Instale o Tailwind: `npm install tailwindcss @tailwindcss/vite`.
4. Abra `vite.config.js`, apague tudo e cole:

```js
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";
import tailwindcss from "@tailwindcss/vite";

export default defineConfig({
  plugins: [react(), tailwindcss()],
});
```

5. Abra `src/index.css`, apague tudo e deixe só esta linha: `@import "tailwindcss";`
6. Abra `src/App.jsx`, apague tudo e cole:

```jsx
function App() {
  return (
    <div className="max-w-md mx-auto p-4">
      <h1 className="text-2xl font-bold mb-4">Lista de compras</h1>
    </div>
  );
}

export default App;
```

7. No terminal, rode `npm run dev` e abra o endereço que aparecer. Deve aparecer o título "Lista de compras".
8. Abra a aba **Source Control** do VSCode. Se aparecer **Inicializar repositório**, clique nele. Depois escreva a mensagem `base: projeto Vite + React` e clique em **Commit**. (O `.gitignore` já vem pronto e deixa a pasta `node_modules` de fora.)
9. Clique em **Publish to GitHub**, escolha o nome `lista-compras` e siga o login do GitHub pelo navegador.

**Resultado esperado:** a página mostra o título, e o repositório `lista-compras` aparece na sua conta do GitHub.

---

## Passo 2 — Criar o componente `ItemLista`

1. Crie o arquivo `src/ItemLista.jsx` com este código:

```jsx
function ItemLista({ texto }) {
  return (
    <div className="border border-gray-200 rounded-lg p-3 mb-2">
      {texto}
    </div>
  );
}

export default ItemLista;
```

2. No `App.jsx`, importe o componente no topo: `import ItemLista from "./ItemLista";`
3. Ainda no `App.jsx`, logo abaixo do `<h1>`, coloque: `<ItemLista texto="Arroz" />`

**Resultado esperado:** o texto "Arroz" aparece dentro de um quadro com borda.

> Leiam com calma: `texto` chega de fora, pela prop. O `ItemLista` só mostra o que recebeu.

---

## Passo 3 — Colocar a lista em estado e renderizar com `.map`

1. No `App.jsx`, importe o `useState`: `import { useState } from "react";`
2. Apague a linha `<ItemLista texto="Arroz" />` e coloque, no começo da função `App`, o estado da lista:

```jsx
const [itens, setItens] = useState([
  { id: 1, texto: "Arroz" },
  { id: 2, texto: "Feijão" },
  { id: 3, texto: "Leite" },
]);
```

3. Logo abaixo do `<h1>`, renderize a lista:

```jsx
{itens.map((item) => (
  <ItemLista key={item.id} texto={item.texto} />
))}
```

**Resultado esperado:** três itens aparecem na tela: Arroz, Feijão e Leite.

> O `key` diz ao React quem é quem na lista. Use sempre um `id` único, e nunca um índice.

---

## Passo 4 — Adicionar item pelo campo e pelo botão

1. No `App.jsx`, dentro da função `App`, junto com o `useState` da lista, crie o estado do campo:

```jsx
const [novoItem, setNovoItem] = useState("");
```

2. Ainda dentro da função `App`, crie a função que adiciona o item:

```jsx
function adicionarItem() {
  if (!novoItem.trim()) return;
  setItens((atual) => [...atual, { id: Date.now(), texto: novoItem }]);
  setNovoItem("");
}
```

3. No JSX, logo abaixo do `<h1>` e acima da lista, coloque o campo e o botão:

```jsx
<div className="flex gap-2 mb-4">
  <input
    value={novoItem}
    onChange={(e) => setNovoItem(e.target.value)}
    placeholder="Novo item"
    className="border border-gray-200 rounded-lg px-3 py-2 flex-1"
  />
  <button
    onClick={adicionarItem}
    className="bg-teal-700 text-white rounded-lg px-4 py-2"
  >
    Adicionar
  </button>
</div>
```

**Resultado esperado:** digitem "Café", cliquem em Adicionar. O item entra no fim da lista e o campo volta a ficar vazio.

> O campo funciona do mesmo jeito que o formulário do encontro 7: o valor está no estado, e o `onChange` atualiza esse estado a cada tecla.

---

## Passo 5 — Remover item

1. Substitua o conteúdo de `src/ItemLista.jsx` por este código, que recebe a prop `onRemover` e mostra o botão:

```jsx
function ItemLista({ texto, onRemover }) {
  return (
    <div className="flex justify-between items-center border border-gray-200 rounded-lg p-3 mb-2">
      <span>{texto}</span>
      <button onClick={onRemover} className="text-red-600 text-sm">
        Remover
      </button>
    </div>
  );
}

export default ItemLista;
```

2. No `App.jsx`, dentro da função `App`, crie a função que remove:

```jsx
function removerItem(id) {
  setItens((atual) => atual.filter((item) => item.id !== id));
}
```

3. No `.map`, passe a função para cada item:

```jsx
{itens.map((item) => (
  <ItemLista
    key={item.id}
    texto={item.texto}
    onRemover={() => removerItem(item.id)}
  />
))}
```

**Resultado esperado:** clicar em Remover tira aquele item da lista, e só ele.

> Reparem na seta `() =>` dentro do `onRemover`. Sem ela, a função rodaria na hora da renderização, e não no clique.

---

## Passo 6 — Mensagem de lista vazia

1. No `App.jsx`, logo acima do `.map`, coloque:

```jsx
{itens.length === 0 && (
  <p className="text-gray-500">Sua lista está vazia.</p>
)}
```

**Resultado esperado:** remova todos os itens. A mensagem aparece.

---

## Bônus (não conta na nota)

Quem terminar antes pode fazer com que clicar no texto de um item marque como comprado, deixando-o riscado. Dica: crie uma prop `comprado` (booleana) e use uma classe `line-through` condicional, do mesmo jeito que o `FilterChip` troca as cores pela prop `ativo`.

---

## Entrega e checklist

Entreguem o link do repositório `lista-compras`, do jeito combinado com o professor. A nota é a quantidade de itens concluídos dividida por 6.

- [ ] **1.** Projeto criado no VSCode, com Tailwind, commit base feito e repositório publicado no GitHub
- [ ] **2.** `ItemLista` recebe `texto` por prop e aparece na tela
- [ ] **3.** Lista em `useState` renderizada com `.map` e `key` (três itens aparecem)
- [ ] **4.** Adicionar item pelo campo e pelo botão funciona, e o campo esvazia
- [ ] **5.** Remover tira só o item clicado
- [ ] **6.** Mensagem "Sua lista está vazia." aparece quando a lista fica vazia
