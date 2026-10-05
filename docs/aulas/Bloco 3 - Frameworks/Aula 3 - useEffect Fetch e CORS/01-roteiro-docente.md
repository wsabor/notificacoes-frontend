# Roteiro Docente — Encontro 8 · 15/09/2026

**UC:** PEND — Programação Front-End
**Turma:** 2-2026-SESI_DEV_OC_1 · 5 aulas
**Tema:** Ciclo de vida (`useEffect`) · Consumo da API (`fetch`) · CORS

---

## O gancho que vem do encontro 1

No primeiro dia do semestre, ao validar as 8 APIs contra o contrato coletivo, uma instrução ficou registrada: se algum grupo estivesse **sem CORS habilitado**, anotar e não corrigir — "vira conteúdo no encontro 8". Hoje é esse dia. Confira suas anotações antigas antes da aula: já sabe quais grupos provavelmente vão travar primeiro no `fetch`, e pode usar isso a seu favor, deixando o erro acontecer de propósito antes de explicar.

---

## Objetivos do encontro

Ao final, o aluno deve ser capaz de:

1. Explicar o que é CORS e por que o navegador bloqueia certas requisições.
2. Usar `useEffect` para buscar dados quando o componente é montado.
3. Tratar os três estados de uma requisição: carregando, sucesso, erro.
4. Substituir dados fixos por dados reais vindos da API do próprio grupo.

**Capacidades mobilizadas**
`CT` Desenvolver interfaces web consumindo API
`CS` Demonstrar resiliência na resolução de problemas

---

## Antes da aula (preparação sua)

- [ ] Revisar suas anotações do encontro 1 — quais grupos tinham CORS desabilitado.
- [ ] Confirmar que a rota `GET` de listagem de notificações de cada grupo **não** está protegida por JWT ainda (só a rota de criação foi protegida no encontro 5) — hoje o fetch é sem token; autenticação nas escritas vem no encontro 9.

---

## Aula 1 — Por que o navegador bloqueia (CORS) (50 min)

**Estratégia:** Exposição dialogada + Demonstração provocada

### Deixe o erro acontecer primeiro (15 min)

Antes de explicar qualquer coisa, peça que um grupo com CORS pendente tente um `fetch` simples para a própria API, direto no console do navegador:

```js
fetch('http://localhost:3000/notificacoes').then(r => r.json()).then(console.log)
```

Se a API não tiver CORS habilitado, o console mostra um erro em vermelho mencionando "CORS policy" ou "Access-Control-Allow-Origin". **Deixe a turma reagir antes de explicar.**

### O que é CORS (20 min)

O navegador, por segurança, bloqueia por padrão que uma página rodando em uma origem (`http://localhost:5173`, o front-end) chame uma API rodando em outra origem (`http://localhost:3000`, ou o IP do Proxmox). Isso existe para impedir que um site malicioso qualquer chame APIs de bancos, e-mail, etc., usando a sessão da vítima sem permissão.

**Origem** = protocolo + domínio + porta. Só um desses três diferentes já conta como origem diferente.

A API precisa dizer explicitamente: "sites desta origem podem me chamar". Isso é configurado no **servidor**, não no navegador — por isso a correção é no backend de cada grupo, não no front-end.

### Corrigindo (15 min)

Na API (Express), se ainda não tiver:

```bash
npm install cors
```

```js
const cors = require('cors');
app.use(cors());
```

> `cors()` sem argumento libera qualquer origem — aceitável para o projeto de vocês. Em produção de verdade, se restringe a origens específicas.

Cada grupo testa de novo o `fetch` do console. Se o erro sumir, CORS está resolvido.

---

## Aula 2 — `useEffect` e os três estados de uma requisição (50 min)

**Estratégia:** Exposição dialogada

### `useEffect`: quando o componente "nasce" (20 min)

```jsx
useEffect(() => {
  console.log("componente montado");
}, []);
```

O array vazio `[]` no final significa "rode isso uma vez, quando o componente aparecer na tela pela primeira vez". Sem o array, roda a cada renderização — quase nunca é isso que se quer.

