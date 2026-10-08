# Decisões (ADRs)

Um ADR registra uma decisão de arquitetura: o contexto, o que foi decidido, as
alternativas descartadas e o custo de voltar atrás. Os textos completos ficam no
`creed-ai-context`, em [`decisoes/adrs/`](https://github.com/creed-educa-ai/creed-ai-context/tree/main/decisoes/adrs);
aqui está o resumo e o **status**, que é o que decide se a regra já vale.

| Status | Significa |
|---|---|
| **Aceito** | decidido pelo time. As regras que saem dele são ✅ ou 🔒 |
| **Proposto** | escrito e em uso, mas o time ainda não fechou. As regras que saem dele são 🟡, a menos que o CI já as cobre |

## As decisões

| ADR | Decisão | Status | O que muda para você |
|---|---|---|---|
| [0009](https://github.com/creed-educa-ai/creed-ai-context/blob/main/decisoes/adrs/0009-migration-consolidada-por-sprint.md) | PR de tarefa sobe **sem migration**; um AGES III consolida as mudanças de banco da sprint | **Aceito** (2026-10-06) | 🔒 o CI recusa migration no PR de tarefa — [Mudar o banco](../tutoriais/mudar-o-banco.md) |
| [0008](https://github.com/creed-educa-ai/creed-ai-context/blob/main/decisoes/adrs/0008-publicacao-de-tasks-no-clickup.md) | Épico e subtarefas são publicados no ClickUp a partir da spec, com rascunho e aprovação | Proposto | é de onde vêm as subtarefas que você pega |
| [0007](https://github.com/creed-educa-ai/creed-ai-context/blob/main/decisoes/adrs/0007-amplify-e-ec2-no-lugar-do-eks.md) | Front no **Amplify**, back + N8N + Keycloak numa **EC2** com Docker Compose; sai o EKS | Proposto | o ambiente local tem a mesma topologia da produção — [Arquitetura](../arquitetura/index.md) |
| [0006](https://github.com/creed-educa-ai/creed-ai-context/blob/main/decisoes/adrs/0006-e-mail-transacional.md) | E-mail transacional pelo Keycloak; Mailpit no local, provedor real condicionado à infraestrutura | Proposto | ainda não implementado |
| [0005](https://github.com/creed-educa-ai/creed-ai-context/blob/main/decisoes/adrs/0005-idioma-do-codigo.md) | **Inglês** em todo identificador novo; português só no termo que a cliente usa | Proposto | 🟡 siga o vizinho — [Idioma e nomes](../codigo/idioma-e-nomes.md) |
| [0004](https://github.com/creed-educa-ai/creed-ai-context/blob/main/decisoes/adrs/0004-camadas-do-backend.md) | As camadas do back: o que veio do Netflix Dispatch e o que é nosso | Proposto | 🔒 na prática: o teste de arquitetura cobra as regras — [Backend](../arquitetura/backend.md) |
| [0002](https://github.com/creed-educa-ai/creed-ai-context/blob/main/decisoes/adrs/0002-mcp-do-clickup-no-setup.md) | O setup registra o acesso do Claude Code ao ClickUp; autenticar é opcional | Proposto | só para quem usa IA |
| [0001](https://github.com/creed-educa-ai/creed-ai-context/blob/main/decisoes/adrs/0001-harness-multi-llm.md) | Um contexto de IA único, independente de ferramenta | Proposto | só para quem usa IA |

A numeração pula a 0003. O código e os READMEs também citam ADR-001, ADR-002 e
ADR-003, com número de seção — esses documentos não estão em `decisoes/adrs/`.

## Uma decisão nova

Decisão que muda a forma de todo mundo trabalhar — uma camada nova, uma biblioteca
estrutural, um jeito novo de versionar — vira ADR antes de virar código. O modelo está
em [`templates/adr-template.md`](https://github.com/creed-educa-ai/creed-ai-context/blob/main/templates/adr-template.md).
Converse com um AGES III antes de escrever.

Quando um ADR muda de status, esta página e as páginas afetadas mudam junto — e as
marcas 🟡 das regras dele viram ✅.
