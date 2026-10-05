# Encontro 8 — Buscando Dados da API: `async`/`await`, `useEffect`, `fetch` e CORS

**PEND — Programação Front-End** · 22/09/2026

---

## O que você vai conseguir fazer ao fim de hoje

- Explicar o que é uma operação **assíncrona** e usar `async`/`await` sem se perder.
- Configurar o front-end para apontar para a **API do seu grupo**, hospedada no servidor da escola, sem endereço fixo espalhado pelo código.
- Entender o que o **CORS** bloqueia, por que ele existe e onde se resolve (na API, não no navegador) — inclusive quando a API já está em produção.
- Usar o **`useEffect`** para rodar código quando o componente aparece na tela.
- Buscar dados de uma API com **`fetch`** e tratar os **três estados** de toda requisição: carregando, erro, sucesso.
- Trocar a lista fixa do projeto por dados reais vindos do banco de dados do seu grupo.

> **Recapitulando:** na semana passada (15/09) vocês recapitularam componentes, props, estado e formulários dos encontros 6–7. Até agora a lista de notificações era um array escrito à mão no `App.jsx`. Hoje esse array some de vez: a lista passa a vir da **API de verdade**, que seu grupo já tem rodando no servidor da escola desde o 3º semestre.

> **Se você já usou React Native:** `useEffect`, `fetch` e `async`/`await` são idênticos aos de lá. O CORS é a única novidade real — e ele aparece porque agora o código roda num **navegador**, que tem regras de segurança que o app de celular não tem.

---

## Antes de tudo: um erro de propósito

Cada grupo tem sua API rodando num container no servidor da escola, num único IP e uma porta por grupo:

| Grupo | Porta | URL base da API              |
| ----- | ----- | ---------------------------- |
| 1     | 8201  | `http://10.187.226.125:8201` |
| 2     | 8202  | `http://10.187.226.125:8202` |
| 3     | 8203  | `http://10.187.226.125:8203` |
| 4     | 8204  | `http://10.187.226.125:8204` |
| 5     | 8205  | `http://10.187.226.125:8205` |
| 6     | 8206  | `http://10.187.226.125:8206` |
| 7     | 8207  | `http://10.187.226.125:8207` |
| 8     | 8208  | `http://10.187.226.125:8208` |

> **Só funciona na rede da escola.** `10.187.226.125` é um IP **privado** — de casa, sem VPN, não há como alcançar; o `fetch` vai dar timeout. Isso não é bug do código.

Com o `npm run dev` do front-end rodando, abra o console do navegador (F12) **na aba do seu projeto** e cole, trocando a porta pela do **seu** grupo:

```js
fetch("http://10.187.226.125:8203/notificacoes")
  .then((r) => r.json())
  .then(console.log);
```

Se aparecer um erro vermelho mencionando **"CORS policy"**, ótimo — era o esperado. Não é bug de vocês: essa API foi escrita no 3º semestre, **antes** de existir um front-end para consumi-la, então quase certamente ainda não trata CORS. Vamos entender por quê antes de corrigir.

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
  const resposta = await fetch("https://api.exemplo.com/notificacoes");
  const dados = await resposta.json();
  console.log(dados);
}
```

> O endereço acima é ilustrativo. Na prática vocês vão usar a URL da API do **seu** grupo — como deixar isso configurado, sem repetir o endereço em vários arquivos, é o Passo 1 da situação-problema, mais adiante.

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

### O que é uma "origem"

Uma **origem** é a combinação **protocolo + domínio + porta**. No nosso caso:

- `http://localhost:5173` — o front-end de vocês (Vite), rodando no notebook
- `http://10.187.226.125:820N` — a API do grupo, rodando no servidor da escola

Máquinas diferentes, endereços diferentes → **origens diferentes**. (O mesmo valeria mesmo que a API estivesse na mesma máquina, só numa porta diferente — origem é a combinação inteira, não só o domínio.)

### O que o CORS bloqueia

Por padrão, o navegador **não deixa** uma página de uma origem ler a resposta de uma API de **outra origem**. Isso se chama política de _same-origin_, e o CORS (_Cross-Origin Resource Sharing_) é o mecanismo que permite abrir exceções.

