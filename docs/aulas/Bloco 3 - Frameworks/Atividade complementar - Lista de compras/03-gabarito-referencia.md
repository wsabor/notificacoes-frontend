# Gabarito — Atividade complementar, Lista de compras

> Só para o professor. Não distribuir com a atividade.

Estado final esperado depois dos passos 1 a 6 da atividade do aluno. Use para comparar com a tela e o código de quem estiver travado.

## `src/App.jsx`

```jsx
import { useState } from "react";
import ItemLista from "./ItemLista";

function App() {
  const [itens, setItens] = useState([
    { id: 1, texto: "Arroz" },
    { id: 2, texto: "Feijão" },
    { id: 3, texto: "Leite" },
  ]);
  const [novoItem, setNovoItem] = useState("");

  function adicionarItem() {
    if (!novoItem.trim()) return;
    setItens((atual) => [...atual, { id: Date.now(), texto: novoItem }]);
    setNovoItem("");
  }

  function removerItem(id) {
    setItens((atual) => atual.filter((item) => item.id !== id));
  }

  return (
    <div className="max-w-md mx-auto p-4">
      <h1 className="text-2xl font-bold mb-4">Lista de compras</h1>

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

      {itens.length === 0 && (
        <p className="text-gray-500">Sua lista está vazia.</p>
      )}

      {itens.map((item) => (
        <ItemLista
          key={item.id}
          texto={item.texto}
          onRemover={() => removerItem(item.id)}
        />
      ))}
    </div>
  );
}

export default App;
```

## `src/ItemLista.jsx`

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

## Bônus (referência)

Uma forma possível: `ItemLista` recebe `comprado` e `onMarcar`, e o `<span>` ganha `className={comprado ? "line-through text-gray-400" : ""}` com `onClick={onMarcar}`. No `App`, o item guarda `comprado: false` e `onMarcar` faz um `.map` que devolve um objeto novo com `comprado` invertido, do mesmo jeito que o `setItens` cria um array novo.

## Erros típicos que aparecem na correção

| Sintoma | Causa provável |
| --- | --- |
| Tela em branco, erro de Tailwind ausente (sem estilos) | Faltou `vite.config.js` com o plugin, ou `src/index.css` sem `@import "tailwindcss";` |
| `Warning: Each child in a list should have a unique "key"` | `key` ausente no `.map` |
| Item removido é outro, ou a tela não atualiza | `itens.splice`/`push` em vez de `filter`/`[...]`, ou `key={index}` |
| Clicar em Remover tira todos os itens, ou dispara sozinho ao carregar | `onRemover={removerItem(item.id)}` em vez de `onRemover={() => removerItem(item.id)}` |
| Campo não digita | `value` sem o `onChange` correspondente |
| Botão Adicionar não faz nada | `onClick={adicionarItem()}` (chama na renderização) em vez de `onClick={adicionarItem}` |
| Item aparece sem texto | `ItemLista` recebe a prop com outro nome, por exemplo `item` em vez de `texto` |
