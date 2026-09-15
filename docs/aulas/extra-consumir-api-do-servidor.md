# Material extra — Consumir a API do grupo hospedada no servidor da escola

**PEND — Programação Front-End** · complemento dos encontros 8 e 9

> **Quando usar:** depois de ter o `fetch` funcionando contra uma API local (encontro 8). Este material troca o alvo do `fetch`: em vez da API rodando no seu notebook, o front-end passa a consumir a API do **seu grupo** que já está no servidor da escola.

---

## Por que fazer isso

Até agora o `fetch` aponta para `http://localhost:3000` — e isso só funciona porque a API está rodando **na mesma máquina** que o front-end. `localhost` quer dizer "esta máquina, aqui".

No servidor da escola, cada grupo tem um container LXC com a sua API já no ar. Apontar o front-end para lá tem três ganhos:

- **Realismo** — front-end e API em máquinas diferentes, comunicando pela rede, que é como funciona de verdade em produção.
- **Independência** — você desenvolve a interface sem precisar subir a API no seu notebook.
- **CORS de verdade** — a origem passa a ser mesmo outra (outro IP, outra porta), então o tratamento de CORS do encontro 8 deixa de ser exercício e passa a valer.

---

## Pré-requisitos

1. **Estar na rede interna da escola.** O endereço `IP_DO_SERVIDOR` (ver abaixo) é um IP **privado** — só existe dentro da rede da escola. De casa, sem VPN, não há como alcançar; o `fetch` vai dar timeout.
2. **A API do seu grupo estar no ar.** Teste antes de mexer no front (Passo 1).
3. **A API do grupo precisa aceitar CORS.** A API foi construída no 3º semestre e pode **não** ter o `cors` habilitado. Se for o caso, vocês vão precisar atualizar o código da API **e reimplantar o container** — não basta o `cors` que vocês adicionaram na cópia local no encontro 8. Ver Passo 4.

---

## Endereços das APIs

Um IP, uma porta por grupo. **Confirme o `IP_DO_SERVIDOR` com o professor** — é o endereço do host na rede da escola. Onde este material escreve `IP_DO_SERVIDOR`, use o valor real.

| Grupo | Porta | URL base da API |
| --- | --- | --- |
| 1 | 8201 | `http://IP_DO_SERVIDOR:8201` |
| 2 | 8202 | `http://IP_DO_SERVIDOR:8202` |
| 3 | 8203 | `http://IP_DO_SERVIDOR:8203` |
| 4 | 8204 | `http://IP_DO_SERVIDOR:8204` |
| 5 | 8205 | `http://IP_DO_SERVIDOR:8205` |
| 6 | 8206 | `http://IP_DO_SERVIDOR:8206` |
| 7 | 8207 | `http://IP_DO_SERVIDOR:8207` |
| 8 | 8208 | `http://IP_DO_SERVIDOR:8208` |

> Se o setup do seu container for diferente (IP próprio por container, ou um subcaminho em vez de porta), ajuste a URL base — o resto do material continua igual.

---

## Passo 1 — Testar a URL da sua API antes de tocar no front

Grupo 3, por exemplo. No navegador, abra direto:

```
http://IP_DO_SERVIDOR:8203/notificacoes
```

Ou no terminal:

```bash
curl http://IP_DO_SERVIDOR:8203/notificacoes
```

O que você espera ver: o JSON da lista de notificações (ou `[]` se o banco estiver vazio). Se der **timeout** ou "não foi possível conectar":

- Confirme que você está na rede da escola.
- Confirme a porta do seu grupo.
- Confirme que o container da API está ligado.

Só siga quando essa URL responder JSON.

---

## Passo 2 — Centralizar a URL base num único lugar

Hoje, `http://localhost:3000` está **repetido** em vários `fetch` (lista, login, criar, atualizar, apagar). Trocar um por um é onde o erro mora. Vamos deixar a URL em **um** lugar.

### 2.1 — Variável de ambiente do Vite

O Vite lê variáveis de arquivos `.env`. Só expõe para o código as que começam com **`VITE_`**.

Crie, na raiz do projeto (mesmo nível do `package.json`), o arquivo **`.env.local`**:

```
VITE_API_URL=http://IP_DO_SERVIDOR:8203
```

(troque `IP_DO_SERVIDOR` pelo IP real e `8203` pela porta do seu grupo)

### 2.2 — `.env.local` não vai para o Git

Cada integrante do grupo pode estar testando uma porta diferente, e o valor não deve ir para o repositório. O `.gitignore` do projeto Vite já ignora `*.local` — confirme rodando `git status` e vendo que `.env.local` **não** aparece.