### Por que isso existe

Imagine que você está logado no seu banco numa aba. Sem essa proteção, qualquer site aberto em outra aba poderia disparar requisições para a API do banco **usando a sua sessão**, e ler as respostas. A política de same-origin corta isso: o site malicioso até consegue **enviar** a requisição, mas o navegador **não deixa ele ler a resposta**, a menos que a API autorize aquela origem explicitamente.

### Onde se resolve: na API do seu grupo — e por que precisa reimplantar

Quem autoriza é o **servidor**, respondendo com um cabeçalho que diz "origens assim podem me chamar". O navegador só obedece; não há nada para "consertar" no front-end.

No repositório do **backend** do grupo:

```bash
npm install cors
```

```js
const cors = require("cors");
app.use(cors()); // antes das rotas
```

`app.use(cors())` sem argumentos libera **todas** as origens — suficiente para desenvolvimento em sala.

**O passo que costuma ser esquecido:** editar o código no notebook **não muda** a API que está rodando no servidor. Depois de commitar essa mudança, é preciso **reimplantar no container** — via SSH: algo como `git pull`, `npm install` (para instalar o pacote `cors`) e reiniciar o processo (`pm2 restart <nome>` ou `sudo systemctl restart <serviço>`, dependendo de como a API do grupo foi colocada no ar no 3º semestre). Confirme com o professor o comando exato do container do seu grupo.

Rode o `fetch` do console de novo (ou recarregue o front). Se o erro sumiu, está resolvido.

> **Por que o React Native nunca reclamou de CORS?** Porque CORS é uma regra **do navegador**. Um app de celular não roda num navegador, então a regra não se aplica. O mesmo `fetch` que funcionava direto no RN pode ser bloqueado na web.

> Precisar restringir a origens específicas (em vez de liberar geral), tratar os cabeçalhos extras que o encontro 9 vai exigir (`Authorization`), ou a API do grupo usa Fastify/ESM em vez de Express? O aprofundamento está em `extra-consumir-api-do-servidor.md`.

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

| Você escreve       | Significa                                                            |
| ------------------ | -------------------------------------------------------------------- |
| `[]` (array vazio) | Roda **uma vez**, quando o componente aparece pela primeira vez.     |
| `[filtro]`         | Roda na primeira vez **e** toda vez que `filtro` mudar.              |
| (omitido)          | Roda depois de **toda** renderização. Quase nunca é o que você quer. |

Para buscar a lista uma vez quando a tela carrega, o caso é `[]`.

> **Vindo do React Native:** mesmo hook, mesma assinatura, mesmas regras do array de dependências. Nada novo aqui.

---

## Parte 4 — Os três estados de uma requisição

Toda tela que busca dado de fora precisa responder três perguntas, o tempo todo: **ainda está carregando? deu certo? deu erro?**

Se você ignorar isso e só tiver a lista, a tela vai mostrar "nenhuma notificação" durante o instante em que os dados ainda estão a caminho — parece bug, mas é só falta de tratamento. E se a API cair (ou a rede da escola falhar), a tela quebra sem explicação.

### O padrão completo

