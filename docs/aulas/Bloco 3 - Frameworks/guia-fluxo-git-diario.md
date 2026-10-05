# Guia — Fechando e abrindo o dia de trabalho no Git

**PEND — Programação Front-End** · fluxo de todo encontro, do primeiro ao último

> Isto não é conteúdo novo — é o mesmo "Fluxo de trabalho" que aparece resumido no início da situação-problema de cada encontro. Aqui está destrinchado, com o porquê de cada passo, para consultar sempre que tiver dúvida na hora de fechar ou abrir o dia.

---

## Por que esse ritual existe

Sem ele, dois problemas certos aparecem:

- **Trabalho se perde.** Se ninguém commita e a máquina trava, ou o Codespace/ambiente reinicia, o dia de trabalho evapora.
- **A turma trabalha em cima de código velho.** Se o grupo não atualiza a `main` antes de criar a branch do dia seguinte, cada um parte de um ponto diferente — e é exatamente aí que nascem os conflitos difíceis de resolver.

O ritual tem duas metades: **fechar o dia** (commit → PR → merge) e **abrir o dia seguinte** (atualizar `main` → nova branch). Uma sem a outra não funciona.

---

## Parte A — Fechando o dia

### Passo 1 — Ver o que mudou

```bash
git status
```

Confira se os arquivos listados são mesmo os que você esperava mexer. Se aparecer algo estranho (ex.: `node_modules/`, um arquivo `.env.local`), é sinal de `.gitignore` incompleto — resolva antes de continuar.

### Passo 2 — Commit

```bash
git add .
git commit -m "mensagem curta descrevendo o que foi feito"
```

A mensagem começa com um verbo e diz **o que** mudou, não "correções" ou "ajustes":

- ✅ `extrai FilterBar e NotificationList, adiciona formulário controlado`
- ✅ `busca notificações da API do grupo, trata carregando e erro`
- ❌ `mudanças do dia`
- ❌ `wip`

Se o dia rendeu partes bem diferentes (ex.: um componente **e** uma correção de bug sem relação), vale fazer **dois commits** em vez de um só — cada commit deveria contar uma coisa.

### Passo 3 — Push

```bash
git push origin sua-branch
```

Na primeira vez que a branch é enviada, o Git costuma sugerir o comando completo com `-u` — pode copiar e colar, ele só lembra o Git de que aquele é o destino padrão dessa branch dali em diante.

### Passo 4 — O grupo escolhe uma branch

Cada integrante trabalhou na própria branch. Nos últimos minutos do encontro, o grupo **compara as versões** e escolhe **uma** para virar Pull Request — geralmente a mais completa, ou a que sobrou tempo de revisar. As outras branches não são descartadas: continuam existindo, só não vão para a `main` hoje.

### Passo 5 — Abrir o Pull Request

No GitHub, na aba **Pull requests** → **New pull request** → escolher a branch escolhida no Passo 4 como origem e `main` como destino. Descrever em 2-3 linhas o que foi feito — não precisa ser longo, mas quem revisa não deveria precisar adivinhar.

(Quem preferir terminal: `gh pr create` faz a mesma coisa, se o `gh` CLI estiver instalado.)

### Passo 6 — Revisar

Antes de aprovar, confira:

- [ ] O projeto roda (`npm run dev`) sem erro no console
- [ ] O que o PR promete fazer é o que o código faz
- [ ] Nenhum arquivo estranho foi incluído (`.env.local`, `node_modules/`, etc.)

### Passo 7 — Merge

Botão **Merge pull request** no GitHub. Prefira **Squash and merge** quando a branch tiver muitos commits pequenos ("ajusta", "ajusta de novo") — ele junta tudo num commit só na `main`, deixando o histórico principal mais limpo. Sem preferência definida, o merge comum também resolve.

### Passo 8 — Apagar a branch (opcional)

O próprio GitHub oferece o botão **Delete branch** depois do merge. Pode apagar sem medo — o código já está na `main`; a branch antiga não faz falta.

---

## Parte B — Abrindo o dia seguinte

**Todo integrante do grupo faz isso, não só quem abriu o PR** — inclusive quem não teve a branch escolhida no dia anterior.

### Passo 1 — Voltar para a `main`

```bash
git checkout main
```

### Passo 2 — Puxar o que foi mesclado

```bash
git pull
```

Isso traz o PR de ontem (e de qualquer outro colega que já tenha mesclado algo) para o seu computador. **Pular este passo é a causa nº 1 de conflito feio** — se você criar a branch de hoje sem antes puxar, ela nasce de um ponto desatualizado, e mais cedo ou mais tarde alguém vai ter que resolver na unha o que já estava resolvido.

### Passo 3 — Criar a branch do dia

```bash
git checkout -b seu-nome-DD-MM
```

Com a data de hoje. A partir daqui, trabalhe, commite, dê `push` — e no fim do dia o ciclo da Parte A recomeça.

---

## O ciclo completo, resumido

```
   dia N                                   dia N+1
┌──────────────┐                       ┌──────────────┐
│ trabalha na  │  commit → push        │ checkout main│
│ branch do dia│ ───────────────────►  │ pull         │
└──────────────┘                       │ checkout -b  │
       │                               │ (nova branch)│
       │ grupo escolhe 1 branch        └──────────────┘
       ▼
   Pull Request → revisão → merge na main
```

---

## Erros comuns

| Sintoma | Causa provável |
| --- | --- |
| Commitei direto na `main` sem querer | Só acontece se ninguém criou a branch do dia primeiro. Se ainda não deu `push`, `git checkout -b nome-do-dia` agora mesmo move o commit para a branch nova, sem perder nada. |
| `git pull` pede para resolver conflito | Alguém mudou as mesmas linhas que você, em commits diferentes. Abra os arquivos marcados, decida o que fica, `git add` neles e `git commit` para fechar o merge. |
| Esqueci de dar `git pull` e já criei a branch de hoje | Dá para consertar: `git merge main` dentro da sua branch traz as atualizações que faltavam (pode gerar os mesmos conflitos do item acima, resolvidos uma vez só). |
| Minha branch não aparece no GitHub | Faltou o `git push origin sua-branch` (Passo 3) — commitar local não envia nada sozinho. |
| PR mostra arquivos que eu não mexi | Sua branch está desatualizada em relação à `main`. Rode `git checkout main && git pull`, volte para sua branch e dê `git merge main`. |

---

## Checklist de fechamento de dia

- [ ] `git status` conferido — só os arquivos esperados
- [ ] Commit com mensagem descritiva (não "ajustes")
- [ ] `git push` feito — a branch aparece no GitHub
- [ ] Grupo escolheu qual branch vira PR
- [ ] PR aberto, revisado e mergeado na `main`
- [ ] Todos os integrantes rodaram `git checkout main && git pull` **antes de sair**
