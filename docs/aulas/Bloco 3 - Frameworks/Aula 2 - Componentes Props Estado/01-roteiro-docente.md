# Roteiro Docente — Encontro 7 · 08/09/2026

**UC:** PEND — Programação Front-End
**Turma:** 2-2026-SESI_DEV_OC_1 · 5 aulas
**Tema:** Componentes, Props, Composição · Estado, Listas e Formulários Controlados

---

## Por que este encontro carrega dois assuntos

Este dia absorveu o conteúdo que originalmente seria do encontro seguinte, para reabsorver a semana extra que a inserção do JWT (25/08) havia gerado. A fusão funciona porque os dois assuntos são, na prática, **a mesma tela ganhando profundidade**: primeiro ela se organiza em componentes (manhã), depois esses componentes ganham vida própria com estado (tarde). Não são dois temas emendados à força — é uma progressão natural.

---

## Objetivos do encontro

Ao final, o aluno deve ser capaz de:

1. Extrair um trecho de JSX repetido em um componente próprio, reutilizável via props.
2. Usar composição (`children`) para construir componentes genéricos que envolvem outros.
3. Gerenciar estado local (`useState`) sem mutar diretamente arrays ou objetos.
4. Construir um formulário controlado que eleva seu resultado para um componente pai.
5. Explicar "elevação de estado" com um exemplo do próprio projeto.

**Capacidades mobilizadas**
`CT` Desenvolver interfaces web interativas
`CS 2` Demonstrar pensamento analítico

---

## Antes da aula (preparação sua)

- [ ] Confirmar que todos os grupos terminaram o encontro 6 com `Button`, `NotificationCard` e `FilterChip` funcionando. Se algum grupo não terminou, ele começa hoje atrasado — considere pareá-lo com você nos primeiros minutos.
- [ ] Ter pronta a branch do dia anterior de referência para mostrar o fluxo de atualização (`git checkout main && git pull`).

---

## Aula 1 — Composição de verdade: além de props simples (50 min)

**Estratégia:** Exposição dialogada

### Recapitulação rápida de props (10 min)

Props é informação que entra de fora para dentro do componente — o componente não decide seus próprios dados, recebe de quem o usa. Peça que um aluno explique com as próprias palavras, usando o `NotificationCard` de ontem como exemplo.

### Composição via `children` (25 min)

Até agora, os componentes recebiam props "de dado" (texto, número, booleano). Composição é diferente: é passar **outro componente** — ou JSX qualquer — como conteúdo.

```jsx
function Painel({ titulo, children }) {
  return (
    <div className="bg-white border border-gray-200 rounded-xl p-4">
      <h2 className="font-semibold mb-3">{titulo}</h2>
      {children}
    </div>
  );
}

// uso:
<Painel titulo="Notificações de hoje">
  <NotificationCard {...notificacao} />
</Painel>
```

`children` é uma prop especial — tudo que fica **entre** as tags de abertura e fechamento do componente vira `children` automaticamente.

> **Ponte com React Native:** o mesmo padrão que envolve uma `<View>` com outras dentro, ou um `<ScrollView>` recebendo qualquer conteúdo como filho. É o mesmo mecanismo, sotaque diferente.

### Quando extrair um componente novo (15 min)

Regra prática para decidir: **se um trecho de JSX se repete, ou se poderia mudar de tamanho independente do resto, ele vira componente.** Exemplo do próprio projeto: a lista inteira de notificações (hoje, um `.map` solto dentro de `App.jsx`) vira um componente `NotificationList`, porque:

- Pode crescer (mais notificações) sem afetar o resto da tela.
- Pode ser testado e entendido isoladamente.
- Se amanhã precisar de paginação ou de um estado de carregamento, a mudança fica contida ali.

---

## Aula 2 — Estado, imutabilidade e o problema de mutar direto (50 min)

**Estratégia:** Exposição dialogada + Demonstração

### `useState`: recapitulação (10 min)

Já usado no encontro 6 no `FilterChip`. Hoje aprofunda: `useState` guarda um valor que, quando muda, faz o componente **renderizar de novo**. Sem `useState`, uma variável comum muda mas a tela não percebe.

### O erro mais comum: mutar em vez de substituir (25 min)

```jsx
// ERRADO — muta o array diretamente
notificacoes.push(novaNotificacao);
setNotificacoes(notificacoes);

// CERTO — cria um array novo
setNotificacoes((atual) => [...atual, novaNotificacao]);
```

Demonstre ao vivo o efeito do erro: o React às vezes **não percebe a mudança** quando o array é mutado diretamente (a referência do objeto continua a mesma), e a tela não atualiza — um bug silencioso, difícil de debugar sem entender a causa.

> **Ponte com React Native:** o mesmo problema existe lá. Se alguém já apanhou com uma `FlatList` que não atualizava depois de um `.push()`, essa é a explicação.

### Formulário controlado (15 min)

"Controlado" significa que o valor do campo vive no estado do React, não apenas no DOM:

```jsx
const [titulo, setTitulo] = useState("");

<input value={titulo} onChange={(e) => setTitulo(e.target.value)} />
```

Cada tecla digitada atualiza o estado, e o campo reflete o estado — não o contrário. Isso permite validar, limpar ou usar o valor a qualquer momento, sem precisar "ir buscar" no DOM.

---

## Aulas 3 a 5 — Situação-problema: lista viva e formulário (150 min)

**Estratégia:** Situação-problema

### Fluxo de trabalho (branch/PR)

