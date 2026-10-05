# Recapitulação — Encontros 6 e 7

**PEND — Programação Front-End** · 15/09/2026 · abertura do encontro 8

> Componentes, props, composição, estado (`useState`), elevação de estado e formulários controlados.

---

## Por que esta recapitulação existe

Os encontros 6 e 7 introduziram muita coisa de uma vez, e parte do material que vocês seguiram estava confuso. O risco real é ter **copiado o código e ele ter funcionado** — sem entender por quê. No encontro 8 os dados passam a vir da API, e quem não firmou props, estado e o fluxo pai↔filho vai travar.

Esta atividade **não é uma re-aula**. É um teste de compreensão. A regra: se você não consegue **explicar** um pedaço, você ainda não sabe aquele pedaço — e agora é a hora barata de descobrir isso.

### Como funciona

- Partes 1 a 4: **individual**, sem consultar o código pronto do projeto (a não ser quando o enunciado mandar).
- Parte 5: **em duplas**.
- Errar aqui não conta nota. Errar aqui é o objetivo.
- Só passamos para o encontro 8 quando o **checkpoint** no fim estiver honestamente marcado.

---

## Parte 1 — Conceitos, sem olhar o código (individual · ~15 min)

Responda por escrito, com suas palavras. Frases curtas.

1. O que é uma **prop**? Quem define o valor dela — o componente que **recebe** ou o que **usa**?
2. Qual a diferença entre uma prop comum (ex.: `titulo="..."`) e a prop `children`? Como cada uma chega no componente?
3. Por que `filtro = "push"` **não** atualiza a tela, e `setFiltro("push")` **atualiza**?
4. No `setNotificacoes((atual) => [nova, ...atual])`, o que o `[...atual]` faz? O que aconteceria de errado se em vez disso você fizesse `atual.push(nova)`?
5. **Elevação de estado:** no nosso projeto, qual componente guarda o `filtro`? Por que não faz sentido o `FilterBar` guardar?
6. Num **formulário controlado**, onde "mora" o valor que o usuário digita? O que liga o `<input>` a esse valor — nos **dois** sentidos (estado → campo e campo → estado)?

---

## Parte 2 — Prever a saída (individual · ~10 min)

Para cada trecho, escreva **o que aparece na tela** (ou o que acontece) **antes** de testar. Depois teste.

### Trecho A

```jsx
function Etiqueta({ lida }) {
  return (
    <div>
      <span>recebida</span>
      {!lida && <span> · nova</span>}
    </div>
  );
}

// uso:
<Etiqueta lida={true} />
```

O que renderiza?

### Trecho B

```jsx
const itens = [
  { id: 1, nome: "Ana" },
  { id: 2, nome: "Bruno" },
];

function Lista() {
  return (
    <ul>
      {itens.map((i) => (
        <li>{i.nome}</li>
      ))}
    </ul>
  );
}
```

O que aparece na tela? Tem algo que o React vai reclamar no console? O quê, e como se resolve?

### Trecho C

```jsx
<button onClick={() => setFiltro("push")}>com seta</button>
<button onClick={setFiltro("push")}>sem seta</button>
```

Qual a diferença de comportamento entre os dois botões? Um deles quebra a página — qual, e por quê?

---

## Parte 3 — Achar o bug (individual → conferência em turma · ~15 min)

Cada trecho tem **um** problema que impede de funcionar. Diga **qual é o erro**, **por que acontece** e **como corrigir**.

### Bug 1

```jsx
// src/components/FilterBar.jsx
function FilterBar({ filtroAtual, onFiltroChange }) {
  return (
    <div className="flex gap-2 mb-4">
      <FilterChip
        label="Todas"
        ativo={filtroAtual === "todas"}
        onClick={() => onFiltroChange("todas")}
      />
    </div>
  );
}
```

### Bug 2

```jsx
function adicionarNotificacao(nova) {
  notificacoes.push(nova);
  setNotificacoes(notificacoes);
}
```

### Bug 3

```jsx
const [titulo, setTitulo] = useState("");

<input value={titulo} placeholder="Título da notificação" />
```

---

