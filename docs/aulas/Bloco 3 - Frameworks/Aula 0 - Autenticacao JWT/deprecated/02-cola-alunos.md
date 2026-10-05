# Cola do Aluno — JWT na API de Notificações

**Encontro 5 · 25/08/2026**

Este documento acompanha o live coding de hoje. Vocês não precisam copiar tudo do quadro — o código já está aqui. Foquem em entender **por que** cada trecho existe, não só em digitar.

> ⚠️ Os nomes de campos abaixo (`Usuario`, `email`, `senha`) são exemplo. Confiram os nomes reais no model de vocês e ajustem.

---

## O que é JWT, em uma frase

Um crachá digital: o servidor confere sua identidade uma vez, no login, e devolve um token. Nas requisições seguintes, vocês mostram o token em vez da senha.

Um JWT tem três partes: `header.payload.signature`. O payload não é secreto — qualquer um decodifica em [jwt.io](https://jwt.io). O que garante que ninguém adultera o token é a assinatura, gerada com um segredo que só o servidor conhece.

---

## Passo 0 — Instalação

```bash
npm install bcryptjs jsonwebtoken
```

No `.env`:

```
JWT_SECRET=uma-frase-longa-dificil-de-adivinhar
JWT_EXPIRES_IN=1h
```

## Passo 1 — Conferir a tabela de usuário

Abram o model. Confirmem se já existe campo de senha. Se não existir, criem agora (migration ou alteração de tabela, como a API de vocês já costuma fazer).

## Passo 2 — Registro

```js
const bcrypt = require('bcryptjs');
const { Usuario } = require('../models');

async function registrar(req, res) {
  try {
    const { nome, email, senha } = req.body;

    const jaExiste = await Usuario.findOne({ where: { email } });
    if (jaExiste) {
      return res.status(409).json({ erro: 'E-mail já cadastrado' });
    }

    const senhaHash = await bcrypt.hash(senha, 10);
    const usuario = await Usuario.create({ nome, email, senha: senhaHash });

    return res.status(201).json({
      id: usuario.id,
      nome: usuario.nome,
      email: usuario.email
    });
  } catch (erro) {
    return res.status(500).json({ erro: 'Erro ao registrar usuário' });
  }
}

module.exports = { registrar };
```

```js
router.post('/registro', registrar);
```

**Testar:** `POST /auth/registro` no Postman. A resposta não deve conter senha nem hash.

## Passo 3 — Login

```js
const jwt = require('jsonwebtoken');

async function login(req, res) {
  try {
    const { email, senha } = req.body;

    const usuario = await Usuario.findOne({ where: { email } });
    if (!usuario) {
      return res.status(401).json({ erro: 'Credenciais inválidas' });
    }

    const senhaConfere = await bcrypt.compare(senha, usuario.senha);
    if (!senhaConfere) {
      return res.status(401).json({ erro: 'Credenciais inválidas' });
    }

    const token = jwt.sign(
      { id: usuario.id, email: usuario.email },
      process.env.JWT_SECRET,
      { expiresIn: process.env.JWT_EXPIRES_IN }
    );

    return res.status(200).json({ token });
  } catch (erro) {
    return res.status(500).json({ erro: 'Erro ao autenticar' });
  }
}

module.exports = { registrar, login };
```

```js
router.post('/login', login);
```

**Testar:** `POST /auth/login`. Copiem o token da resposta — vão usar no próximo passo.

> Reparem: a mensagem de erro é igual para "e-mail não existe" e "senha errada". Por quê? *(pensem antes de perguntar ao professor)*

## Passo 4 — Middleware de proteção

```js
const jwt = require('jsonwebtoken');

function autenticar(req, res, next) {
  const authHeader = req.headers.authorization;

  if (!authHeader) {
    return res.status(401).json({ erro: 'Token não fornecido' });
  }

  const [, token] = authHeader.split(' '); // formato: "Bearer <token>"

  try {
    const payload = jwt.verify(token, process.env.JWT_SECRET);
    req.usuario = payload;
    next();
  } catch (erro) {
    return res.status(401).json({ erro: 'Token inválido ou expirado' });
  }
}

module.exports = autenticar;
```

Aplicar em uma rota existente:

```js
const autenticar = require('../middlewares/autenticar');

router.post('/notificacoes', autenticar, criarNotificacao);
```

## Passo 5 — Teste triplo (obrigatório)

No Postman, testem `POST /notificacoes` três vezes:

| # | Como | Resultado esperado |
|---|---|---|
| 1 | Sem header `Authorization` | `401` |
| 2 | `Authorization: Bearer token-invalido` | `401` |
| 3 | `Authorization: Bearer <token-real-do-login>` | Funciona normalmente |

> No Postman, o header vai em **Headers**: chave `Authorization`, valor `Bearer <token>` (com espaço entre "Bearer" e o token).

---

## Checklist de encerramento

- [ ] Registro funcionando (senha nunca aparece na resposta)
- [ ] Login funcionando (retorna token)
- [ ] Middleware criado
- [ ] Pelo menos uma rota protegida
- [ ] Teste triplo com os três resultados corretos
- [ ] `CONTRATO-API.md` atualizado com as rotas `/auth/registro` e `/auth/login`
- [ ] Push feito

---

## Para pensar até o próximo encontro

A partir de hoje, a API de vocês sabe dizer "não" para quem não devia entrar. Em breve, o React vai aprender a mostrar esse mesmo token nas próprias requisições — vocês estão construindo os dois lados da mesma conversa.