Para o próximo colega saber o formato, crie um **`.env.example`** (esse **vai** para o Git):

```
# URL base da API do SEU grupo. Copie este arquivo para .env.local e ajuste a porta.
# Grupo 1 = 8201, Grupo 2 = 8202, ... Grupo 8 = 8208
VITE_API_URL=http://IP_DO_SERVIDOR:8201
```

### 2.3 — Um módulo de configuração

Crie `src/config.js`:

```js
export const API_URL = import.meta.env.VITE_API_URL ?? "http://localhost:3000";
```

- `import.meta.env.VITE_API_URL` é o valor que veio do `.env.local`.
- `?? "http://localhost:3000"` é o **padrão**: se ninguém configurou o `.env.local`, o projeto continua funcionando contra uma API local, como no encontro 8.

### 2.4 — Reiniciar o servidor

O Vite só lê os arquivos `.env` **na inicialização**. Depois de criar ou mudar o `.env.local`, pare o `npm run dev` (Ctrl+C) e rode de novo.

---

## Passo 3 — Trocar os `fetch`

Onde estava a URL fixa, agora entra a `API_URL`.

**Antes:**

```jsx
const resposta = await fetch("http://localhost:3000/notificacoes");
```

**Depois:**

```jsx
import { API_URL } from "./config"; // ajuste o caminho conforme a pasta do arquivo

const resposta = await fetch(`${API_URL}/notificacoes`);
```

Faça o mesmo em **todos** os `fetch` do projeto:

| Onde | Antes | Depois |
| --- | --- | --- |
| Lista (encontro 8) | `fetch("http://localhost:3000/notificacoes")` | `fetch(\`${API_URL}/notificacoes\`)` |
| Login (encontro 9) | `fetch("http://localhost:3000/auth/login", …)` | `fetch(\`${API_URL}/auth/login\`, …)` |
| Criar / atualizar / apagar (encontro 9) | `fetch("http://localhost:3000/notificacoes…", …)` | `fetch(\`${API_URL}/notificacoes…\`, …)` |

Dica: busque no projeto por `localhost:3000` e confirme que **nenhuma** ocorrência sobrou.

---

## Passo 4 — Ajustar o CORS na API do 3º semestre

Com o front em `http://localhost:5173` e a API em `http://IP_DO_SERVIDOR:820N`, a origem é **mesmo** outra. O navegador vai bloquear a resposta a menos que a API autorize — e a API de vocês foi escrita no 3º semestre, **antes** de existir um front-end para consumi-la, então quase certamente ela ainda não trata CORS.

### 4.1 — Ver o estado atual

No repositório do **backend**, procure por `cors` no `package.json` e no arquivo principal (`app.js` / `server.js` / `index.js`). Se não houver nada, é o esperado — siga para o 4.2.

### 4.2 — Adicionar o middleware

```bash
npm install cors
```

No arquivo principal, **antes** das rotas:

```js
const cors = require("cors");

app.use(cors()); // libera todas as origens
```

`app.use(cors())` sem argumentos responde com `Access-Control-Allow-Origin: *` e já trata a requisição de *preflight* (`OPTIONS`) que o navegador dispara antes de um `POST`/`PUT`/`DELETE` com cabeçalhos. Para desenvolvimento em sala, é suficiente.

> Se o projeto usa **Fastify** em vez de Express: `npm install @fastify/cors` e `fastify.register(require('@fastify/cors'))`. Se usa **Express com ESM** (`import`), troque o `require` por `import cors from "cors";`.

### 4.3 — (Opcional) Restringir às origens conhecidas

Se preferir não deixar aberto para todo mundo:

```js
app.use(cors({
  origin: [
    "http://localhost:5173",              // front em desenvolvimento
    "http://IP_DO_SERVIDOR:8081",         // front do grupo publicado no Nginx da sala (encontro 16)
  ],
  methods: ["GET", "POST", "PUT", "PATCH", "DELETE"],
  allowedHeaders: ["Content-Type", "Authorization"],
}));
```

- **`methods`** — precisa incluir os verbos do CRUD do encontro 9.
- **`allowedHeaders`** — precisa incluir `Authorization` (o token do login) e `Content-Type` (o corpo JSON). Sem eles, o login e as escritas falham no *preflight*, mesmo com o `GET` da lista funcionando.
- A porta `8081` é a do front do grupo 1 no container de front-ends (ver plano do semestre, seção de deploy); ajuste para a do seu grupo.

### 4.4 — Reimplantar no container

**Este é o passo que costuma ser esquecido.** Editar o código no notebook não muda a API que está rodando no servidor. Depois de commitar a mudança do CORS, é preciso atualizar o container do grupo:

