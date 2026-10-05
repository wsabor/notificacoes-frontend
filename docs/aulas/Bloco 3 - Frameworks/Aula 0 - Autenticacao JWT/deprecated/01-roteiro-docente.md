# Roteiro Docente — Encontro 5 · 25/08/2026

**UC:** PEND — Programação Front-End (conteúdo de backend, aula-ponte)
**Turma:** 2-2026-SESI_DEV_OC_1 · 5 aulas
**Tema:** Autenticação JWT na API de Notificações — live coding conjunto

---

## Por que esta aula existe

O encontro 9 (22/09) pressupõe uma API que autentica por JWT. Ela nunca foi implementada — ficou documentada como decisão de design em PSOF, mas o código nunca saiu do papel. Esta aula fecha essa lacuna **antes** que ela vire um bloqueio.

Formato: você codifica ao vivo, projetado, narrando cada decisão. Os 8 grupos replicam em paralelo, cada um na própria API. Não é atividade de descoberta — é demonstração guiada com prática imediata.

---

## Objetivos do encontro

Ao final, cada grupo deve ter, na própria API:

1. Endpoint de registro que cria usuário com senha *hasheada*.
2. Endpoint de login que valida credenciais e devolve um token JWT.
3. Middleware que valida o token e bloqueia acesso sem ele.
4. Pelo menos uma rota existente protegida por esse middleware.
5. Tudo testado e funcionando no Postman/Insomnia.

**Fora do escopo de hoje:** refresh token, permissões por papel, recuperação de senha, login social. Se surgir a pergunta, valide a curiosidade e adie — "isso é semana que vem" não se aplica aqui porque não está no cronograma, então diga: "isso existe, mas é assunto para outro momento; hoje o objetivo é o básico funcionando de ponta a ponta."

---

## Antes da aula (preparação sua)

- [ ] Abrir o model de usuário/participante da **sua** API de referência e confirmar os nomes reais dos campos (nome, e-mail, senha). Os nomes usados neste roteiro (`Usuario`, `email`, `senha`) são genéricos — ajuste ao vivo para o que já existe.
- [ ] Confirmar que `bcryptjs` e `jsonwebtoken` não estão instalados ainda (ou já estão — nesse caso, pule a instalação e vá direto ao código).
- [ ] Ter um `.env` de exemplo pronto para mostrar onde entra o segredo do JWT.
- [ ] Testar o roteiro inteiro uma vez, na sua máquina, antes da aula. É código ao vivo — imprevistos acontecem menos quando você já passou por eles sozinho.

---

## Aula 1 — Por que JWT, e o que ele resolve (50 min)

**Estratégia:** Exposição dialogada

### O problema que autenticação resolve (15 min)

HTTP não lembra de nada entre uma requisição e outra. Sem autenticação, qualquer pessoa que souber a URL de uma rota consegue chamá-la — não existe "usuário logado" do ponto de vista do servidor.

Pergunta disparadora: *"Quando vocês fazem login em um site, o navegador manda a senha em toda requisição seguinte?"* A resposta certa é não — e é aí que entra o token.

### O que é JWT (20 min)

**JWT (JSON Web Token)**: um crachá digital. No login, o servidor confere a identidade uma vez e devolve um token assinado. Nas requisições seguintes, o cliente manda esse token em vez da senha — e o servidor confia nele porque só o servidor sabe validar a assinatura.

Estrutura de um JWT — três partes separadas por ponto:

```
header.payload.signature
```

- **Header**: algoritmo usado.
- **Payload**: dados do usuário (ex: id, e-mail) — **não é secreto**, qualquer um decodifica. Nunca colocar senha aqui.
- **Signature**: garante que o token não foi adulterado. Só quem tem o segredo (`JWT_SECRET`) consegue gerar uma assinatura válida.

