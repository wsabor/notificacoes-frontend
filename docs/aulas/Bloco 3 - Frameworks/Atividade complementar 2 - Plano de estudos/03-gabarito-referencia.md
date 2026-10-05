# Gabarito — Atividade complementar 2, Plano de estudos

> Só para o professor. Não distribuir com a atividade.

Esta é **uma** solução possível. Como os alunos escrevem o código sozinhos, nomes de variáveis, de funções e de props vão variar. Corrija pelo **comportamento** descrito no checklist, não pela semelhança com este código.

## `src/App.jsx`

```jsx
import { useState } from "react";
import TopicoEstudo from "./TopicoEstudo";

function App() {
  const [topicos, setTopicos] = useState([
    { id: 1, texto: "Matemática — Funções", estudado: false },
    { id: 2, texto: "Português — Interpretação de texto", estudado: true },
    { id: 3, texto: "Biologia — Genética", estudado: false },
  ]);
  const [novoTopico, setNovoTopico] = useState("");

  function adicionarTopico() {
    if (!novoTopico.trim()) return;
    setTopicos((atual) => [
      ...atual,
      { id: Date.now(), texto: novoTopico, estudado: false },
    ]);
    setNovoTopico("");
  }

  function alternarEstudado(id) {
    setTopicos((atual) =>
      atual.map((t) => (t.id === id ? { ...t, estudado: !t.estudado } : t)),
    );
  }

  const totalEstudados = topicos.filter((t) => t.estudado).length;

  return (
    <div className="max-w-md mx-auto p-4">
      <h1 className="text-2xl font-bold mb-4">Plano de estudos — Vestibular</h1>

      <div className="flex gap-2 mb-4">
        <input
          value={novoTopico}
          onChange={(e) => setNovoTopico(e.target.value)}
          placeholder="Novo tópico"
          className="border border-gray-200 rounded-lg px-3 py-2 flex-1"
        />
        <button
          onClick={adicionarTopico}
          className="bg-teal-700 text-white rounded-lg px-4 py-2"
        >
          Adicionar
        </button>
      </div>

      <p className="text-gray-600 mb-2">
        {totalEstudados} de {topicos.length} tópicos estudados
      </p>

      {totalEstudados === topicos.length && (
        <p className="text-green-700 font-semibold mb-2">
          Tudo estudado! Bora pra prova.
        </p>
      )}

      {topicos.map((t) => (
        <TopicoEstudo
          key={t.id}
          texto={t.texto}
          estudado={t.estudado}
          onAlternar={() => alternarEstudado(t.id)}
        />
      ))}
    </div>
  );
}

export default App;
```

## `src/TopicoEstudo.jsx`

```jsx
function TopicoEstudo({ texto, estudado, onAlternar }) {
  return (
    <div
      onClick={onAlternar}
      className="flex justify-between items-center border border-gray-200 rounded-lg p-3 mb-2 cursor-pointer"
    >
      <span className={estudado ? "line-through text-gray-400" : ""}>
        {texto}
      </span>
      {estudado && <span className="text-green-700 text-sm">✓ estudado</span>}
    </div>
  );
}

export default TopicoEstudo;
```

## Variações que valem como certas

- Usar um `<button>` para marcar, em vez de clicar no quadro inteiro.
- Usar `<form onSubmit>` com `e.preventDefault()` no lugar do `onClick` do botão Adicionar.
- Contador e mensagem dentro de um componente próprio, recebendo os números por prop.
- Texto do selo, cores ou classes diferentes, desde que a aparência mude conforme `estudado`.

## O que **não** vale como certo

- Contador guardado em `useState` e atualizado à mão. Ele sai de sincronia com a lista. É erro de conceito, mesmo que pareça funcionar nos testes rápidos. Desconte o requisito 6.
- Alternar com `topicos[i].estudado = !topicos[i].estudado` seguido de `setTopicos(topicos)`. É mutação direta, e a tela pode não atualizar. Desconte o requisito 5.
- Código idêntico ao da lista de compras com só o texto trocado, quando o requisito pedia algo diferente. Converse com o aluno antes de dar o ponto.

## Erros típicos e a pergunta que destrava

Prefira devolver uma pergunta, em vez de mostrar a linha certa:

| Sintoma | Pergunta para o aluno |
| --- | --- |
| Clicar não muda nada | "A função de alternar é chamada no clique? Coloque um `console.log` dentro dela." |
| Todos os tópicos mudam juntos | "Dentro do `.map`, como você sabe qual é o tópico clicado?" |
| A tela não atualiza ao clicar | "Você criou um array novo, ou mudou o que já existia?" |
| Alterna sozinho ao carregar a página | "Você passou a função, ou o resultado de chamar a função?" |
| Riscado aparece em todos, ou em nenhum | "O `TopicoEstudo` está recebendo `estudado` por prop? Qual valor chega?" |
| Mensagem final aparece logo no início | "O que o seu contador conta? Ele conta só os estudados?" |
