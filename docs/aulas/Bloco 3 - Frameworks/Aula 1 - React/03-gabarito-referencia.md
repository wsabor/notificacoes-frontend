# Gabarito — Encontro 6 (uso do docente)

O código deste encontro varia por grupo (cores, nomes de campos, decisões de estilo do próprio design system). Este documento não é "a resposta certa" — é uma referência de correção e um repertório de erros esperados.

---

## Critério de aceitação, por componente

| Componente | Mínimo aceitável | Sinal de que está raso demais |
|---|---|---|
| `Button` | Recebe `children` + `variant`; duas aparências distintas | Texto do botão fixo dentro do componente, sem `children` |
| `NotificationCard` | Todos os campos vêm de props; nenhum texto de notificação fixo | `titulo`/`texto` escritos direto no JSX do componente |
| `FilterChip` | Aparência muda visivelmente conforme prop `ativo` | Chip sempre com a mesma cor, ativo ou não |
| `App.jsx` | Usa os três componentes com dados de uma lista (`.map`) | Cada notificação copiada e colada manualmente, uma `<NotificationCard>` por vez |

O teste rápido para qualquer componente: **"se eu mudar só os dados na lista de exemplo, o componente reflete a mudança sem eu tocar no arquivo do componente?"** Se sim, prop está funcionando. Se não, há dado fixo escondido.

---

## Erros recorrentes esperados

| Erro | Causa provável | Como apontar |
|---|---|---|
| `<View>` ou `onPress` aparecendo no código React web | Reflexo do React Native | Apontar a tabela de comparação da Parte 3 — não é erro grave, é vocabulário migrando |
| Esquecer `key` no `.map()` | Hábito ainda não formado (React Native também exige, mas é comum passar despercebido) | Mostrar o aviso no console do navegador — é autoexplicativo |
| Cor do Tailwind padrão em vez da cor customizada | Não configurou o bloco `@theme` no CSS ou esqueceu de trocar a classe | Perguntar: "essa cor está em algum lugar do Figma de vocês, ou é a cor de qualquer projeto Tailwind?" |
| Aluno seguindo tutorial da internet com `tailwind.config.js` e `theme.extend.colors` | Muito material online ainda ensina a sintaxe da v3 | Explicar rapidamente: mesmo conceito, mas na v4 a configuração de cor mora no CSS (`@theme`), não em JS |
| Projeto Vite criado numa subpasta nova, não na pasta clonada | Esqueceram o `.` no comando `npm create vite@latest .` | Resolver na hora: mover os arquivos ou recriar o projeto corretamente |
| Commit direto na `main` (fora do scaffold inicial) | Primeiro dia de verdade usando o fluxo de branch — esquecimento esperado | Reforçar o guia; não é motivo de punição hoje, é o primeiro dia valendo |
| `className` escrito como `class` (reflexo de HTML puro do encontro 1/2) | Confusão com HTML estático anterior | Lembrar: JSX exige `className`, igual `style` já era objeto em React Native |

---

## Sobre o ritmo comprimido

Se, ao circular, você perceber que a maioria dos grupos está **claramente atrasada** em relação ao roteiro (por exemplo, ainda configurando o Tailwind quando deveria estar na Aula 3), é sinal de que a compressão foi otimista demais para esta turma específica. Nesse caso:

- Não sacrifique a Parte 5 (componentização) para "fechar o conteúdo teórico". É a parte com maior valor de retenção.
- Prefira deixar `Button` e `FilterChip` completos e `NotificationCard` como tarefa do encontro 7, a fazer os três pela metade.
- Registre o atraso — ele é o primeiro sinal real de que a folga prevista pelo React Native não está se confirmando na prática, o que pode exigir revisar a compressão dos encontros 7–9.