```jsx
import { API_URL } from "./config";

const [notificacoes, setNotificacoes] = useState([]);
const [carregando, setCarregando] = useState(true);
const [erro, setErro] = useState(null);

useEffect(() => {
  async function buscar() {
    try {
      const resposta = await fetch(`${API_URL}/notificacoes`);
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

`API_URL` vem do módulo de configuração que vocês criam no Passo 1 da situação-problema — é a URL base da API do **seu** grupo.

Lendo o resto por partes:

- **Três estados:** a lista (começa vazia), `carregando` (começa `true` — a tela já nasce carregando), `erro` (começa `null` — sem erro).
- **`async function buscar()` dentro do `useEffect`:** a função do `useEffect` não pode ser `async` ela mesma, então declaramos uma função `async` por dentro e a chamamos (`buscar()`) na última linha.
- **`if (!resposta.ok) throw ...`:** ponto que pega todo mundo — o `fetch` **só** rejeita a Promise em falha de rede de verdade (sem internet, servidor fora do ar, fora da rede da escola). Se a API responde `404` ou `500`, o `fetch` considera "deu certo, recebi uma resposta". Você precisa checar `resposta.ok` (que é `true` para status 200–299) e lançar o erro na mão.
- **`await resposta.json()`:** lê o corpo e converte de texto JSON para objeto/array JavaScript.
- **`catch (e)`:** qualquer erro dos `await` acima cai aqui. Guardamos a mensagem em `erro`.
- **`finally`:** com erro ou sem erro, a busca terminou → `setCarregando(false)`.

### Mostrando os três estados na tela

```jsx
{
  carregando && <p className="text-gray-500">Carregando notificações...</p>;
}
{
  erro && (
    <p className="text-red-600">Não foi possível carregar. Tente novamente.</p>
  );
}
{
  !carregando && !erro && (
    <NotificationList notificacoes={notificacoesVisiveis} />
  );
}
```

Um bloco por estado. Só um aparece de cada vez. Isso é a heurística **"visibilidade do status do sistema"** (encontro 4): o usuário nunca fica sem saber o que está acontecendo.

---

## Parte 5 — Situação-problema

**Fluxo de trabalho:**

```bash
git checkout main && git pull
git checkout -b seu-nome-22-09
```

### Passo 1 — Configurar a URL da API do seu grupo

Antes de tudo, teste a URL direto — no navegador ou no terminal:

```bash
curl http://10.187.226.125:820N/notificacoes
```

(troque `820N` pela porta do seu grupo). Deve responder JSON (ou `[]`). Se der timeout, confira: rede da escola, porta certa, container ligado.

Confirmando que responde, centralize a URL. Crie, na raiz do projeto, o arquivo **`.env.local`**:

```
VITE_API_URL=http://10.187.226.125:8203
```

(troque `8203` pela porta do seu grupo). O Vite só expõe para o código variáveis que começam com `VITE_`.

Crie `src/config.js`:

```js
export const API_URL = import.meta.env.VITE_API_URL;
```

**Reinicie o `npm run dev`** — o Vite só lê arquivos `.env` na inicialização.

O `.env.local` **não vai para o Git** (confirme com `git status` que ele não aparece — o `.gitignore` do Vite já ignora `*.local`). Para o resto do grupo saber o formato, crie um `.env.example` (esse sim é commitado):

```
# URL base da API do grupo. Copie para .env.local e ajuste a porta do seu grupo.
VITE_API_URL=http://10.187.226.125:8201
```

### Passo 2 — Ligar o CORS na API do seu grupo

Aplique a correção da Parte 2: `npm install cors` + `app.use(cors())` no backend, commit, e **reimplante no container** (é a parte que costuma ser esquecida). Teste de novo a URL do Passo 1 pelo console do navegador até o erro de CORS sumir.

### Passo 3 — Trocar dados fixos por dados reais

No `App.jsx` (ou `Home.jsx`, se já tiver páginas):

- Remova a `const notificacoesIniciais` (o array escrito à mão).
- Troque `useState(notificacoesIniciais)` por `useState([])`.
- Adicione os estados `carregando` (inicial `true`) e `erro` (inicial `null`).
- Adicione o `useEffect` com a função `buscar`, usando `` `${API_URL}/notificacoes` `` (Parte 4).

O `notificacoesVisiveis` (o `.filter` do encontro passado) **continua igual** — ele filtra o que estiver em `notificacoes`, venha de onde vier.

### Passo 4 — Mostrar os três estados

Coloque os três blocos condicionais (Parte 4) no lugar onde hoje está só o `<NotificationList>`.

### Passo 5 — Testar o erro de propósito

A API do grupo é compartilhada — não dá para simplesmente desligá-la para testar. Em vez disso, **provoque o erro pela configuração**: troque a porta no `.env.local` para uma que não existe (ex.: `8299`), reinicie o `npm run dev` e recarregue. Deve aparecer a mensagem de erro amigável — **nunca** uma tela em branco ou o app quebrado. Depois volte para a porta certa, reinicie e recarregue: a lista volta.

### Passo 6 — Validação cruzada

Troque temporariamente `VITE_API_URL` no `.env.local` para a porta de **outro** grupo, reinicie e recarregue. Os dados que aparecem batem com o banco daquele grupo? Se sim, o `fetch` está mesmo lendo do valor configurado. Volte para a porta do seu grupo ao terminar.

### Passo 7 — Commit, push e Pull Request

```bash
git add .
git commit -m "busca notificações da API do grupo, trata carregando e erro"
git push origin seu-nome-22-09
```

Grupo escolhe uma branch, abre PR, revisa, faz merge. Todos rodam `git checkout main && git pull`.

---

## Erros comuns de hoje

| Sintoma                                                          | Causa provável                                                                                                                                          |
| ---------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `fetch` sempre cai no `catch` com erro de rede                   | Fora da rede da escola, porta errada no `.env.local`, ou container da API desligado. Teste a URL direto (Passo 1).                                      |
| Erro vermelho "CORS policy" no console                           | O `cors` não está ativo na API do grupo, ou a mudança não foi **reimplantada no container**. Resolve **no backend**, e precisa reiniciar o processo lá. |
| A tela pisca "nenhuma notificação" e depois mostra a lista       | Faltou o estado `carregando` — a lista vazia aparece enquanto os dados vêm.                                                                             |
| A tela fica em branco quando a API cai                           | O erro não está sendo capturado/mostrado. Confira o `try/catch` e o bloco `{erro && ...}`.                                                              |
| A busca dispara sem parar / a página trava                       | `useEffect` sem o `[]` no final, ou `fetch` chamado direto no corpo do componente.                                                                      |
| API respondeu 404 mas o código seguiu como se tivesse dado certo | Faltou `if (!resposta.ok) throw ...`. O `fetch` não lança erro para status HTTP.                                                                        |
| `dados.map is not a function`                                    | A API não devolveu um array (talvez `{ notificacoes: [...] }`). Confira o formato no `CONTRATO-API.md` e ajuste.                                        |
| `Unexpected token < in JSON`                                     | A resposta não era JSON (às vezes é uma página de erro HTML). Cheque a URL e se a API está no ar.                                                       |
| Mudei o `.env.local` e nada aconteceu                            | Faltou reiniciar o `npm run dev` — o Vite só lê `.env` na inicialização.                                                                                |
| Funciona no meu notebook, quebra no do colega                    | O colega não tem `.env.local` próprio (ele é ignorado pelo Git de propósito). Precisa copiar do `.env.example` e ajustar.                               |
| Warning "can't perform a state update on an unmounted component" | A tela trocou antes da resposta chegar. Por ora, sem problema; a forma robusta (cleanup do `useEffect`) vem depois.                                     |

---

## Checklist de encerramento

- [ ] `.env.local` criado com `VITE_API_URL` apontando para a porta do grupo; `src/config.js` exportando `API_URL`
- [ ] `.env.local` fora do controle de versão; `.env.example` commitado
- [ ] CORS habilitado na API do grupo **e reimplantado no container**
- [ ] Lista de notificações vindo do banco de dados real do grupo, via `fetch` dentro de `useEffect` com `[]`
- [ ] Estado de **carregando** visível enquanto os dados chegam
- [ ] Estado de **erro** testado de propósito (porta errada no `.env.local`) com mensagem amigável
- [ ] `resposta.ok` checado antes de usar os dados
- [ ] Filtro do encontro passado ainda funcionando sobre os dados reais
- [ ] Validação cruzada com a API de outro grupo confirmada
- [ ] Pull Request revisado e mergeado
- [ ] Todos atualizaram a `main` local antes de sair

---

## Para pensar até o próximo encontro

Hoje vocês só **pediram** para a API: "me dá a lista" — e ela deu, sem perguntar quem estava pedindo. No próximo encontro vocês vão se **apresentar** para ela: login de verdade, com o JWT que construíram no encontro 5. Depois disso, a API passa a distinguir quem pode ver de quem pode criar, editar e apagar.