Mostre ao vivo em [jwt.io](https://jwt.io) um token de exemplo sendo decodificado — o efeito de "é só um texto codificado, não criptografado" costuma surpreender.

### Hash de senha (15 min)

Nunca se guarda senha em texto puro no banco. `bcryptjs` transforma a senha em um hash irreversível — mesmo que o banco vaze, ninguém recupera a senha original.

```js
const hash = await bcrypt.hash(senha, 10);
// no login:
const confere = await bcrypt.compare(senhaDigitada, hash);
```

O número `10` é o "custo" do hash — quanto maior, mais lento e mais seguro. `10` é um padrão razoável para projeto de estudo.

---

## Aulas 2 a 5 — Live coding (200 min)

**Estratégia:** Live coding conjunto — você digita projetado, a turma acompanha na própria API, pausando em cada checkpoint para todo mundo alcançar.

### Checkpoint 0 — Instalação (10 min)

```bash
npm install bcryptjs jsonwebtoken
```

No `.env`, adicionar:

```
JWT_SECRET=uma-frase-longa-dificil-de-adivinhar
JWT_EXPIRES_IN=1h
```

> Pergunte à turma por que o segredo fica no `.env` e não no código. Deixe alguém responder antes de confirmar: se o `JWT_SECRET` vazar no GitHub, qualquer pessoa consegue forjar tokens válidos.

### Checkpoint 1 — Confirmar a tabela de usuário existente (15 min)

Antes de escrever qualquer rota nova, cada grupo abre o próprio model e confirma os campos reais. Ajuste os nomes dos exemplos abaixo em tempo real, conforme o que aparecer na tela.

```js
// exemplo — ajustar para os campos reais do model de cada grupo
{
  id: INTEGER,
  nome: STRING,
  email: STRING,
  senha: STRING   // se ainda não existir, criar via migration/alter agora
}
```

Se o campo de senha não existir ainda, este é o momento de criar (migration ou `sync({ alter: true })`, conforme o que a API de cada grupo já usa).

### Checkpoint 2 — Rota de registro (35 min)

```js
// controllers/authController.js
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
      // nunca devolver a senha, nem o hash
    });
  } catch (erro) {
    return res.status(500).json({ erro: 'Erro ao registrar usuário' });
  }
}

module.exports = { registrar };
```

```js
// routes/authRoutes.js
const express = require('express');
const router = express.Router();
const { registrar } = require('../controllers/authController');

router.post('/registro', registrar);

module.exports = router;
```

**Testar agora no Postman**, antes de seguir: `POST /auth/registro` com nome/email/senha. Confirmar que a resposta não devolve a senha nem o hash.

### Checkpoint 3 — Rota de login (40 min)

```js
// controllers/authController.js (acrescentar)
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
// routes/authRoutes.js (acrescentar)
router.post('/login', login);
```

> **Ponto de discussão:** por que a mensagem de erro é igual para "e-mail não existe" e "senha errada"? Deixe a turma tentar responder antes de confirmar: se as mensagens fossem diferentes, alguém malicioso descobriria quais e-mails estão cadastrados só tentando logar.

**Testar agora**: `POST /auth/login` com as credenciais criadas no checkpoint anterior. Copiar o token da resposta — vai ser usado no próximo checkpoint.

### Checkpoint 4 — Middleware de proteção (40 min)

```js
// middlewares/autenticar.js
const jwt = require('jsonwebtoken');

function autenticar(req, res, next) {
  const authHeader = req.headers.authorization;

  if (!authHeader) {
    return res.status(401).json({ erro: 'Token não fornecido' });
  }

  const [, token] = authHeader.split(' '); // formato: "Bearer <token>"

  try {
    const payload = jwt.verify(token, process.env.JWT_SECRET);
    req.usuario = payload; // disponível nas rotas seguintes
    next();
  } catch (erro) {
    return res.status(401).json({ erro: 'Token inválido ou expirado' });
  }
}

module.exports = autenticar;
```

Aplicar em **uma** rota existente, como demonstração:

```js
// routes/notificacaoRoutes.js
const autenticar = require('../middlewares/autenticar');

router.post('/notificacoes', autenticar, criarNotificacao);
```

**Testar agora, três vezes:**

1. `POST /notificacoes` **sem** header `Authorization` → deve retornar `401`.
2. `POST /notificacoes` com header `Authorization: Bearer token-invalido` → deve retornar `401`.
3. `POST /notificacoes` com header `Authorization: Bearer <token-real-do-login>` → deve funcionar normalmente.

Esse teste triplo é o critério de aceitação da aula.

### Checkpoint 5 — Consolidação (20 min)

Cada grupo:

- [ ] Roda os três testes do Checkpoint 4 e confirma os resultados.
- [ ] Faz commit e push das alterações.
- [ ] Atualiza o `CONTRATO-API.md` do grupo (criado no encontro 1) com as duas novas rotas — `POST /auth/registro` e `POST /auth/login` — seguindo o mesmo formato usado desde então.

---

## O que observar circulando

| Sinal | Ação |
|---|---|
| Grupo copiando sem entender o `bcrypt.compare` | Perguntar: "por que não dá para comparar a senha digitada direto com o hash guardado?" |
| Grupo esquecendo de proteger a rota de teste (Checkpoint 4) | Sem isso, não há como validar que o middleware funciona — insistir no teste triplo |
| `JWT_SECRET` fraco ou commitado no código | Corrigir na hora — é uma vulnerabilidade real, não só didática |
| Grupo terminando cedo | Desafio extra: proteger uma segunda rota (ex: `DELETE /notificacoes/:id`) sozinhos, sem o roteiro |

---

## Fechamento em sala (últimos 10 min)

Sem tarefa de casa. Antes de liberar, cada grupo precisa ter:

- [ ] Registro e login funcionando, testados no Postman
- [ ] Middleware protegendo pelo menos uma rota
- [ ] Teste triplo (sem token / token inválido / token válido) executado e com resultado correto
- [ ] `CONTRATO-API.md` atualizado
- [ ] Push feito

---

## Recursos

Quadro branco · Projetor · Computadores dos alunos · Postman/Insomnia · jwt.io (para demonstração) · Servidor Proxmox (LXC) · Repositórios das APIs dos grupos

## Ponte para o encontro 6 (01/09)

Fechar com: *"A partir de hoje, toda API de vocês sabe dizer 'não' para quem não devia entrar. Nas próximas semanas, o React vai aprender a pedir licença antes de entrar — com o mesmo token que vocês acabaram de criar."*