## Parte 4 — Reconstruir do zero (individual · ~25 min)

Agora sem rede. Consultar o material **não vale** — a ideia é escrever de memória e descobrir o que ainda não está firme.

### 4.1 — `FilterChip`

1. **Apague** o arquivo `src/components/FilterChip.jsx`.
2. Reescreva do zero. Requisitos:
   - Recebe as props `label`, `ativo` e `onClick`.
   - Renderiza um `<button>` com o texto `label`.
   - Ao clicar, chama `onClick`.
   - Quando `ativo` for `true`, o chip usa a cor da marca (fundo `bg-marca`, texto branco); quando `false`, fica apagado (fundo branco, texto e borda em cinza).
   - Tem `export default`.
3. Rode `npm run dev` e confirme que os chips voltaram a funcionar.

### 4.2 — `NotificationList`

Escreva `src/components/NotificationList.jsx` do zero. Requisitos:

- Recebe a prop `notificacoes` (um array).
- Se o array estiver **vazio**, renderiza só uma mensagem: "Nenhuma notificação por aqui."
- Se tiver itens, renderiza um `<NotificationCard>` para cada um, com `key`.
- Importa o que precisar; tem `export default`.

Confirme rodando: apague todas as notificações da tela e veja se a mensagem de vazio aparece.

---

## Parte 5 — Explicar ao colega (duplas · ~10 min)

Sem código na tela. Cada um narra **em voz alta** o caminho completo, citando **cada componente** e **cada prop ou função** envolvida. O colega interrompe em qualquer ponto vago ou pulado.

- **Aluno A:** o usuário clica no chip "Push". Conte tudo o que acontece até a lista mostrar só as notificações PUSH. (Dica do que não pode faltar: `onClick`, `onFiltroChange`, `setFiltro`, o estado no `App`, o `.filter`, a prop `notificacoes` da lista.)
- **Aluno B:** o usuário preenche o formulário e clica em "Adicionar". Conte tudo até o cartão novo aparecer no topo da lista. (Não pode faltar: `value`/`onChange`, `handleSubmit`, `e.preventDefault()`, `onAdicionar`, `setNotificacoes((atual) => [nova, ...atual])`.)

Depois troquem os papéis com o outro cenário.

---

## Checkpoint — pronto para o encontro 8?

Marque só o que for **honestamente** verdade:

- [ ] Reconstruí `FilterChip` e `NotificationList` **sem consultar** e funcionaram
- [ ] Sei dizer, para um par pai/filho, **o que desce** (props, funções) e **o que sobe** (eventos, dados)
- [ ] Sei explicar por que não se altera estado diretamente
- [ ] Narrei o fluxo de um clique no filtro e de um envio de formulário em voz alta, sem travar
- [ ] Sei a diferença entre `onClick={fn}` e `onClick={fn()}`

Se algum item ficou desmarcado, chame o professor **antes** de seguir.

---

## Ponte para o encontro 8

Até aqui, a lista de notificações era um array que **nós** escrevemos à mão no `App.jsx`. A partir de agora esse array some: a lista inteira vem de **fora**, da API que vocês construíram no backend.

Isso levanta perguntas que não existiam com dados locais:

- Buscar dado leva tempo — **o que a tela mostra enquanto os dados não chegaram?**
- E **se a busca falhar** (API fora do ar, sem internet)?
- **Quando** disparar a busca, sem cair num laço infinito de renderizações?

O encontro 8 responde essas três com `async`/`await`, `useEffect` e `fetch` — e apresenta o CORS, a primeira regra de segurança do navegador que vocês vão encontrar.

---
---

# Gabarito e notas para o professor

> Não distribuir esta seção junto com a atividade. Se for imprimir/compartilhar para os alunos, cortar daqui para baixo.

## Tempo e ritmo

| Parte | Tempo | Formato |
| --- | --- | --- |
| 1 — Conceitos | ~15 min resolvendo + ~10 min conferência oral | Individual → turma |
| 2 — Prever a saída | ~10 min + ~5 min conferência | Individual → turma |
| 3 — Achar o bug | ~15 min (inclui conferência) | Individual → turma |
| 4 — Reconstruir | ~25 min | Individual, professor circulando |
| 5 — Explicar ao colega | ~10 min | Duplas |
| Checkpoint + ponte | ~5 min | Turma |

