# Atividade complementar 2 — Plano de estudos para o vestibular

**PEND — Programação Front-End** · individual · nota pelo checklist

---

## O desafio

Na lista de compras vocês receberam o código pronto em cada passo. Agora é diferente: **vocês escrevem o código inteiro**. Os passos abaixo dizem **o que** a página precisa fazer, não **como** escrever. O único lugar com código pronto é a renderização condicional (requisitos 5 e 6), que é a novidade desta atividade.

A página é um plano de estudos: uma lista de tópicos do vestibular. Dá para adicionar tópicos e marcar cada um como estudado. No topo, um contador mostra quanto do plano já foi estudado.

Vocês já sabem tudo o que precisa, menos a parte condicional:

- Componente que recebe dados por **props**
- Lista guardada em **`useState`**
- **`.map`** com **`key`** para mostrar a lista
- Campo controlado e evento de clique, como no formulário do encontro 7

**Regra:** não copiem o código da lista de compras. Podem olhar para lembrar como algo funciona, mas escrevam com as próprias mãos. Façam um commit ao terminar cada requisito, com uma mensagem dizendo o que foi feito.

---

## Requisito 1 — Projeto criado e publicado

1. Criem uma pasta `plano-estudos`, abram no VSCode e criem um projeto Vite com React dentro dela.
2. Instalem e configurem o Tailwind do mesmo jeito da lista de compras. **Só para esta parte**, podem consultar aquele material.
3. Deixem o `App.jsx` mostrando apenas o título "Plano de estudos — Vestibular".
4. Façam o commit de base e publiquem no GitHub pela aba **Source Control**, com o nome `plano-estudos`.

**Resultado esperado:** a página mostra o título com estilo do Tailwind, e o repositório aparece na conta de vocês no GitHub.

---

## Requisito 2 — Componente do tópico

1. Criem um componente chamado `TopicoEstudo`, num arquivo próprio.
2. Ele recebe o texto do tópico **por prop** e mostra esse texto dentro de um quadro com borda.
3. Usem o componente no `App` com um texto fixo, por exemplo "Matemática — Funções".

**Resultado esperado:** o tópico aparece na tela, dentro de um quadro.

---

## Requisito 3 — Lista de tópicos em estado

1. No `App`, guardem em **`useState`** uma lista com três tópicos. Cada tópico é um objeto com `id`, `texto` e `estudado` (verdadeiro ou falso).
2. Deixem pelo menos um tópico já com `estudado` verdadeiro. Ele vai servir para testar o requisito 5.
3. Mostrem a lista na tela usando `.map` e o componente `TopicoEstudo`. Não esqueçam a `key`.

**Resultado esperado:** três tópicos aparecem na tela. Por enquanto, os estudados e os não estudados ficam com a mesma aparência.

---

## Requisito 4 — Adicionar tópico

1. Coloquem um campo de texto e um botão **Adicionar** acima da lista.
2. O valor do campo fica num estado próprio (campo controlado).
3. Ao clicar em Adicionar, o tópico digitado entra no fim da lista, com `estudado` falso, e o campo volta a ficar vazio.
4. Se o campo estiver vazio, nada deve ser adicionado.

**Resultado esperado:** digitem "Química — Estequiometria" e cliquem em Adicionar. O tópico entra na lista e o campo esvazia.

> Lembrem: nada de `push`. Criem um array novo a partir do atual.

---

## Requisito 5 — Marcar como estudado (renderização condicional)

**Parte A: a lógica (sem código pronto).** Clicar num tópico deve inverter o `estudado` dele: o que não foi estudado passa a ser estudado, e vice-versa.

1. No `App`, criem uma função que recebe o `id` do tópico clicado.
2. Ela atualiza a lista com `.map`, criando um array novo. No tópico clicado, devolvam uma **cópia** com `estudado` invertido. Nos outros, devolvam o próprio tópico, sem mudança.
3. Passem essa função para o `TopicoEstudo` por prop, e chamem ela no clique do quadro.
4. Passem também o `estudado` de cada tópico por prop.

**Parte B: a aparência (com código para destravar).** Agora o `TopicoEstudo` precisa **aparecer diferente** conforme a prop `estudado`. Isso é renderização condicional, e tem duas formas que vocês vão usar.

A primeira forma **troca uma classe** conforme uma condição, com o ternário `? :`:

```jsx
<span className={estudado ? "line-through text-gray-400" : ""}>
  {texto}
</span>
```

Se `estudado` for verdadeiro, o texto fica riscado e cinza. Se for falso, fica sem classe extra.

A segunda forma **mostra ou esconde um elemento inteiro**, com o `&&`:

```jsx
{estudado && <span className="text-green-700 text-sm">✓ estudado</span>}
```

Se `estudado` for verdadeiro, o selo aparece. Se for falso, o React não mostra nada.

Coloquem as duas dentro do `TopicoEstudo`, no lugar onde hoje está só o texto.

**Resultado esperado:** o tópico que já começou estudado aparece riscado e com o selo "✓ estudado". Clicar em qualquer tópico liga e desliga essa aparência.

---

## Requisito 6 — Contador e mensagem final

1. No `App`, calculem quantos tópicos estão com `estudado` verdadeiro. Dica: `filter` mais `.length`. **Não** criem um estado para isso, calculem a partir da lista.
2. Mostrem acima da lista a frase "X de Y tópicos estudados", com os números de verdade.
3. Quando **todos** os tópicos estiverem estudados, mostrem uma mensagem de parabéns. Usem o `&&` do requisito 5:

```jsx
{totalEstudados === topicos.length && (
  <p className="text-green-700 font-semibold">Tudo estudado! Bora pra prova.</p>
)}
```

Adaptem os nomes `totalEstudados` e `topicos` para os nomes que vocês usaram.

**Resultado esperado:** o contador muda a cada clique. Ao marcar o último tópico, a mensagem aparece. Ao desmarcar um, ela some.

---

## Entrega e checklist

Entreguem o link do repositório `plano-estudos`, do jeito combinado com o professor. A nota é a quantidade de requisitos concluídos dividida por 6.

- [ ] **1.** Projeto criado com Tailwind, commit base feito e repositório publicado no GitHub
- [ ] **2.** `TopicoEstudo` recebe o texto por prop e aparece na tela
- [ ] **3.** Três tópicos em `useState`, mostrados com `.map` e `key`
- [ ] **4.** Adicionar tópico funciona, o campo esvazia e não entra tópico vazio
- [ ] **5.** Clicar num tópico liga e desliga o riscado e o selo "✓ estudado"
- [ ] **6.** Contador "X de Y tópicos estudados" correto, e a mensagem aparece só quando todos estão estudados