> **Ponte com React Native:** `useEffect` é **idêntico** ao que vocês já usam lá — não é adaptação, é o mesmo hook, a mesma sintaxe, o mesmo array de dependências. Se alguém já usou `useEffect` para buscar dados de uma API em React Native, é a mesma lógica aqui.

### Os três estados de uma requisição (30 min)

Toda tela que busca dados de fora precisa responder três perguntas: ainda está carregando? Deu certo? Deu erro?

```jsx
const [notificacoes, setNotificacoes] = useState([]);
const [carregando, setCarregando] = useState(true);
const [erro, setErro] = useState(null);

useEffect(() => {
  async function buscar() {
    try {
      const resposta = await fetch('http://localhost:3000/notificacoes');
      if (!resposta.ok) throw new Error('Erro ao buscar notificações');
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

Três pontos a martelar:

- `resposta.ok` — o `fetch` **não lança erro sozinho** em respostas `404` ou `500`. Só lança erro em falha de rede. É preciso checar `.ok` manualmente.
- `finally` — roda tanto no sucesso quanto no erro, por isso é o lugar certo para `setCarregando(false)`.
- Por que três estados e não só a lista: sem eles, a tela mostra "nenhuma notificação" por um instante mesmo quando os dados estão a caminho — parece bug, não é.

---

## Aulas 3 a 5 — Situação-problema: dados de verdade (150 min)

**Estratégia:** Situação-problema

### Fluxo de trabalho (branch/PR)

Mesma dinâmica: branch do dia, commits, PR ao final, todos atualizam a `main`.

### Parte A — Substituir os dados fixos (60 min)

Em `App.jsx`, remover `notificacoesExemplo` e usar o padrão da Aula 2 para buscar da API real do grupo (URL do `CONTRATO-API.md`, encontro 1).

### Parte B — Refletir os três estados na tela (50 min)

```jsx
{carregando && <p className="text-gray-500">Carregando notificações...</p>}
{erro && <p className="text-red-600">Não foi possível carregar. Tente novamente.</p>}
{!carregando && !erro && <NotificationList notificacoes={notificacoesFiltradas} />}
```

> **Ponte com heurísticas (encontro 4):** isso é "visibilidade do status do sistema" — o usuário nunca fica sem saber o que está acontecendo.

### Parte C — Testar o erro de propósito (20 min)

Desligar a API do próprio grupo (parar o container ou o processo por um instante) e confirmar que a mensagem de erro aparece — não uma tela em branco, nem o app quebrando.

### Validação cruzada e Pull Request (20 min)

Grupos conferem uns aos outros: a listagem reflete os dados reais do banco de cada grupo? Depois, fluxo normal de branch/PR.

---

## O que observar circulando

| Sinal | Ação |
|---|---|
| Grupo travado em erro de CORS ainda | Voltar à Aula 1 — provavelmente esqueceram de reiniciar a API depois de instalar `cors` |
| `fetch` sem `try/catch` | Perguntar: "o que acontece se a internet cair no meio dessa chamada?" |
| Checagem de erro via `.catch()` do fetch, sem checar `resposta.ok` | Mostrar que uma resposta `404` chega ao `.then()`, não ao `.catch()` |
| Estado de carregamento nunca desliga | Verificar se o `finally` foi esquecido |

---

## Fechamento em sala (últimos 15 min)

Sem tarefa de casa. Antes de liberar, cada grupo precisa ter:

- [ ] CORS habilitado na própria API
- [ ] Lista de notificações vindo de verdade do banco do grupo, via `fetch`
- [ ] Estados de carregamento e erro visíveis na tela
- [ ] Erro testado de propósito (API desligada) e mensagem amigável confirmada
- [ ] Pull Request revisado e mergeado
- [ ] Todos atualizaram a `main` local antes de sair

---

## Recursos

Quadro branco · Projetor · Computadores dos alunos · Console do navegador (DevTools) · `CONTRATO-API.md` de cada grupo · Servidor Proxmox (LXC)

## Ponte para o encontro 9 (22/09)

Fechar com: *"Hoje vocês só perguntaram para a API — 'me dá a lista'. Semana que vem vocês vão se apresentar para ela — login de verdade, com o JWT que construímos no encontro 5 — e aí ela deixa vocês criar, editar e apagar."*
