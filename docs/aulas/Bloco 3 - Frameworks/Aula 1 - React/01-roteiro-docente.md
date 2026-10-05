# Roteiro Docente — Encontro 6 · 01/09/2026

**UC:** PEND — Programação Front-End
**Turma:** 2-2026-SESI_DEV_OC_1 · 5 aulas
**Tema:** Frameworks, Vite, JSX, Componentes/Props/Composição, Tailwind

---

## Por que este encontro carrega mais conteúdo que o normal

Este dia junta o que originalmente seriam dois encontros. A compressão só é segura porque a turma já conhece JSX, componentes, props e `useState` do React Native (PPDM, com o Irineu). Hoje é **tradução de vocabulário**, não conceito novo — reforce isso explicitamente na abertura, é o que justifica o ritmo.

---

## Objetivos do encontro

Ao final, o aluno deve ser capaz de:

1. Explicar a diferença entre biblioteca e framework, e onde o React se encaixa.
2. Criar e rodar um projeto React com Vite.
3. Escrever JSX e entender em que difere da sintaxe do React Native.
4. Criar componentes reutilizáveis com props, aplicando o design system definido no Figma.
5. Configurar e usar Tailwind CSS.

**Capacidades mobilizadas**
`CT` Desenvolver interfaces web utilizando frameworks
`CS 1` Demonstrar autogestão

---

## Antes da aula (preparação sua)

- [ ] Ter a URL de criação de repositório do GitHub pronta para compartilhar.
- [ ] Ter o Figma do encontro 4 de um grupo de referência aberto, para projetar durante a demonstração.
- [ ] Revisar o `01-guia-branches-pull-requests.md` — hoje é a primeira vez que o fluxo de branch/PR roda de verdade, em cima de um repositório novo.

---

## Aula 1 — Frameworks e o repositório do front-end (50 min)

**Estratégia:** Exposição dialogada + Atividade prática

### Biblioteca x Framework (15 min)

**Biblioteca**: você chama o código dela quando precisa (`import algo from 'biblioteca'`, você decide quando usar).
**Framework**: ele chama o seu código (você escreve componentes, o React decide quando renderizar).

React é oficialmente uma biblioteca, mas ocupa o papel de framework na prática do mercado — por isso o conteúdo formativo trata os dois como sinônimos aqui. Cite rapidamente outros frameworks/bibliotecas do mercado (Vue, Angular, Svelte) só para dar panorama — não é o foco do dia.

> **Ponte com React Native:** eles já usam React (a biblioteca) todo dia em PPDM. A diferença de hoje não é o React — é o **destino** do que ele renderiza: HTML/DOM no navegador, em vez de componentes nativos do celular.

### Criando o repositório de front-end (35 min)

Hoje é o primeiro dia em que o front-end de cada grupo ganha existência própria — separado do repositório de backend que já existe desde o ano passado.

1. Um integrante de cada grupo cria o repositório vazio no GitHub: `<nome-do-grupo>-frontend`.
2. Adiciona os colegas como colaboradores.
3. Todo mundo clona **usando SSH** — a chave já foi configurada no encontro 5. Se alguém ainda não configurou (faltou naquele dia), use a Parte 2 do guia agora: `01-guia-branches-pull-requests.md`.

```bash
git clone git@github.com:<usuario>/<nome-do-grupo>-frontend.git
cd <nome-do-grupo>-frontend
```

4. Criar o projeto com Vite **dentro** da pasta clonada:

```bash
npm create vite@latest . -- --template react
npm install
npm run dev
```

> O `.` depois de `vite@latest` cria o projeto na pasta atual, em vez de criar uma subpasta nova — importante porque a pasta já é o repositório clonado.

5. Primeiro commit, na `main`, só desta vez (é o scaffold inicial, ainda não é código de ninguém especificamente):

```bash
git add .
git commit -m "scaffold inicial do projeto Vite + React"
git push origin main
```

A partir do **próximo** commit, o fluxo de branch por aluno começa a valer — ver Aula 3.

---

## Aula 2 — JSX e configuração do Tailwind (50 min)

**Estratégia:** Exposição dialogada + Demonstração

### JSX: o que muda vindo do React Native (25 min)

Mostre lado a lado:

| React Native | React (web) |
|---|---|
| `<View>` | `<div>` |
| `<Text>` | `<p>`, `<span>`, `<h1>`... |
| `<TouchableOpacity onPress={...}>` | `<button onClick={...}>` |
| `StyleSheet.create({...})` | classes CSS (hoje: Tailwind) |
| `<Image source={...}>` | `<img src={...}>` |

A lógica de JSX é **idêntica**: chaves para JavaScript dentro do markup, um único elemento raiz por retorno, `className` em vez de `class` (igual já era `style` como objeto em RN). O que muda é só o vocabulário de tags, porque o destino é o navegador, não um componente nativo.

### Configurando o Tailwind — v4 (25 min)

```bash
npm install tailwindcss @tailwindcss/vite
```

Não é preciso instalar PostCSS nem Autoprefixer separadamente — a v4 já inclui os dois.

Em `vite.config.js`, adicionar o plugin do Tailwind:

```js
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'
import tailwindcss from '@tailwindcss/vite'

export default defineConfig({
  plugins: [react(), tailwindcss()],
})
```

Em `src/index.css`, substituir todo o conteúdo por uma única linha:

```css
@import "tailwindcss";
```

Testar: aplicar `className="text-3xl font-bold text-teal-800"` em qualquer elemento e confirmar que o estilo aparece.

> **Ponte com Figma:** na v4, cores customizadas não vão mais num arquivo `tailwind.config.js` — entram direto no CSS, dentro de um bloco `@theme`:
> ```css
> @import "tailwindcss";
>
> @theme {
>   --color-marca: #0F4D46;
>   --color-destaque: #FF4A26;
> }
> ```
> O uso nas classes continua igual (`bg-marca`, `text-destaque`) — só a configuração migrou de JavaScript para CSS. Se algum tutorial ou vídeo que os alunos encontrarem por conta própria mostrar `tailwind.config.js` com `theme.extend.colors`, é conteúdo da v3 — ainda funciona em projetos antigos, mas não é o fluxo daqui em diante.

---

## Aulas 3 a 5 — Situação-problema: componentizando o design system (150 min)

**Estratégia:** Situação-problema

### Contexto

Cada grupo tem, do encontro 4, um mini design system no Figma: paleta, escala tipográfica, e três componentes (botão, cartão de notificação, chip de filtro). Hoje esses componentes saem do Figma e viram código React de verdade.

### Fluxo de trabalho (branch/PR)

A partir de agora, todo trabalho segue o guia (`01-guia-branches-pull-requests.md`):

1. Cada aluno cria a própria branch (`git checkout -b nome-01-09`).
2. Trabalha, commita, dá `push` na própria branch.
3. Nos últimos 20 minutos, o grupo compara as versões e escolhe uma para virar Pull Request.
4. Todo mundo atualiza a `main` local antes de sair.

### Componentes a construir (60 min)

**`Button.jsx`** — aceita `children` e uma prop `variant` (`"primario"` ou `"destaque"`):

```jsx
function Button({ children, variant = "primario", onClick }) {
  const estilos = {
    primario: "bg-marca text-white",
    destaque: "bg-destaque text-white",
  };

  return (
    <button
      onClick={onClick}
      className={`px-4 py-2 rounded-lg font-semibold ${estilos[variant]}`}
    >
      {children}
    </button>
  );
}

export default Button;
```

**`NotificationCard.jsx`** — recebe props com os dados da notificação:

```jsx
function NotificationCard({ canal, hora, titulo, texto, lida }) {
  return (
    <div className="bg-white border border-gray-200 rounded-xl p-4 mb-4">
      <div className="flex gap-2 text-xs font-mono text-gray-500 mb-2">
        <span className="bg-teal-50 text-marca px-2 py-0.5 rounded">{canal}</span>
        <span>{hora}</span>
        {!lida && <span>não lida</span>}
      </div>
      <h3 className="font-semibold text-base mb-1">{titulo}</h3>
      <p className="text-gray-600 text-sm">{texto}</p>
    </div>
  );
}

export default NotificationCard;
```

**`FilterChip.jsx`** — recebe `label`, `ativo` e `onClick`:

```jsx
function FilterChip({ label, ativo, onClick }) {
  return (
    <button
      onClick={onClick}
      className={`px-3 py-1.5 rounded-full text-sm border ${
        ativo ? "bg-marca text-white border-marca" : "bg-white text-gray-500 border-gray-200"
      }`}
    >
      {label}
    </button>
  );
}

export default FilterChip;
```

