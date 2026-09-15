# Encontro 8 — Buscando Dados da API: `async`/`await`, `useEffect`, `fetch` e CORS

**PEND — Programação Front-End** · 15/09/2026

---

## O que você vai conseguir fazer ao fim de hoje

- Explicar o que é uma operação **assíncrona** e usar `async`/`await` sem se perder.
- Entender o que o **CORS** bloqueia, por que ele existe e onde se resolve (na API, não no navegador).
- Usar o **`useEffect`** para rodar código quando o componente aparece na tela.
- Buscar dados de uma API com **`fetch`** e tratar os **três estados** de toda requisição: carregando, erro, sucesso.
- Trocar a lista fixa do projeto por dados reais vindos do banco de dados de vocês.

> **Recapitulando:** até agora a lista de notificações era um array escrito à mão no `App.jsx`. Hoje esse array some. A lista passa a vir da API que vocês construíram no backend.

> **Se você já usou React Native:** `useEffect`, `fetch` e `async`/`await` são idênticos aos de lá. O CORS é a única novidade real — e ele aparece porque agora o código roda num **navegador**, que tem regras de segurança que o app de celular não tem.

---

## Parte 1 — Assíncrono: código que espera

### O problema

Buscar dado de uma API leva tempo — décimos de segundo, às vezes mais. Se o JavaScript **parasse** e ficasse esperando a resposta chegar, a página inteira congelaria nesse meio tempo: nenhum clique, nenhum scroll, nada.

Então o JavaScript não espera. Ele **dispara** a busca, continua executando o resto, e trata a resposta **quando ela chegar**. Isso é código **assíncrono**.

### `Promise`: a "promessa de um valor futuro"

Uma função assíncrona não devolve o valor na hora — devolve uma **`Promise`**: um objeto que representa "o resultado ainda não está pronto, mas vai estar". Uma Promise termina de um de dois jeitos: **resolvida** (deu certo, tem um valor) ou **rejeitada** (deu erro).

### `async`/`await`: lendo assíncrono como se fosse linha a linha

`await` pausa a função **naquele ponto** até a Promise terminar, e entrega o valor resolvido. Só pode ser usado dentro de uma função marcada com `async`.

```jsx
async function buscar() {
  const resposta = await fetch("http://localhost:3000/notificacoes");
  const dados = await resposta.json();
  console.log(dados);
}
```

Leia como: "busque (e espere), depois converta para JSON (e espere), depois imprima". A função `buscar` roda de forma assíncrona, mas **por dentro** dela o código é linear e fácil de ler.

- `await fetch(...)` espera a resposta **chegar**.
- `await resposta.json()` espera o **corpo** da resposta ser lido e convertido de texto para objeto. São duas esperas separadas.

### `try`/`catch`/`finally`

Como uma Promise pode ser rejeitada, o `await` pode "lançar" um erro. Você captura com `try`/`catch`:

```jsx
async function buscar() {
  try {
    const resposta = await fetch("...");
    const dados = await resposta.json();
    // ... usa os dados
  } catch (e) {
    // ... deu erro em qualquer await acima
  } finally {
    // ... roda SEMPRE, com erro ou sem erro
  }
}
```

O `finally` é o lugar certo para "desligar o carregando" — porque ele roda nos dois caminhos.

---

## Parte 2 — CORS

### O erro de propósito

Com o `npm run dev` do front-end rodando, abra o console do navegador (F12) **na aba do seu projeto** e cole:

```js
fetch('http://localhost:3000/notificacoes').then(r => r.json()).then(console.log)
```

Se aparecer um erro vermelho mencionando **"CORS policy"**, ótimo — era o esperado. Não é bug de vocês.

### O que é uma "origem"

Uma **origem** é a combinação **protocolo + domínio + porta**. Exemplos:

- `http://localhost:5173` — o front-end de vocês (Vite)
- `http://localhost:3000` — a API de vocês (Express)

Mesma máquina, mas **portas diferentes** → **origens diferentes**.

### O que o CORS bloqueia

Por padrão, o navegador **não deixa** uma página de uma origem ler a resposta de uma API de **outra origem**. Isso se chama política de *same-origin*, e o CORS (*Cross-Origin Resource Sharing*) é o mecanismo que permite abrir exceções.

### Por que isso existe

Imagine que você está logado no seu banco numa aba. Sem essa proteção, qualquer site aberto em outra aba poderia disparar requisições para a API do banco **usando a sua sessão**, e ler as respostas. A política de same-origin corta isso: o site malicioso até consegue **enviar** a requisição, mas o navegador **não deixa ele ler a resposta**, a menos que a API autorize aquela origem explicitamente.

### Onde se resolve: na API

Quem autoriza é o **servidor**, respondendo com um cabeçalho que diz "origens assim podem me chamar". O navegador só obedece. Não há nada para "consertar" no front-end.

