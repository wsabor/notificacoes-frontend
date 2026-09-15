# Correção — o que arrumar antes de rodar a Aula 7

Mapa do que precisa ser arrumado, na ordem em que faz sentido explicar para os
alunos. Separado em **"está quebrado"**, **"está faltando montar"** e
**"buraco na apostila"**.

---

## 1. Componentes que já existem mas estão quebrados

Esses dois arquivos foram criados a partir dos trechos da apostila — e os
trechos da apostila estavam incompletos. **Já corrigidos no repositório**;
ficam aqui como roteiro para explicar o porquê aos alunos.

### `src/components/FilterBar.jsx`

Faltavam **duas linhas**:

- No topo: `import FilterChip from "./FilterChip";` — o componente usa
  `<FilterChip>` mas nunca importava.
- No fim: `export default FilterBar;` — sem isso, nenhum outro arquivo consegue
  importar ele.

> A apostila mostrava o `FilterBar` **sem** o import e **sem** o export. **Já
> corrigido** em `02-atividade-alunos.md` — Passo 2, linhas 108–122 (`import
> FilterChip` na linha 109, `export default FilterBar` na linha 121).

### `src/components/NotificationList.jsx`

Faltava **uma linha**:

- No topo: `import NotificationCard from "./NotificationCard";` — usa
  `<NotificationCard>` no `.map` sem importar.

> A apostila (Passo 1) tinha o mesmo problema. **Já corrigido** em
> `02-atividade-alunos.md` — Passo 1, linha 83 (`import NotificationCard` no
> topo do snippet). Esse é o momento didático: "todo arquivo que usa um componente
> precisa importar esse componente — o `App.jsx` de vocês já fazia isso, aqui
> não é diferente".

---

## 2. O `App.jsx` — o que ainda está errado

O `src/App.jsx` já começou a ser refatorado, mas está com erros que impedem de
rodar. A Aula 7 só mostrava fragmentos soltos — o aluno tinha que adivinhar como
encaixar. Agora a apostila tem o `App.jsx` completo montado
(`02-atividade-alunos.md`, Passo 4 — "Juntando tudo em `App.jsx`", linha 202).
Use esse bloco como referência ao arrumar os pontos abaixo.

### 2a. Hook e função no lugar errado — `App.jsx` linhas 7 e 9–11

```jsx
const [notificacoes, setNotificacoes] = useState([...]);   // linha 7

function adicionarNotificacao(nova) {                       // linhas 9–11
  setNotificacoes((atual) => [nova, ...atual]);
}
```

Os dois estão **fora** da função `App`, no nível do arquivo. `useState` só pode
ser chamado dentro de um componente, e `adicionarNotificacao` usa `setNotificacoes`,
então precisa enxergar esse estado. Os dois têm que ir para **dentro** de `App`,
junto do `const [filtro, setFiltro]` da linha 14. Ver detalhes no item "erro da
linha 7" abaixo.

### 2b. Lista inicial `[...]` — `App.jsx` linha 7

`[...]` era um placeholder da apostila, não é código válido. Trocar pela lista de
notificações de exemplo da Aula 6 (o array de objetos com `id`, `canal`, `hora`,
`titulo`, `texto`, `lida`) — declarada como `const notificacoesIniciais = [...]`
fora do componente (dado puro pode ficar no nível do arquivo) e passada em
`useState(notificacoesIniciais)`.

### 2c. `div` sobrando em volta do `<FilterBar>` — `App.jsx` linhas 20–22

```jsx
<div className="flex gap-2 mb-4">
  <FilterBar filtroAtual={filtro} onFiltroChange={setFiltro} />
</div>
```

O `FilterBar` **já tem** o seu próprio `<div className="flex gap-2 mb-4">` dentro
dele. Essa `div` externa é resto do código antigo (quando os três `<FilterChip>`
ficavam soltos aqui) e pode sair — deixar só `<FilterBar ... />`.

### 2d. `notificacoes={}` vazio — `App.jsx` linha 24

```jsx
<NotificationList notificacoes={} />
```

`{}` vazio no JSX é erro ("JSX attributes must only be assigned a non-empty
expression"). Aqui vai a lista **já filtrada** — ver seção 3.

### 2e. Botão de teste sobrando — `App.jsx` linha 28

```jsx
<Button variant="destaque">Enviar notificação de teste</Button>
```

Era da Aula 6, quando não havia formulário. Agora quem cria notificação é o
`<NovaNotificacaoForm>`. Pode remover esse botão (e, com ele, o
`import Button` da linha 4, que não é mais usado direto no `App`).

---

## 3. O filtro precisa ser aplicado na lista

A Aula 7 dizia *"o `App` também precisa do filtro atual para filtrar a lista"* —
mas **não mostrava o código que filtra**. O aluno liga o `FilterBar`, clica nos
chips, vê a cor mudar… e a lista não muda. Parece bug. **Já corrigido** na
apostila: `02-atividade-alunos.md`, Passo 2b — "Aplicar o filtro na lista"
(linha 126).

Dentro do `App`, antes do `return`:

```jsx
const notificacoesVisiveis = notificacoes.filter((n) => {
  if (filtro === "todas") return true;
  if (filtro === "push")  return n.canal === "PUSH";
  if (filtro === "email") return n.canal === "EMAIL";
});
```

e passar `notificacoesVisiveis` (não `notificacoes`) para o `<NotificationList>`.

**Atenção ao detalhe que pega todo mundo:** os dados usam `canal: "PUSH"` /
`"EMAIL"` (maiúsculo) e o filtro usa `"push"` / `"email"` (minúsculo). Se
comparar direto `n.canal === filtro` não funciona nunca. É um ótimo exercício de
depuração pra mostrar aos alunos — mas é preciso decidir se resolve com
`.toLowerCase()`, com o `if` explícito acima, ou padronizando os valores.

---

## 4. Coisas menores (não são da Aula 7, mas vão aparecer)

- `src/index.css` linhas 4–5 — as cores ainda são as de exemplo (`#0f4d46`,
  `#ff4a26`) com o comentário "troquem pelas cores de vocês".
- `src/App.css` — sobrou do template do Vite, ninguém importa, pode apagar. O
  mesmo vale para `src/assets/hero.png` e `src/assets/react.svg`.
- Checklist da Aula 6 (linha 272 de `01-atividade-alunos.md`) dizia "cores
  aplicadas no `tailwind.config.js`", contradizendo a Parte 4, que explica que
  **no Tailwind v4 não existe esse arquivo** — a config é o bloco `@theme` no
  CSS. **Já corrigido**: a linha 272 agora diz "no bloco `@theme` do
  `src/index.css`".
