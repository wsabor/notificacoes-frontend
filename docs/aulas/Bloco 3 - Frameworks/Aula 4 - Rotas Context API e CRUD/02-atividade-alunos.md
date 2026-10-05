# Encontro 9 — Rotas, Context API e CRUD Autenticado

**PEND — Programação Front-End** · 22/09/2026

---

## O que você vai conseguir fazer ao fim de hoje

- Explicar por que uma aplicação React precisa de **rotas** mesmo tendo uma única página HTML.
- Configurar **`react-router-dom`**: várias telas, cada uma com sua URL.
- Entender o que é **prop drilling** e usar a **Context API** para evitá-lo.
- Guardar a sessão do usuário (token JWT) em Context + `localStorage`.
- Proteger uma rota: quem não está logado é mandado para o login.
- Completar o **CRUD**: criar, marcar como lida e apagar notificações, todas as escritas autenticadas com o token.

> **Recapitulando:** vocês têm uma tela que lê a lista da API (encontro 8), filtra (encontro 7) e tem um formulário controlado (encontro 7). Hoje tudo isso se conecta com o login (JWT do encontro 5) e vira um produto funcionando de ponta a ponta.

> **Se você já usou React Native:** o React Navigation resolve o mesmo problema que o `react-router-dom`; a Context API é a mesma de lá, usada do mesmo jeito para guardar sessão. Sintaxe diferente, conceitos idênticos.

---

## Parte 1 — Rotas numa Single Page Application

### O problema

O projeto de vocês tem **um** `index.html`. Quando o usuário navega, o JavaScript troca o que está na tela — a página nunca recarrega de fato. Isso é uma **SPA** (_Single Page Application_).

Só que, sem rotas, algumas coisas quebram:

- A URL fica sempre a mesma (`localhost:5173/`), então **não dá para compartilhar** um link para uma tela específica.
- O **botão voltar** do navegador não funciona como o usuário espera.
- Recarregar a página sempre leva de volta ao começo.

Rotas devolvem significado à URL: `/login` mostra o login, `/` mostra a lista — e cada uma é um endereço real.

### Instalando e configurando

```bash
npm install react-router-dom
```

Primeiro, envolva a aplicação com o `BrowserRouter`, em `main.jsx`:

```jsx
// src/main.jsx
import { BrowserRouter } from "react-router-dom";

createRoot(document.getElementById("root")).render(
  <BrowserRouter>
    <App />
  </BrowserRouter>,
);
```

Depois, declare as rotas onde antes havia só o conteúdo do `App`:

```jsx
// src/App.jsx
import { Routes, Route } from "react-router-dom";
import Login from "./pages/Login";
import Home from "./pages/Home";

function App() {
  return (
    <Routes>
      <Route path="/login" element={<Login />} />
      <Route path="/" element={<Home />} />
    </Routes>
  );
}
```

- **`<Routes>`** olha a URL atual e renderiza **a primeira `<Route>` que combina**.
- **`path`** é o endereço; **`element`** é o componente a mostrar.

### Reorganizando em `pages/`

O que estava dentro do `App.jsx` (lista, filtros, formulário, os `useState`, o `useEffect` do `fetch`) **se muda para** `src/pages/Home.jsx` — que passa a ser um componente comum. E nasce `src/pages/Login.jsx`, novo. O `App.jsx` fica pequeno: só o mapa de rotas.

### Navegando entre rotas

- **`<Link to="/login">`** — um link clicável, substitui o `<a href>` (que recarregaria a página).
- **`useNavigate()`** — para navegar **por código** (ex.: depois de um login bem-sucedido):

  ```jsx
  const navigate = useNavigate();
  navigate("/"); // vai para a home
  ```

- **`<Navigate to="/login" />`** — um componente que, ao ser renderizado, redireciona na hora. Útil dentro de condições.

> **Vindo do React Native:** `<Route>` ≈ tela no navigator; `useNavigate()` ≈ `navigation.navigate()`; `<Navigate>` ≈ um redirect condicional.

---

## Parte 2 — Context API

### O problema: prop drilling

Depois do login, **vários** componentes precisam saber o token: o `Home` para buscar dados, o formulário para criar, um botão de logout no cabeçalho, a rota protegida para decidir se deixa entrar.