Na API de vocês (Express):

```bash
npm install cors
```

```js
const cors = require('cors');
app.use(cors());
```

`app.use(cors())` sem argumentos libera **todas** as origens — suficiente para desenvolvimento. Em produção você restringiria à origem real do front.

Rode o `fetch` do console de novo. Se o erro sumiu, está resolvido.

> **Por que o React Native nunca reclamou de CORS?** Porque CORS é uma regra **do navegador**. Um app de celular não roda num navegador, então a regra não se aplica. O mesmo `fetch` que funcionava direto no RN pode ser bloqueado na web.

---

## Parte 3 — `useEffect`: rodar código em momentos-chave do componente

### O problema

Você **não pode** simplesmente chamar `fetch` no corpo do componente:

```jsx
function App() {
  const dados = fetch(...);   // ERRADO
}
```

O corpo do componente roda **toda vez que ele renderiza** — e ele renderiza muitas vezes. Isso dispararia uma busca a cada renderização, e como cada busca chama `setState`, que causa outra renderização... vira um laço infinito.

### A solução: `useEffect`

`useEffect` serve para código que precisa acontecer **por causa de** uma renderização, mas não **durante** ela: buscar dados, assinar um evento, mexer no `document`.

```jsx
import { useEffect } from "react";

useEffect(() => {
  console.log("o componente apareceu na tela");
}, []);
```

Dois argumentos:

1. **A função** — o que rodar.
2. **O array de dependências** — quando rodar.

### O array de dependências, os três casos

| Você escreve | Significa |
| --- | --- |
| `[]` (array vazio) | Roda **uma vez**, quando o componente aparece pela primeira vez. |
| `[filtro]` | Roda na primeira vez **e** toda vez que `filtro` mudar. |
| (omitido) | Roda depois de **toda** renderização. Quase nunca é o que você quer. |

Para buscar a lista uma vez quando a tela carrega, o caso é `[]`.

> **Vindo do React Native:** mesmo hook, mesma assinatura, mesmas regras do array de dependências. Nada novo aqui.

---

## Parte 4 — Os três estados de uma requisição

Toda tela que busca dado de fora precisa responder três perguntas, o tempo todo: **ainda está carregando? deu certo? deu erro?**

Se você ignorar isso e só tiver a lista, a tela vai mostrar "nenhuma notificação" durante o instante em que os dados ainda estão a caminho — parece bug, mas é só falta de tratamento. E se a API cair, a tela quebra sem explicação.

### O padrão completo

```jsx
const [notificacoes, setNotificacoes] = useState([]);
const [carregando, setCarregando] = useState(true);
const [erro, setErro] = useState(null);

useEffect(() => {
  async function buscar() {
    try {
      const resposta = await fetch("http://localhost:3000/notificacoes");
      if (!resposta.ok) throw new Error("Erro ao buscar notificações");
      const dados = await resposta.json();
      setNotificacoes(dados);
    } catch (e) {
      setErro(e.message);
    } finally {
      setCarregando(false);
    }
  }

  buscar();
}, []);
```

Lendo por partes:

- **Três estados:** a lista (começa vazia), `carregando` (começa `true` — a tela já nasce carregando), `erro` (começa `null` — sem erro).
- **`async function buscar()` dentro do `useEffect`:** a função do `useEffect` não pode ser `async` ela mesma, então declaramos uma função `async` por dentro e a chamamos (`buscar()`) na última linha.
- **`if (!resposta.ok) throw ...`:** ponto que pega todo mundo — o `fetch` **só** rejeita a Promise em falha de rede de verdade (sem internet, servidor fora do ar). Se a API responde `404` ou `500`, o `fetch` considera "deu certo, recebi uma resposta". Você precisa checar `resposta.ok` (que é `true` para status 200–299) e lançar o erro na mão.
- **`await resposta.json()`:** lê o corpo e converte de texto JSON para objeto/array JavaScript.
- **`catch (e)`:** qualquer erro dos `await` acima cai aqui. Guardamos a mensagem em `erro`.
- **`finally`:** com erro ou sem erro, a busca terminou → `setCarregando(false)`.

### Mostrando os três estados na tela

```jsx
{carregando && <p className="text-gray-500">Carregando notificações...</p>}
{erro && <p className="text-red-600">Não foi possível carregar. Tente novamente.</p>}
{!carregando && !erro && (
  <NotificationList notificacoes={notificacoesVisiveis} />
)}
```

Um bloco por estado. Só um aparece de cada vez. Isso é a heurística **"visibilidade do status do sistema"** (encontro 4): o usuário nunca fica sem saber o que está acontecendo.

---

## Parte 5 — Situação-problema

**Fluxo de trabalho:**

```bash
git checkout main && git pull
git checkout -b seu-nome-15-09
```

