# Como uma tarefa chega até você

```
card no ClickUp ──▶ discovery (AGES III/IV) ──▶ subtarefas publicadas no ClickUp
                                                        │
                    você pega uma ◀─────────────────────┘
                         │
                         ▼
     branch a partir da dev ──▶ código no molde ──▶ suíte local verde
                                                        │
                         merge ◀── review ◀── PR ◀──────┘
```

## Quem especifica e quem implementa

**AGES III e IV fazem a discovery**: leem o card cru, decidem o recorte, escrevem o
contrato de API quando há um, e publicam no ClickUp um épico com **subtarefas** prontas
para alguém pegar. Esse trabalho de especificação mora no
[`creed-ai-context`](https://github.com/creed-educa-ai/creed-ai-context), mas o
resultado dele é a subtarefa — e **a subtarefa tem que se bastar**.

Você, ao pegar uma subtarefa, não precisa abrir o `creed-ai-context` para começar.

## Antes de começar: a subtarefa se basta?

Leia só a subtarefa e os anexos dela, e responda:

1. **O que muda para quem usa o sistema?**
2. **Por onde eu começo — que arquivo eu abro primeiro?**
3. **Como eu sei que terminei?**

Precisou perguntar a alguém para responder qualquer uma das três? **A subtarefa não
está pronta**, e isso é defeito da publicação, não seu. Comente na subtarefa e marque
quem a publicou. Não preencha a lacuna no palpite. O checklist completo está em
[Definition of Ready](../processo/definition-of-ready.md).

## Durante a tarefa

| Situação | O que fazer |
|---|---|
| Dúvida de **produto** ("esse campo é obrigatório?", "inativo conta?") | não pare: escolha a interpretação mais barata de reverter e registre como **premissa** — ver [Premissas](../processo/premissas.md) |
| Dúvida **técnica** ("isso é service ou repository?", "que nome dou à branch?") | está neste site ou no molde. Não está? Pergunte a um AGES III/IV — e a resposta vira página aqui |
| A tarefa precisa de back **e** front | são duas subtarefas e **dois PRs**, um por repo. O contrato de API vem escrito na subtarefa |
| Achou um problema fora do escopo da tarefa | anote na descrição do PR e avise quem especificou. Não conserte de passagem |

## Uma subtarefa, um repo, um PR

Subtarefa não cruza repositório. Um PR do back e um do front que dependem um do outro
são revisados em separado, e o contrato escrito na subtarefa é o que liga os dois. Isso
é de propósito: um PR que mexe em dois lugares é irrevisável por uma pessoa só, e o time
tem **uma** aprovação obrigatória por PR.

## Do código ao merge

1. Branch a partir da `dev` atualizada, com o nome no padrão 🔒 — [Branches](../git/index.md).
2. Código no molde — [Arquitetura](../arquitetura/index.md).
3. Mudou `models.py`? O PR **não** leva migration 🔒 — [Mudar o banco](../tutoriais/mudar-o-banco.md).
4. Suíte local verde — [Testes e qualidade local](../codigo/testes.md).
5. Todos os itens da [Definition of Done](../processo/definition-of-done.md).
6. PR com a descrição completa — [Pull request](../git/pull-request.md).
7. Review — [O que a review cobra](../git/revisao.md).
8. Merge e apague a branch.
