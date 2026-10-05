# Gabarito — Encontro 9 (uso do docente)

---

## Critério de aceitação

| Item | Mínimo aceitável | Sinal de que está raso demais |
|---|---|---|
| Rotas | `/login` e `/` navegáveis sem recarregar a página | `<a href="...">` comum em vez de `<Link>`/`useNavigate` — recarrega a página, perde o estado do Context |
| Context | Token acessível via `useAuth()` em qualquer componente | Token ainda sendo passado por prop, Context criado mas não usado de verdade |
| Rota protegida | Redireciona de fato para `/login` sem token | Rota "protegida" só esconde visualmente, mas ainda permite acessar a URL direto |
| CRUD | As 3 operações de escrita mandam o header `Authorization` | Só a criação tem token; atualizar/apagar esquecidos ou sem header |

O teste mais rápido de tudo: **abrir uma aba anônima e tentar acessar a rota principal sem logar.** Se conseguir ver alguma coisa, a proteção não está completa.

---

## Erros recorrentes esperados

| Erro | Causa provável | Como apontar |
|---|---|---|
| Login "funciona" mas a próxima tela não reconhece o usuário | `AuthProvider` não envolve o componente que usa `useAuth()` | Verificar a árvore de componentes em `main.jsx` — o Provider precisa estar **acima** de tudo que precisa do contexto |
| Token some ao recarregar a página | Esqueceram `useState(() => localStorage.getItem('token'))` na inicialização — começou com `useState(null)` puro | Mostrar que `localStorage` tem o token, mas o estado do React não foi lido dele |
| `401` mesmo enviando o token | Header escrito errado, faltou `Bearer ` com espaço, ou token com aspas extras | Inspecionar o Network tab, comparar com o middleware do encontro 5 |
| Apagar/atualizar funciona mas não reflete na tela | Estado local (`notificacoes`) não foi atualizado depois da resposta da API | Perguntar: "a API apagou no banco, mas alguém disse pro React esquecer esse item também?" |
| Logout não redireciona | Função `logout()` limpa o token mas ninguém chama `navigate('/login')` depois | Adicionar a navegação explícita após o logout |

---

## Sobre o ritmo

Este é o encontro mais arriscado de atraso do bloco inteiro — muita coisa nova em pouco tempo (rotas + Context + autenticação + 3 operações CRUD). Se ao final da Parte C a maioria dos grupos não tiver as três operações completas:

- **Priorizar completar criar e atualizar** (marcar como lida). Apagar pode ficar pendente para o início do encontro 10, antes da revisão da somativa.
- Isto seria o **terceiro sinal consecutivo** (depois dos encontros 6 e 7) de que a compressão do bloco React ficou otimista demais. Se acontecer, vale conversar sobre ajustar o escopo mínimo exigido na somativa parcial de 29/09 — cobrar CRUD completo pode não ser realista se a turma chegou até aqui apertada.
- Se, ao contrário, a maioria terminar com folga, é um bom sinal de que a aposta da compressão (React Native acelerando o ritmo) se confirmou — vale registrar isso também, para calibrar semestres futuros.