### Passo 1 — Ligar o CORS na API

No repositório do **backend**, instale e ative o `cors` (Parte 2). Suba a API. Teste o `fetch` pelo console do front até o erro de CORS sumir.

### Passo 2 — Trocar dados fixos por dados reais

No `App.jsx`:

- Remova a `const notificacoesIniciais` (o array escrito à mão).
- Troque `useState(notificacoesIniciais)` por `useState([])`.
- Adicione os estados `carregando` (inicial `true`) e `erro` (inicial `null`).
- Adicione o `useEffect` com a função `buscar` (Parte 4).
- A URL da API está no `CONTRATO-API.md` que vocês criaram no encontro 1 — use a **de vocês**, não `localhost:3000` fixo se a porta for outra.

O `notificacoesVisiveis` (o `.filter` do encontro passado) **continua igual** — ele filtra o que estiver em `notificacoes`, venha de onde vier.

### Passo 3 — Mostrar os três estados

Coloque os três blocos condicionais (Parte 4) no lugar onde hoje está só o `<NotificationList>`.

### Passo 4 — Testar o erro de propósito

Com a tela funcionando, **desligue a API** (pare o processo) e recarregue o front. Deve aparecer a mensagem de erro amigável — **nunca** uma tela em branco ou o app quebrado. Religue a API e recarregue: a lista volta.

### Passo 5 — Validação cruzada

Abra a tela de outro grupo e confira: os dados que aparecem batem com o banco **daquele** grupo? Se sim, o `fetch` de vocês está apontando para a API certa e lendo a resposta certa.

### Passo 6 — Commit, push e Pull Request

```bash
git add .
git commit -m "busca notificações da API com fetch, trata carregando e erro"
git push origin seu-nome-15-09
```

Grupo escolhe uma branch, abre PR, revisa, faz merge. Todos rodam `git checkout main && git pull`.

---

## Erros comuns de hoje

| Sintoma | Causa provável |
| --- | --- |
| Erro vermelho "CORS policy" no console | O `cors` não está ativo na API, ou a API não foi reiniciada depois de adicionar. Resolve **no backend**. |
| A tela pisca "nenhuma notificação" e depois mostra a lista | Faltou o estado `carregando` — a lista vazia aparece enquanto os dados vêm. |
| A tela fica em branco quando a API cai | O erro não está sendo capturado/mostrado. Confira o `try/catch` e o bloco `{erro && ...}`. |
| A busca dispara sem parar / a página trava | `useEffect` sem o `[]` no final, ou `fetch` chamado direto no corpo do componente. |
| API respondeu 404 mas o código seguiu como se tivesse dado certo | Faltou `if (!resposta.ok) throw ...`. O `fetch` não lança erro para status HTTP. |
| `dados.map is not a function` | A API não devolveu um array (talvez `{ notificacoes: [...] }`). Confira o formato no `CONTRATO-API.md` e ajuste. |
| `Unexpected token < in JSON` | A resposta não era JSON (às vezes é uma página de erro HTML). Cheque a URL e se a API está no ar. |
| Warning "can't perform a state update on an unmounted component" | A tela trocou antes da resposta chegar. Por ora, sem problema; a forma robusta (cleanup do `useEffect`) vem depois. |

---

## Checklist de encerramento

- [ ] CORS habilitado na própria API (`cors` instalado e `app.use(cors())`)
- [ ] Lista de notificações vindo do banco de dados real, via `fetch` dentro de `useEffect` com `[]`
- [ ] Estado de **carregando** visível enquanto os dados chegam
- [ ] Estado de **erro** testado de propósito (API desligada) com mensagem amigável
- [ ] `resposta.ok` checado antes de usar os dados
- [ ] Filtro do encontro passado ainda funcionando sobre os dados reais
- [ ] Pull Request revisado e mergeado
- [ ] Todos atualizaram a `main` local antes de sair

---

## Material extra (opcional)

Hoje o `fetch` aponta para `http://localhost:3000` — a API rodando no próprio notebook. Dá para apontar o front-end para a **API do seu grupo hospedada no servidor da escola**, o que torna o CORS um problema real (origem de verdade diferente) e dispensa subir a API localmente. O passo a passo — centralizar a URL numa variável `VITE_API_URL`, trocar os `fetch`, e habilitar o `cors` na API do 3º semestre — está em [`extra-consumir-api-do-servidor.md`](extra-consumir-api-do-servidor.md). Fazer quando a rede da sala permitir.

---

## Para pensar até o próximo encontro

Hoje vocês só **pediram** para a API: "me dá a lista" — e ela deu, sem perguntar quem estava pedindo. No próximo encontro vocês vão se **apresentar** para ela: login de verdade, com o JWT que construíram no encontro 5. Depois disso, a API passa a distinguir quem pode ver de quem pode criar, editar e apagar.