Total ~2 aulas. Só iniciar o encontro 8 depois do checkpoint. Se boa parte da turma não fechar a Parte 4, vale gastar a 3ª aula terminando o recap — é o motivo de ele existir.

## Gabarito — Parte 1

1. Prop é um dado que o componente **recebe de fora**, de quem o usa. Quem define o valor é **quem usa** o componente (`<Card titulo="X" />`); o componente só lê.
2. Prop comum passa como atributo na tag e chega em `props.nome`. `children` **não** é atributo: é tudo que fica **entre** as tags de abertura e fechamento, e chega em `props.children`. Onde o componente escreve `{children}`, esse conteúdo aparece.
3. Uma variável comum não avisa o React que mudou. `filtro = "push"` muda o valor na memória, mas não dispara nova renderização — a tela continua com o valor antigo. `setFiltro(...)` muda o valor **e** agenda a re-renderização de quem depende dele.
4. `[...atual]` cria um **array novo** com os mesmos itens. Como é um objeto novo (referência nova), o React percebe a mudança e redesenha. `atual.push(nova)` altera o **mesmo** array; a referência não muda; o React compara, vê "igual" e **não redesenha** — bug silencioso.
5. Quem guarda o `filtro` é o **`App`**, porque ele é o pai comum do `FilterBar` (que precisa saber qual chip pintar) e da `NotificationList` (que precisa do filtro para mostrar a lista certa). Se o `FilterBar` guardasse, a lista não teria como saber do valor — ficariam dessincronizados.
6. O valor mora no **estado do React** (`useState`). `value={titulo}` leva o estado para o campo; `onChange={(e) => setTitulo(e.target.value)}` leva o que foi digitado de volta para o estado. Os dois juntos fecham o ciclo.

## Gabarito — Parte 2

- **A:** aparece só **"recebida"**. Com `lida={true}`, `!lida` é `false`, e `false && (...)` não renderiza nada.
- **B:** aparece a lista **"Ana"** e **"Bruno"**. O React reclama no console: *"Warning: Each child in a list should have a unique key prop"* — falta `key={i.id}` no `<li>`.
- **C:** o botão **"com seta"** funciona: no clique, chama `setFiltro("push")`. O botão **"sem seta"** quebra: `setFiltro("push")` é **executado na renderização** (o `onClick` recebe o *resultado* da chamada, não uma função). Isso chama `setFiltro` durante o render → novo render → nova chamada → erro *"Too many re-renders"*. `onClick` espera **uma função**, não o retorno de chamá-la.

## Gabarito — Parte 3

- **Bug 1:** o arquivo usa `<FilterChip>` mas não tem `import FilterChip from "./FilterChip";`, e não tem `export default FilterBar;` no fim. Sintomas: `FilterChip is not defined`, ou o `App` recebe `undefined` ao importar `FilterBar`. Corrigir adicionando as duas linhas.
- **Bug 2:** muta o array (`push`) e devolve **a mesma referência** para `setNotificacoes`. O React não detecta mudança e a tela não atualiza. Corrigir: `setNotificacoes((atual) => [nova, ...atual]);`.
- **Bug 3:** `value={titulo}` prende o campo ao estado, mas sem `onChange` o estado nunca muda — o campo fica **travado**, não dá para digitar. Corrigir adicionando `onChange={(e) => setTitulo(e.target.value)}`.

## Sinais de que a turma ainda não está pronta para o encontro 8

- Na Parte 4, recorrem ao material antigo em vez de tentar de memória.
- Na Parte 5, a narração pula direto de "clica" para "a lista muda", sem citar `setFiltro`, o estado no `App` e o `.filter`.
- Confundem "o que é estado" com "o que é prop" — tratam toda prop como se pudesse ser alterada com um `set`.
- Não sabem dizer por que `notificacoesVisiveis` **não** é `useState`.

Se dois ou mais desses aparecerem na maioria, reforce com um exemplo ao vivo antes do encontro 8 — não avance.