Sem uma ferramenta, o token teria que descer por prop, de componente em componente:

```
App → Home → Cabecalho → BotaoLogout   (só o último usa o token)
```

Os do meio (`Home`, `Cabecalho`) recebem e repassam uma prop que **não usam para nada**, só para entregar ao próximo. Isso se chama **prop drilling** — é verboso e quebra fácil quando a árvore muda.

### A solução: um "canal" que qualquer componente sintoniza

A **Context API** cria um valor compartilhado que qualquer componente **dentro de uma área da árvore** pode ler diretamente, sem receber por prop.

```jsx
// src/context/AuthContext.jsx
import { createContext, useState, useContext } from "react";

const AuthContext = createContext(null);

export function AuthProvider({ children }) {
  const [token, setToken] = useState(() => localStorage.getItem("token"));

  function login(novoToken) {
    setToken(novoToken);
    localStorage.setItem("token", novoToken);
  }

  function logout() {
    setToken(null);
    localStorage.removeItem("token");
  }

  return (
    <AuthContext.Provider value={{ token, login, logout }}>
      {children}
    </AuthContext.Provider>
  );
}

export function useAuth() {
  return useContext(AuthContext);
}
```

Lendo por partes:

- **`createContext(null)`** — cria o canal. `null` é o valor padrão (usado só se alguém ler o contexto sem estar dentro do Provider).
- **`AuthProvider`** — um componente que **segura o estado** (`token`) e **disponibiliza** um objeto com `{ token, login, logout }` para tudo que estiver dentro dele (`children`).
- **`useState(() => localStorage.getItem("token"))`** — a função dentro do `useState` é um **inicializador preguiçoso**: roda só uma vez, na montagem, para pegar o token que já estava salvo (assim o usuário continua logado depois de recarregar a página).
- **`login` / `logout`** — mudam o estado **e** o `localStorage` juntos, para os dois nunca ficarem fora de sincronia.
- **`useAuth()`** — um atalho. Em vez de cada componente importar `AuthContext` e chamar `useContext`, ele chama `useAuth()`.

### Usando

Envolva a aplicação com o Provider, em `main.jsx` (por fora ou por dentro do `BrowserRouter`, tanto faz, desde que envolva tudo que precisa de `useAuth`):

```jsx
<AuthProvider>
  <BrowserRouter>
    <App />
  </BrowserRouter>
</AuthProvider>
```

E qualquer componente lê a sessão com uma linha:

```jsx
const { token, login, logout } = useAuth();
```

> **Vindo do React Native:** é o mesmo padrão usado para guardar sessão em apps RN. O `localStorage` aqui é o do navegador de verdade — funciona normalmente no projeto de vocês. (No RN seria `AsyncStorage`.)

---

## Parte 3 — CRUD: os quatro verbos

"CRUD" são as quatro operações sobre um recurso, e cada uma tem um método HTTP:

| Operação               | Método HTTP     | No projeto          | Precisa de token? |
| ---------------------- | --------------- | ------------------- | ----------------- |
| **C**reate (criar)     | `POST`          | criar notificação   | sim               |
| **R**ead (ler)         | `GET`           | listar notificações | não               |
| **U**pdate (atualizar) | `PUT` / `PATCH` | marcar como lida    | sim               |
| **D**elete (apagar)    | `DELETE`        | excluir notificação | sim               |

**Ler é aberto; escrever exige login.** Faz sentido: qualquer um pode ver o mural, mas só quem está autenticado pode mexer nele.

### Como mandar o token

Toda requisição de escrita leva um cabeçalho `Authorization` com o token:

```jsx
import { API_URL } from "../config"; // ajuste o caminho conforme a pasta do arquivo

const { token } = useAuth();

await fetch(`${API_URL}/notificacoes`, {
  method: "POST",
  headers: {
    "Content-Type": "application/json",
    Authorization: `Bearer ${token}`,
  },
  body: JSON.stringify(novaNotificacao),
});
```

`API_URL` é o mesmo módulo de configuração criado no encontro 8 — a URL da API do seu grupo. Nenhum `fetch` a partir de hoje deveria ter um endereço escrito na mão.

