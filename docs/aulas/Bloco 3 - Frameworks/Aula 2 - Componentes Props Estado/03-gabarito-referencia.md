# Gabarito — Encontro 7 (uso do docente)

Código varia por grupo (estilos, nomes). Este documento é referência de correção, não resposta única.

---

## Critério de aceitação, por parte

| Parte | Mínimo aceitável | Sinal de que está raso demais |
|---|---|---|
| `NotificationList` | Recebe array via prop, trata lista vazia | `.map` ainda solto em `App.jsx`, sem componente próprio |
| `FilterBar` | Estado do filtro **não** existe dentro do `FilterBar` | Filtro funciona, mas tem `useState` duplicado dentro do `FilterBar` |
| Formulário | `value` + `onChange` em todo input; `e.preventDefault()` presente | Input sem `value` (não controlado) ou página recarregando ao enviar |
| Atualização de estado | Sempre via novo array/objeto (`[...atual, novo]`) | Qualquer `.push()`, `.splice()` ou mutação direta de objeto |

O teste rápido para "elevação de estado" está funcionando: **peça para o aluno apontar em qual arquivo o estado realmente mora.** Se a resposta for "no componente que estou olhando agora" quando deveria estar no pai, o conceito não pegou ainda.

---

## Erros recorrentes esperados

| Erro | Causa provável | Como apontar |
|---|---|---|
| Notificação nova não aparece na lista | Mutação direta do array (`.push`) em vez de spread | Mostrar no React DevTools que o estado "mudou" mas a referência é a mesma |
| Formulário recarrega a página ao enviar | Faltou `e.preventDefault()` | Sintoma clássico — perguntar "o que muda na URL quando isso acontece?" |
| Filtro para de funcionar depois da extração do `FilterBar` | Estado duplicado (um no pai, outro no filho) | "Tem dois lugares decidindo a mesma coisa. Qual deveria mandar?" |
| `key` ausente ou usando índice do array (`key={i}`) | Hábito ainda não formado | Funciona por ora, mas explicar por que quebra ao reordenar/filtrar a lista |
| Canal do formulário sempre "PUSH", nunca muda | Esqueceram de conectar um seletor ao estado `canal` | Perguntar se o formulário realmente permite escolher, ou só parece que permite |

---

## Sobre o ritmo

Este encontro já é a fusão de dois dias originais — não há mais folga para compensar atraso adicional sem impacto real no encontro 8. Se ao final da Parte C a maioria dos grupos não tiver o formulário funcionando:

- Aceite entregar sem o Passo 3 completo, com plano de terminá-lo nos primeiros 20 minutos do encontro 8, **descontados** do tempo de `fetch`/`useEffect` daquele dia.
- Registre isso como segundo sinal de que a compressão do bloco React está apertada — combinado com uma eventual folga não confirmada no encontro 6, pode ser hora de reavaliar se o encontro 9 (Rotas + Auth + CRUD) precisa de ajuste de escopo.
