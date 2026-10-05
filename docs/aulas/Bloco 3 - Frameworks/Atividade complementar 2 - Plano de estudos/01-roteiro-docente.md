# Roteiro docente — Atividade complementar 2, Plano de estudos

**PEND — Programação Front-End** · aplicar depois da Atividade complementar 1 (Lista de compras)

> Material do professor. Não distribuir. Os alunos recebem `02-atividade-alunos.md`. O gabarito está em `03-gabarito-referencia.md`.

---

## A diferença para a atividade 1

A lista de compras entregava o código pronto em cada passo. Aqui os alunos recebem só requisitos, e escrevem o código sozinhos. A estrutura é quase a mesma (componente, estado, `.map`, adicionar), então quem fez a primeira atividade tem o caminho na cabeça. O que é novo:

- **Remover saiu, alternar entrou.** Alternar exige `.map` devolvendo um objeto novo, um passo acima do `filter`.
- **Renderização condicional** (ternário na classe e `&&`). É o único conceito novo, e é a única parte com código fornecido.
- **Valor calculado** (contador a partir da lista, sem estado próprio). Retoma o `notificacoesVisiveis` do encontro 7.

## Tempo

Entre 60 e 90 minutos. Vai bem mais devagar que a atividade 1, porque os alunos precisam pensar. Se a aula acabar antes, a nota parcial pelo checklist resolve: quem chegou ao requisito 4 tem 4 de 6.

## Como acompanhar sem entregar a resposta

Os alunos vão pedir "me mostra o código". A atividade só funciona se você **devolver uma pergunta**. A tabela no fim do gabarito tem uma pergunta para cada travamento comum.

Pontos de atenção:

- **Requisito 2:** ver quem faz sozinho é o termômetro da turma. Quem trava aqui não fixou componente com props. Indique a atividade 1 como consulta.
- **Requisito 5, Parte A:** é o ponto mais difícil. Se metade da turma travar nele, vale fazer uma explicação rápida no quadro do `.map` que troca um único item, sem mostrar o código completo.
- **Cópia da lista de compras:** é esperada em parte. O critério é: copiar a estrutura e reescrever é aceitável. Colar o arquivo e só trocar o texto não é.

## Correção

Seis requisitos, um ponto cada. Nota = requisitos concluídos ÷ 6. Corrija pelo comportamento da página (rodar o projeto e testar), não pelo código. O gabarito lista as variações que valem e os casos que devem ser descontados.

Os commits por requisito mostram até onde cada aluno chegou sozinho. Se o histórico tiver um único commit com tudo pronto, converse com o aluno antes de fechar a nota.