- **`method`** — muda conforme a operação (`POST`, `PUT`, `DELETE`).
- **`"Content-Type": "application/json"`** — avisa a API que o corpo é JSON. Sem isso, ela pode não conseguir ler o `body`.
- **`"Authorization": \`Bearer ${token}\`\`** — o formato que o backend de vocês espera (`Bearer ` + espaço + token). É isso que o middleware de autenticação do encontro 5 valida.
- **`body: JSON.stringify(...)`** — o corpo tem que ser **string**; `JSON.stringify` converte o objeto.
- `GET` **não** leva `Authorization` nem `body`.

---

## Parte 4 — Situação-problema

**Fluxo de trabalho:**

```bash
git checkout main && git pull
git checkout -b seu-nome-22-09
```

### Passo 1 — Instalar rotas e reorganizar em páginas

1. `npm install react-router-dom`.
2. `main.jsx`: envolver o `App` com `<BrowserRouter>`.
3. Criar `src/pages/Home.jsx` e **mover** para lá todo o conteúdo atual do `App` (estados, `useEffect` do `fetch`, JSX da lista/filtro/formulário).
4. `App.jsx` vira só o `<Routes>` com as rotas `/login` e `/`.
5. Rodar e confirmar que `/` ainda mostra a lista.

### Passo 2 — Context de autenticação

1. Criar `src/context/AuthContext.jsx` (código da Parte 2).
2. Envolver a aplicação com `<AuthProvider>` no `main.jsx`.

### Passo 3 — Tela de login

```jsx
// src/pages/Login.jsx
import { useState } from "react";
import { useNavigate } from "react-router-dom";
import { useAuth } from "../context/AuthContext";
import { API_URL } from "../config";

function Login() {
  const [email, setEmail] = useState("");
  const [senha, setSenha] = useState("");
  const [erro, setErro] = useState(null);
  const { login } = useAuth();
  const navigate = useNavigate();

  async function handleSubmit(e) {
    e.preventDefault();
    setErro(null);
    try {
      const resposta = await fetch(`${API_URL}/auth/login`, {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({ email, senha }),
      });
      if (!resposta.ok) throw new Error("Credenciais inválidas");
      const { token } = await resposta.json();
      login(token);
      navigate("/");
    } catch (e) {
      setErro(e.message);
    }
  }

  return (
    <form
      onSubmit={handleSubmit}
      className="max-w-sm mx-auto p-4 flex flex-col gap-2"
    >
      <input
        value={email}
        onChange={(e) => setEmail(e.target.value)}
        placeholder="E-mail"
        className="border border-gray-200 rounded-lg px-3 py-2"
      />
      <input
        type="password"
        value={senha}
        onChange={(e) => setSenha(e.target.value)}
        placeholder="Senha"
        className="border border-gray-200 rounded-lg px-3 py-2"
      />
      {erro && <p className="text-red-600 text-sm">{erro}</p>}
      <button className="bg-marca text-white rounded-lg px-4 py-2 font-semibold">
        Entrar
      </button>
    </form>
  );
}

export default Login;
```

Repare: os `<input>` são **controlados**, exatamente como o formulário do encontro 7. O `handleSubmit` é o padrão `async` + `try/catch` do encontro 8, com `POST` + `Content-Type` da Parte 3. Ao dar certo: `login(token)` guarda a sessão e `navigate("/")` troca de tela.

**Testar:** login com as credenciais que vocês criaram no encontro 5 (`POST /auth/registro`). Ao logar, a URL deve mudar para `/` e a lista aparecer.

### Passo 4 — Proteger a rota principal

```jsx
import { Navigate } from "react-router-dom";
import { useAuth } from "./context/AuthContext";

function RotaProtegida({ children }) {
  const { token } = useAuth();
  if (!token) return <Navigate to="/login" />;
  return children;
}
```

```jsx
// no App.jsx
<Route
  path="/"
  element={
    <RotaProtegida>
      <Home />
    </RotaProtegida>
  }