> Os valores acima (`bg-marca`, cores, espaçamentos) são exemplo — cada grupo ajusta para o próprio sistema definido no Figma. O que não muda é a estrutura: três componentes, cada um recebendo props, nenhum com dado fixo (*hardcoded*) dentro.

### Composição — juntando os três (60 min)

Em `App.jsx`, usar os três componentes juntos, com uma lista de dados de exemplo (ainda sem API — isso vem no encontro 8):

```jsx
import { useState } from "react";
import FilterChip from "./components/FilterChip";
import NotificationCard from "./components/NotificationCard";
import Button from "./components/Button";

const notificacoesExemplo = [
  { id: 1, canal: "PUSH", hora: "14:32", titulo: "Inscrição confirmada", texto: "Seu lugar está garantido.", lida: false },
  { id: 2, canal: "EMAIL", hora: "13:10", titulo: "Evento amanhã", texto: "Não esqueça o notebook.", lida: true },
];

function App() {
  const [filtro, setFiltro] = useState("todas");

  return (
    <div className="max-w-2xl mx-auto p-4">
      <h1 className="text-2xl font-bold mb-4">Central de Notificações</h1>

      <div className="flex gap-2 mb-4">
        <FilterChip label="Todas" ativo={filtro === "todas"} onClick={() => setFiltro("todas")} />
        <FilterChip label="Push" ativo={filtro === "push"} onClick={() => setFiltro("push")} />
        <FilterChip label="E-mail" ativo={filtro === "email"} onClick={() => setFiltro("email")} />
      </div>

      {notificacoesExemplo.map((n) => (
        <NotificationCard key={n.id} {...n} />
      ))}

      <Button variant="destaque">Enviar notificação de teste</Button>
    </div>
  );
}

export default App;
```

> **Ponte com heurísticas (encontro 4):** o `FilterChip` já resolve "reconhecimento em vez de memorização" — o estado `ativo` muda a cor, então o usuário vê qual filtro está selecionado sem precisar lembrar.

### Validação cruzada e Pull Request (30 min)

Grupos comparam as versões nas próprias branches, escolhem uma, e seguem o Passo 6 do guia (Pull Request → revisão → merge). Todo mundo atualiza a `main` antes de sair.

---

## O que observar circulando

| Sinal | Ação |
|---|---|
| Dado fixo (*hardcoded*) dentro do componente, em vez de vir por prop | "Se amanhã vier outra notificação diferente, esse componente aguenta sem editar o código?" |
| Cor do Tailwind padrão (`bg-blue-500`) em vez da cor customizada do grupo | "Isso é a cor de qualquer projeto Tailwind do mundo. Cadê a identidade de vocês?" |
| Aluno commitando direto na `main` | Reforçar o fluxo de branch — hoje é o primeiro dia valendo de verdade |
| Grupo com sintaxe de React Native "vazando" (`onPress`, `<View>`) | Normal no início — corrigir com a tabela de comparação da Aula 2 |

---

## Fechamento em sala (últimos 15 min)

Sem tarefa de casa. Antes de liberar, cada grupo precisa ter:

- [ ] Repositório de front-end criado e clonado por todos via SSH
- [ ] Projeto Vite + React + Tailwind rodando (`npm run dev`)
- [ ] Cores do design system aplicadas no `tailwind.config.js`
- [ ] Três componentes construídos: `Button`, `NotificationCard`, `FilterChip`
- [ ] Composição funcionando em `App.jsx`, com filtro clicável mudando de estado
- [ ] Pull Request de uma branch escolhida, revisado e mergeado na `main`
- [ ] Todos atualizaram a `main` local antes de sair

---

## Recursos

Quadro branco · Projetor · Computadores dos alunos · Node.js/Vite · Figma do encontro 4 (referência de cada grupo) · `01-guia-branches-pull-requests.md` · Repositórios de front-end dos grupos

## Ponte para o encontro 7 (08/09)

Fechar com: *"Hoje os componentes mostraram dados inventados, escritos por vocês. Semana que vem eles vão guardar o próprio estado — o que acontece quando o usuário digita ou clica muda a tela na hora, sem recarregar nada."*