Mesma dinâmica desde o encontro 6: `git checkout main && git pull`, criar branch do dia, trabalhar, `push`, grupo escolhe uma branch, Pull Request, todos atualizam a `main` no fim.

### Parte A — Extraindo `NotificationList` (40 min)

Refatorar o `.map` solto em `App.jsx` para um componente próprio:

```jsx
function NotificationList({ notificacoes }) {
  if (notificacoes.length === 0) {
    return <p className="text-gray-500 text-sm">Nenhuma notificação por aqui.</p>;
  }

  return (
    <div>
      {notificacoes.map((n) => (
        <NotificationCard key={n.id} {...n} />
      ))}
    </div>
  );
}

export default NotificationList;
```

> Reparem no estado vazio (`length === 0`) — é a mesma preocupação do fluxograma do encontro 3. Todo estado do fluxo precisa de representação na tela, inclusive "não tem nada aqui ainda".

### Parte B — Elevação de estado no filtro (30 min)

Extrair a barra de filtros para um componente próprio, mas o **estado continua no pai** (`App`) — só a exibição desce para o filho:

```jsx
function FilterBar({ filtroAtual, onFiltroChange }) {
  return (
    <div className="flex gap-2 mb-4">
      <FilterChip label="Todas" ativo={filtroAtual === "todas"} onClick={() => onFiltroChange("todas")} />
      <FilterChip label="Push" ativo={filtroAtual === "push"} onClick={() => onFiltroChange("push")} />
      <FilterChip label="E-mail" ativo={filtroAtual === "email"} onClick={() => onFiltroChange("email")} />
    </div>
  );
}
```

> **Isto é elevação de estado.** O `FilterBar` não decide qual filtro está ativo — ele só avisa o pai (`onFiltroChange`) que algo foi clicado. Quem decide continua sendo `App`. Pergunte: "por que não deixamos o estado dentro do `FilterBar`?" — resposta esperada: porque o `App` também precisa saber o filtro atual, para filtrar a lista antes de passar para `NotificationList`.

### Parte C — Formulário controlado que eleva o resultado (60 min)

```jsx
function NovaNotificacaoForm({ onAdicionar }) {
  const [titulo, setTitulo] = useState("");
  const [texto, setTexto] = useState("");
  const [canal, setCanal] = useState("PUSH");

  function handleSubmit(e) {
    e.preventDefault();
    if (!titulo.trim()) return;

    onAdicionar({
      id: Date.now(),
      canal,
      hora: new Date().toLocaleTimeString().slice(0, 5),
      titulo,
      texto,
      lida: false,
    });

    setTitulo("");
    setTexto("");
  }

  return (
    <form onSubmit={handleSubmit} className="flex flex-col gap-2 mb-6">
      <input
        value={titulo}
        onChange={(e) => setTitulo(e.target.value)}
        placeholder="Título da notificação"
        className="border border-gray-200 rounded-lg px-3 py-2"
      />
      <textarea
        value={texto}
        onChange={(e) => setTexto(e.target.value)}
        placeholder="Texto"
        className="border border-gray-200 rounded-lg px-3 py-2"
      />
      <Button variant="destaque">Adicionar notificação</Button>
    </form>
  );
}
```

Em `App.jsx`, a função que recebe a nova notificação e a acrescenta ao estado:

```jsx
function adicionarNotificacao(nova) {
  setNotificacoes((atual) => [nova, ...atual]);
}

// <NovaNotificacaoForm onAdicionar={adicionarNotificacao} />
```

> **De novo, elevação de estado** — mas agora subindo um dado novo, não só um clique. O formulário não sabe nada sobre a lista; só entrega o resultado pronto para quem sabe.

### Validação cruzada e Pull Request (20 min)

Grupos testam: preencher o formulário, confirmar que a notificação aparece no topo da lista, sem recarregar a página. Depois, fluxo normal de branch/PR.

---

## O que observar circulando

| Sinal | Ação |
|---|---|
| Grupo mutando o array direto (`notificacoes.push(...)`) | Mostrar o bug ao vivo: a tela não atualiza. Corrigir com spread. |
| Estado do filtro duplicado (um no `FilterBar`, outro no `App`) | "Se existem dois estados controlando a mesma coisa, qual manda?" — o certo é um só, no pai. |
| Formulário sem `e.preventDefault()` | Página recarrega ao enviar — sintoma clássico, fácil de reconhecer |
| Grupo terminando cedo | Desafio extra: adicionar um botão "excluir" em `NotificationCard`, propagando outro callback elevado até `App` |

---

## Fechamento em sala (últimos 15 min)

Sem tarefa de casa. Antes de liberar, cada grupo precisa ter:

- [ ] `NotificationList` extraído como componente próprio, com estado vazio tratado
- [ ] `FilterBar` extraído, com estado do filtro permanecendo em `App`
- [ ] Formulário controlado adicionando notificações à lista, sem mutar o array diretamente
- [ ] Testado: adicionar notificação aparece no topo sem recarregar a página
- [ ] Pull Request revisado e mergeado
- [ ] Todos atualizaram a `main` local antes de sair

---

## Recursos

Quadro branco · Projetor · Computadores dos alunos · `01-guia-branches-pull-requests.md` · Repositórios de front-end dos grupos

## Ponte para o encontro 8 (15/09)

Fechar com: *"Hoje toda notificação nasceu de vocês, digitada no formulário. Semana que vem ela vai nascer de fora — a lista inteira vai vir da API de verdade, com `fetch`."*
