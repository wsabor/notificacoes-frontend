# Aprofundamento — indo além do encontro 8

**PEND — Programação Front-End** · opcional, para depois do encontro 8

> **Vocês não precisam de nada daqui para terminar o encontro 8.** O básico — `.env.local`, `src/config.js`, `app.use(cors())` — já está em `03-atividade-alunos.md` e é suficiente para a lista carregar com carregando/erro tratados. Venham aqui se: quiserem entender CORS com mais profundidade, a API do grupo usar Fastify em vez de Express, precisarem confirmar o CORS sem depender do navegador, ou esbarrarem num erro que a tabela do encontro 8 não cobriu. Duas partes daqui voltam a ser úteis mais pra frente — o CORS restrito por origem (encontro 9, quando entram os cabeçalhos do login) e o aviso sobre HTTPS (encontro 16, quando o front for pro ar).

---

## Endereços das APIs (referência rápida)

| Grupo | Porta | URL base da API |
| --- | --- | --- |
| 1 | 8201 | `http://10.187.226.125:8201` |
| 2 | 8202 | `http://10.187.226.125:8202` |
| 3 | 8203 | `http://10.187.226.125:8203` |
| 4 | 8204 | `http://10.187.226.125:8204` |
| 5 | 8205 | `http://10.187.226.125:8205` |
| 6 | 8206 | `http://10.187.226.125:8206` |
| 7 | 8207 | `http://10.187.226.125:8207` |
| 8 | 8208 | `http://10.187.226.125:8208` |

---

## Indo além do `cors()` básico

No encontro 8 vocês usaram `app.use(cors())`, liberando todas as origens — suficiente para a sala. Se quiserem mais controle:

### Restringir a origens conhecidas

Em vez de liberar geral, deixem só as origens que vocês reconhecem:

```js
app.use(
  cors({
    origin: [
      "http://localhost:5173", // seu front em desenvolvimento
      "http://10.187.226.125:8081", // o front do grupo publicado no Nginx da sala (encontro 16)
    ],
    methods: ["GET", "POST", "PUT", "PATCH", "DELETE"],
    allowedHeaders: ["Content-Type", "Authorization"],
  }),
);
```

- **`methods`** — precisa incluir os verbos do CRUD que vocês vão usar no encontro 9.
- **`allowedHeaders`** — precisa incluir `Authorization` (o token do login) e `Content-Type` (o corpo JSON). Sem eles, o login e as escritas vão falhar no *preflight*, mesmo com o `GET` da lista funcionando.
- A porta `8081` é a do front do grupo 1 no container de front-ends (ver o plano do semestre, seção de deploy) — ajustem para a porta do **seu** grupo.

### Se a API do grupo usa outro framework ou outro estilo de módulo

- **Fastify** em vez de Express: `npm install @fastify/cors` e `fastify.register(require('@fastify/cors'))`.
- **Express com ESM** (`import`/`export` em vez de `require`/`module.exports`): troquem `const cors = require("cors");` por `import cors from "cors";`.

### Confirmar que o CORS está realmente ativo no servidor, sem abrir o navegador

Depois de reimplantar no container, testem direto pelo terminal:

```bash
curl -i -X OPTIONS http://10.187.226.125:820N/notificacoes \
  -H "Origin: http://localhost:5173" \
  -H "Access-Control-Request-Method: POST"
```

(troquem `820N` pela porta do seu grupo). Se aparecer um cabeçalho `Access-Control-Allow-Origin` na resposta, o servidor está respondendo o *preflight* corretamente. Se o front ainda reclamar de CORS depois disso, o problema é outro — cache do navegador (dê um hard refresh), ou a origem errada na lista.

---

## Fallback para testar com uma API local (opcional)

Quiserem poder testar sem depender da rede da escola — em casa, ensaiando algo? Deem um valor padrão em `src/config.js`:

```js
export const API_URL = import.meta.env.VITE_API_URL ?? "http://localhost:3000";
```

Sem `.env.local` configurado, o projeto cai para uma API rodando no próprio notebook (se vocês tiverem uma no ar). Totalmente opcional — o fluxo do encontro 8 não depende disso.

---

## Um aviso para guardar até o encontro 16 (deploy)

- **Hoje está tudo em HTTP.** As APIs do servidor não têm HTTPS. Enquanto o front também roda em HTTP (`localhost`, ou o Nginx da sala), sem problema.
- **Quando publicarem na Vercel, isso muda.** A Vercel serve por HTTPS, e o navegador vai **bloquear** chamadas de uma página HTTPS para `http://10.187.226.125…` — é o chamado *mixed content* (página segura chamando recurso inseguro). As saídas: a API precisaria de HTTPS, ou um proxy, ou vocês mantêm a versão da Vercel apontando para outra API pública. Não é para resolver agora — é assunto do encontro 16.

---

## Outros erros que podem aparecer (além dos que já viram no encontro 8)

| Sintoma | Causa provável |
| --- | --- |
| `import.meta.env.VITE_API_URL` vem `undefined` | O nome da variável não começa com `VITE_`, ou o arquivo não se chama `.env.local` (ou `.env`) e não está na raiz do projeto. |
| `.env.local` apareceu no `git status` | O `.gitignore` não está ignorando `*.local`. Adicionem `.env.local` ao `.gitignore`. |
| Login e escritas falham no *preflight*, mas o `GET` funciona | O CORS restrito (acima) não inclui `Authorization`/`Content-Type` em `allowedHeaders`, ou o método que vocês usaram não está em `methods`. |
| Front na Vercel não carrega dados da API da escola | *Mixed content*: página HTTPS chamando API HTTP. Ver o aviso do encontro 16, acima. |
