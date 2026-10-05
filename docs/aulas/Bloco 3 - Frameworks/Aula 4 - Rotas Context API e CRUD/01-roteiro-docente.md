# Roteiro Docente — Encontro 9 · 22/09/2026

**UC:** PEND — Programação Front-End
**Turma:** 2-2026-SESI_DEV_OC_1 · 5 aulas
**Tema:** Rotas (react-router-dom) · Context API + JWT · CRUD completo

---

## Por que este é o encontro mais importante do bloco

Tudo que veio antes converge aqui. O JWT do encontro 5 finalmente é consumido. O formulário do encontro 7 finalmente cria de verdade, não só localmente. O `fetch` do encontro 8 finalmente vai além de ler. Depois de hoje, "a interface consome a API de Notificações" deixa de ser a meta do semestre e vira fato consumado — o que resta (blocos 4, 5, 6) é refinamento sobre uma base funcional, não construção do zero.

Reserve energia para este dia. É o mais denso do bloco.

---

## Objetivos do encontro

Ao final, o aluno deve ser capaz de:

1. Configurar rotas com `react-router-dom` e navegar entre telas sem recarregar a página.
2. Explicar o que resolve o Context API e por que evita passar prop por muitos níveis.
3. Implementar login consumindo o JWT criado no encontro 5, guardando o token em contexto.
4. Proteger rotas que exigem autenticação.
5. Executar as quatro operações de CRUD (criar, ler, atualizar, apagar) autenticadas.

**Capacidades mobilizadas**
`CT` Desenvolver interfaces web consumindo API
`CS` Demonstrar autonomia

---

## Antes da aula (preparação sua)

- [ ] Relembrar com cada grupo (ou verificar no repositório) as credenciais de teste criadas no encontro 5 (`POST /auth/registro`).
- [ ] Ter clara a URL de login de cada grupo, para não perder tempo procurando durante a aula.

---

## Aula 1 — Rotas com react-router-dom (50 min)

**Estratégia:** Exposição dialogada + Atividade prática

### Instalação e configuração básica (20 min)

```bash
npm install react-router-dom
```

```jsx
// main.jsx
import { BrowserRouter } from 'react-router-dom';

<BrowserRouter>
  <App />
</BrowserRouter>
```

```jsx
// App.jsx
import { Routes, Route } from 'react-router-dom';
import Login from './pages/Login';
import Home from './pages/Home';

<Routes>
  <Route path="/login" element={<Login />} />
  <Route path="/" element={<Home />} />
</Routes>
```

> **Ponte com React Native:** o React Navigation resolve o mesmo problema — navegar entre telas sem recarregar tudo do zero. A sintaxe é diferente, o conceito é idêntico: rotas nomeadas, cada uma com seu componente.

### Reorganizando o projeto em páginas (30 min)

Até agora, tudo vivia em `App.jsx`. Hoje ele se divide:

- `pages/Login.jsx` — formulário de login
- `pages/Home.jsx` — a lista de notificações, filtros e formulário que já existiam

`App.jsx` passa a só decidir qual página mostrar, via `<Routes>`.

---

## Aula 2 — Context API: o problema que ele resolve (50 min)

**Estratégia:** Exposição dialogada

### O problema (15 min)

Sem Context, para `Home` saber se o usuário está logado, o token precisaria ser passado por prop desde o componente mais alto até quem precisa — mesmo que passe por componentes no meio que não usam o token para nada. Isso se chama *prop drilling*.

### Context API resolve isso (20 min)

