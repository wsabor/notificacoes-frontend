# Gabarito — Encontro 8 (uso do docente)

---

## Critério de aceitação

| Item | Mínimo aceitável | Sinal de que está raso demais |
|---|---|---|
| CORS | `cors()` instalado e aplicado, API reiniciada | Erro de CORS "resolvido" só porque testaram em aba anônima (cache mascarando o problema) |
| `useEffect` | Array de dependências `[]` presente | `useEffect` sem array — roda em loop infinito se atualizar estado dentro dele |
| Tratamento de erro | `resposta.ok` checado manualmente | Só `.catch()`, sem checar `.ok` — deixa passar erros 404/500 como sucesso |
| Estados de carregamento | Os três estados (carregando/erro/dados) refletidos na tela | Só a lista, sem feedback de carregamento nem erro |

---

## Erros recorrentes esperados

| Erro | Causa provável | Como apontar |
|---|---|---|
| CORS "não resolve" mesmo após `app.use(cors())` | Esqueceram de reiniciar o processo da API | "Vocês salvaram o arquivo, mas o servidor rodando ainda é o antigo. Reiniciem." |
| `useEffect` disparando em loop infinito | Array de dependências ausente, ou dependência que muda a cada render | Mostrar o console enchendo de requisições — efeito visualmente óbvio |
| Erro 404 tratado como sucesso | Não checaram `resposta.ok` | Mostrar no Network tab do DevTools que a resposta chegou com status 404, mas o código seguiu como se desse certo |
| Tela fica "Carregando..." para sempre | Esqueceram do `finally`, ou erro não tratado interrompeu antes de chegar lá | Adicionar `finally { setCarregando(false) }` |
| Mistura de URL: `localhost` num computador, IP do Proxmox em outro | Cada grupo tem endereço diferente para a própria API | Confirmar que cada grupo usa a URL do próprio `CONTRATO-API.md`, não copiou de outro grupo |

---

## Sobre o teste de erro proposital (Parte 4, Passo 3)

Vale insistir nesse passo mesmo com pressa de tempo — é onde normalmente se descobre que o tratamento de erro não existe de verdade (só existe no código, nunca foi executado). Um grupo que "termina rápido" sem nunca ter visto a própria mensagem de erro aparecer não terminou, só não testou.

---

## Sobre o ritmo

Este encontro tem menos risco de atraso que os anteriores — CORS costuma ser resolvido rápido uma vez entendido, e o padrão de `useEffect` + três estados é mecânico depois da primeira vez. Se sobrar tempo, desafio extra: adicionar um botão "tentar novamente" que reexecuta a busca em caso de erro (repetir a função `buscar()` a partir de um clique, não só do `useEffect`).
