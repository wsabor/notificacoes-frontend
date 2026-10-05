# Mini-fix — o formulário tem um bug escondido

**PEND — Programação Front-End** · ~10-15 min, antes de começar o encontro 8

> Isto não é aula nova. É um bug que sobrou do encontro 7 — achá-lo é um exercício rápido de leitura de código antes de seguir em frente.

---

## Passo 1 — Rodem o lint

```bash
npm run lint
```

Deve aparecer algo como:

```
src/components/NovaNotificacaoForm.jsx
  7:17  error  'setCanal' is assigned a value but never used
```

## Passo 2 — Antes de mexer em qualquer coisa, respondam (sem olhar a resposta ainda)

1. O que `setCanal` deveria fazer?
2. Por que o lint diz que ele "nunca é usado", se ele está bem ali na linha 7?
3. Abram `NovaNotificacaoForm.jsx` e procurem: existe, em algum lugar do JSX, um jeito de o usuário **escolher** entre Push e E-mail? Tem `<select>`, `<input type="radio">`, alguma coisa assim?
4. Se a resposta do item 3 for "não" — toda notificação criada pelo formulário sai com `canal` valendo o quê, sempre?
5. Consequência prática: se alguém criar uma notificação pelo formulário e depois clicar no chip "E-mail", o que vai acontecer?

## Passo 3 — Corrigir

Falta um `<select>` controlado — o mesmo padrão de `value`/`onChange` que o `<input>` e o `<textarea>` já usam ali do lado, só que numa tag diferente. Acrescentem, entre o `<textarea>` e o `<Button>`:

```jsx
<select
  value={canal}
  onChange={(e) => setCanal(e.target.value)}
  className="border border-gray-200 rounded-lg px-3 py-2"
>
  <option value="PUSH">Push</option>
  <option value="EMAIL">E-mail</option>
</select>
```

## Passo 4 — Confirmar

```bash
npm run lint
```

Não deve sobrar nenhum erro. Depois, no navegador: criem uma notificação escolhendo "E-mail" no formulário, filtrem por "E-mail" no chip — ela precisa aparecer.

## Passo 5 — Commit

Segue o fluxo normal ([`guia-fluxo-git-diario.md`](../guia-fluxo-git-diario.md)):

```bash
git add .
git commit -m "corrige select de canal não controlado no formulário"
```

Pode entrar junto com o primeiro commit do encontro 8 — não precisa de branch ou PR separado só para isso.

---
---

# Gabarito — só para o professor

> Cortar daqui para baixo se for compartilhar o arquivo com a turma.

**Respostas do Passo 2:**

1. Devia trocar o valor de `canal` quando o usuário escolhesse outro canal no formulário.
2. Porque "usar" uma variável, para o linter, é ler o seu **valor** em algum lugar do código. Chamar `setCanal` só conta se algo **dispara** essa chamada — e como nenhum elemento tem `onChange={(e) => setCanal(...)}`, a função nunca é invocada; ela existe mas está sempre parada.
3. Não — o form original só tem `<input>` (título) e `<textarea>` (texto). Não existe controle nenhum para `canal`.
4. Sempre `"PUSH"` — é o valor inicial do `useState("PUSH")`, e nada nunca muda.
5. A notificação criada não aparece — ela tem `canal: "PUSH"`, e o filtro "E-mail" só mostra `n.canal === "EMAIL"`. Parece que o formulário "perdeu" a notificação, mas na real ela está lá, só que classificada errado.

**Onde isso já foi corrigido:** `02-atividade-alunos.md` desta aula (Passo 4) e `src/components/NovaNotificacaoForm.jsx` deste repositório já têm o `<select>`. Este mini-fix é para os repositórios dos **grupos**, que replicaram o bug original.