```bash
# dentro do container LXC da API do grupo (via SSH):
cd <pasta-da-api>
git pull
npm install          # instala o pacote cors
# reiniciar o processo da API:
pm2 restart <nome-do-processo>     # se estiver usando pm2
# ou: sudo systemctl restart <servico-da-api>   # se for um serviço systemd
```

(O comando exato depende de como a API do grupo foi colocada para rodar no 3º semestre — `pm2`, `systemd`, `screen`, etc. Confirme com o professor.)

### 4.5 — Confirmar

Do notebook, na rede da escola:

```bash
curl -i -X OPTIONS http://IP_DO_SERVIDOR:820N/notificacoes \
  -H "Origin: http://localhost:5173" \
  -H "Access-Control-Request-Method: POST"
```

Na resposta deve aparecer um cabeçalho `Access-Control-Allow-Origin`. Se apareceu, o CORS está ativo no servidor. Recarregue o front e o erro de "CORS policy" deve ter sumido.

---

## Passo 5 — Testar

1. **Sua API:** `.env.local` com a porta do seu grupo, `npm run dev` reiniciado, tela carregando a lista do servidor. Os dados batem com o banco do seu grupo?
2. **Erro de rede tratado:** coloque uma porta errada no `.env.local` (ex.: `8299`), reinicie e recarregue. A tela deve mostrar a **mensagem de erro amigável** do encontro 8 — não uma tela em branco. Depois volte a porta certa.
3. **Validação cruzada:** troque temporariamente o `VITE_API_URL` para a porta de **outro** grupo, reinicie e recarregue. A lista muda para os dados daquele grupo? Se sim, o `fetch` está mesmo lendo do valor configurado. Volte para a sua porta ao terminar.

---

## Cuidados

- **Rede da escola apenas.** Fora dela (casa, celular 4G), o `IP_DO_SERVIDOR` não existe e o `fetch` dá timeout. Isso não é bug do código.
- **Tudo em HTTP.** As APIs do servidor não têm HTTPS. Enquanto o front-end também roda em HTTP (`localhost` ou o Nginx da sala), tudo bem. Quando vocês publicarem o front na **Vercel** (encontro 16), que serve por HTTPS, o navegador vai **bloquear** chamadas a `http://IP_DO_SERVIDOR…` como *mixed content*. Aí as opções são: API com HTTPS, um proxy, ou manter a versão Vercel apontando para outra API pública. É assunto do encontro 16 — por ora, ficar no HTTP.
- **Nunca commite `.env.local`.** O `.env.example` no repositório documenta o formato; o valor real fica só na máquina de cada um.
- **Reinicie o `npm run dev`** a cada mudança em arquivo `.env`.

---

## Erros comuns

| Sintoma | Causa provável |
| --- | --- |
| `fetch` sempre cai no `catch` com erro de rede | Fora da rede da escola, porta errada, ou container da API desligado. Teste a URL direto no navegador (Passo 1). |
| Erro "CORS policy" no console | A API do servidor não tem `cors` habilitado, ou a mudança não foi reimplantada no container. Resolve **no backend**. |
| Mudei o `.env.local` e nada aconteceu | Faltou reiniciar o `npm run dev`. |
| `import.meta.env.VITE_API_URL` vem `undefined` | O nome da variável não começa com `VITE_`, ou o arquivo não é `.env.local` / `.env` na raiz do projeto. |
| Funciona no meu PC, quebra no do colega | O colega não criou o `.env.local` dele (foi ignorado pelo Git, de propósito). Ele precisa copiar do `.env.example`. |
| `.env.local` apareceu no `git status` | O `.gitignore` não está ignorando `*.local`. Adicione `.env.local` ao `.gitignore`. |
| Front na Vercel não carrega dados da API da escola | *Mixed content*: página HTTPS chamando API HTTP. Ver "Cuidados". |

---

## Checklist

- [ ] URL da API do grupo testada direto no navegador/`curl`, respondendo JSON
- [ ] `.env.local` criado com `VITE_API_URL` apontando para a porta do grupo
- [ ] `.env.local` **fora** do controle de versão; `.env.example` commitado
- [ ] `src/config.js` exportando `API_URL` com fallback para `localhost`
- [ ] Todos os `fetch` usando `API_URL` — nenhuma ocorrência de `localhost:3000` sobrando
- [ ] `cors` habilitado na API do servidor e **reimplantado** no container
- [ ] Lista carregando do servidor; erro de rede testado e mensagem amigável confirmada
- [ ] Validação cruzada com a API de outro grupo funcionando ao trocar o valor