/>
```

`RotaProtegida` é composição (`children`) + Context: se não há token, renderiza `<Navigate>` e o usuário é redirecionado; se há, mostra o conteúdo.

**Testar:** abrir `/` numa aba anônima (sem login) e confirmar o redirecionamento para `/login`.

### Passo 5 — Completar o CRUD com token

O formulário do encontro 7 já monta a notificação. Agora, em vez de só chamar `onAdicionar` local, ele faz um `POST` na API (Parte 3) com o `Authorization`. Depois do `POST` dar certo, aí sim atualiza a lista na tela.

Faltam duas operações no `NotificationCard` (ou onde fizer sentido):

- **Marcar como lida** — `PUT` ou `PATCH` na notificação, mesmo header de autenticação.
- **Apagar** — `DELETE` na notificação, mesmo header.

As três escritas seguem o mesmo molde: mesma URL base, método diferente, sempre `Authorization: Bearer <token>`. **Ler (`GET`) continua sem token**, como no encontro 8.

### Passo 6 — Testar o fluxo completo

Com outro grupo, em sequência: **login → ver a lista → criar uma notificação → marcar como lida → apagar → logout → confirmar que `/` redireciona de volta para `/login`.**

### Passo 7 — Commit, push e Pull Request

```bash
git add .
git commit -m "adiciona rotas, contexto de auth e CRUD autenticado"
git push origin seu-nome-22-09
```

Grupo escolhe uma branch, abre PR, revisa, faz merge. Todos rodam `git checkout main && git pull`.

---

## Erros comuns de hoje

| Sintoma                                                     | Causa provável                                                                                                                       |
| ----------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| `useAuth()` devolve `null` / erro ao desestruturar          | O componente não está **dentro** do `<AuthProvider>`. Confira o `main.jsx`.                                                          |
| Recarreguei numa rota tipo `/login` e deu 404               | Servidor não configurado para SPA. No `vite dev` costuma funcionar; em build/deploy precisa de fallback para `index.html`.           |
| Loguei, mas a próxima tela diz "não autorizado"             | O token não está indo no header, ou o formato não é `Bearer <token>`, ou você mandou `Authorization` num `GET` que a API não espera. |
| Fiz logout mas o app continua "logado"                      | `logout` não limpou o `localStorage`, ou algum componente guardou o token numa cópia própria em vez de ler do Context.               |
| Depois de recarregar, caí no login mesmo tendo logado antes | O `useState` do token não está lendo do `localStorage` na inicialização (`useState(() => localStorage.getItem("token"))`).           |
| `POST` volta com erro de CORS só agora                      | A API precisa liberar os headers `Content-Type` e `Authorization` no CORS. Ajuste no backend.                                        |
| A notificação criada só aparece depois de recarregar        | Faltou atualizar o estado da lista no front depois do `POST` dar certo.                                                              |
| Redireciona para `/login` em loop                           | A rota `/login` também está dentro da `RotaProtegida`. Só a `/` deve ser protegida.                                                  |

---

## Checklist de encerramento

- [ ] Rotas `/login` e `/` configuradas, conteúdo do `App` movido para `pages/Home.jsx`
- [ ] `AuthContext` criado; aplicação envolvida pelo `<AuthProvider>`
- [ ] Login funcionando; token guardado em Context **e** `localStorage`
- [ ] Sessão sobrevive a recarregar a página
- [ ] Rota `/` protegida — quem não está logado vai para `/login`
- [ ] Criar, marcar como lida e apagar — todas autenticadas com `Authorization: Bearer <token>`
- [ ] `GET` da lista continua sem token
- [ ] Fluxo completo testado, do login ao logout
- [ ] Pull Request revisado e mergeado
- [ ] Todos atualizaram a `main` local antes de sair

---

## Se o CORS bloquear só o login/CRUD (não o `GET`)

O `GET` da lista funciona desde o encontro 8. Se agora, com login e CRUD, aparecer erro de CORS de novo, o motivo mais comum é a API do grupo aceitar a origem mas não os cabeçalhos extras (`Authorization`, `Content-Type`) nem os métodos de escrita no _preflight_. Ver "CORS além do básico" em [`extra-consumir-api-do-servidor.md`](<../Aula 3 - useEffect Fetch e CORS/extra-consumir-api-do-servidor.md>).

---

## Para pensar até o próximo encontro

Depois deste encontro, o trabalho muda de natureza: não é mais **construir** — é **polir** o que já funciona de ponta a ponta. Estados de carregando em toda ação, mensagens de erro claras, campos validados, o visual fiel ao Figma. O produto está de pé; agora ele precisa ficar apresentável.