```jsx
// context/AuthContext.jsx
import { createContext, useState, useContext } from 'react';

const AuthContext = createContext(null);

export function AuthProvider({ children }) {
  const [token, setToken] = useState(null);

  function login(novoToken) {
    setToken(novoToken);
    localStorage.setItem('token', novoToken);
  }

  function logout() {
    setToken(null);
    localStorage.removeItem('token');
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

Qualquer componente dentro do `AuthProvider` acessa o token com uma linha, sem prop nenhuma no meio do caminho:

```jsx
const { token, login, logout } = useAuth();
```

> **Ponte com React Native:** Context API é usado exatamente assim em apps RN para guardar sessão de usuário — muitos apps que vocês já usaram (ou construíram em PPDM) fazem isso por trás dos panos.

### Persistência com `localStorage` (15 min)

Guardar o token no `localStorage` faz o login "durar" mesmo se a página recarregar. Mostrar rapidamente como recuperar ao iniciar o app:

```jsx
const [token, setToken] = useState(() => localStorage.getItem('token'));
```

> Isso é `localStorage` do navegador de verdade, funcionando normalmente no projeto de vocês — diferente da restrição que existe em ambientes de teste/artefato, aqui não há problema nenhum em usar.

---

## Aulas 3 a 5 — Situação-problema: login, rotas protegidas e CRUD (150 min)

**Estratégia:** Estratégia desafiadora

### Fluxo de trabalho (branch/PR)

Mesma dinâmica de sempre.

### Parte A — Tela de login consumindo o JWT (40 min)

```jsx
// pages/Login.jsx
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
      const resposta = await fetch('http://localhost:3000/auth/login', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ email, senha }),
      });
      if (!resposta.ok) throw new Error('Credenciais inválidas');
      const { token } = await resposta.json();
      login(token);
      navigate('/');
    } catch (e) {
      setErro(e.message);
    }
  }

  return (
    <form onSubmit={handleSubmit}>
      {/* inputs controlados de email e senha, iguais ao formulário do encontro 7 */}
      {erro && <p className="text-red-600">{erro}</p>}
    </form>
  );
}
```

**Testar:** login com as credenciais criadas no encontro 5. Confirmar redirecionamento para `/`.

### Parte B — Rota protegida (30 min)

```jsx
function RotaProtegida({ children }) {
  const { token } = useAuth();
  if (!token) return <Navigate to="/login" />;
  return children;
}

// uso:
<Route path="/" element={<RotaProtegida><Home /></RotaProtegida>} />
```

**Testar:** acessar `/` sem estar logado deve redirecionar para `/login`.

### Parte C — CRUD completo, agora autenticado (60 min)

O formulário do encontro 7 já cria notificações — agora precisa mandar o token:

```jsx
const { token } = useAuth();

await fetch('http://localhost:3000/notificacoes', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'Authorization': `Bearer ${token}`,
  },
  body: JSON.stringify(novaNotificacao),
});
```

Acrescentar as duas operações que faltam:

- **Atualizar** — marcar como lida (`PUT`/`PATCH`), mesmo header de autenticação.
- **Apagar** — excluir notificação (`DELETE`), mesmo header.

> Todas as três (criar, atualizar, apagar) seguem o mesmo padrão: mesma URL base, método diferente, sempre com `Authorization: Bearer <token>`. Ler (`GET`) continua sem token, como no encontro 8.

### Validação cruzada e Pull Request (20 min)

Grupos testam uns aos outros: login → ver lista → criar → marcar como lida → apagar → logout. Se algum passo falhar em silêncio, é sinal de token não sendo enviado ou rota não protegida corretamente.

---

## O que observar circulando

| Sinal | Ação |
|---|---|
| Token não enviado no header (`Authorization` ausente) | API provavelmente responde `401` — checar Network tab do DevTools |
| Rota protegida não redireciona | Conferir se `RotaProtegida` está de fato envolvendo o `<Route>` certo |
| Token não persiste ao recarregar página | Esqueceram de ler do `localStorage` na inicialização do estado |
| Logout não limpa a tela | Verificar se `logout()` também redireciona para `/login`, não só limpa o token |

---

## Fechamento em sala (últimos 15 min)

Sem tarefa de casa. Antes de liberar, cada grupo precisa ter:

- [ ] Rotas configuradas (`/login` e `/`)
- [ ] Login funcionando, token guardado em Context + `localStorage`
- [ ] Rota protegida redirecionando quem não está logado
- [ ] Criar, atualizar e apagar notificação — todos autenticados
- [ ] Fluxo completo testado: login → listar → criar → editar → apagar → logout
- [ ] Pull Request revisado e mergeado
- [ ] Todos atualizaram a `main` local antes de sair

---

## Recursos

Quadro branco · Projetor · Computadores dos alunos · `CONTRATO-API.md` · Credenciais de teste do encontro 5 · Servidor Proxmox (LXC)

## Ponte para o encontro 10 (29/09)

Fechar com: *"Na próxima terça é a somativa parcial. A partir de hoje, o que falta não é construir — é polir o que já funciona de ponta a ponta."*
