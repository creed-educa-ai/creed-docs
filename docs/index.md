# Padrões do time CREED

Este site reúne **como o time do CREED.ai Educa escreve, organiza, versiona e entrega
código**: as regras de git e de PR, a arquitetura, as convenções de código, o processo
de uma tarefa e os tutoriais do dia a dia.

Ele existe por um motivo prático: **nada que a review cobra deveria ser novidade na hora
do PR.** Se uma regra vale para o seu código, ela está aqui antes, com o motivo dela.

!!! tip "Primeira vez aqui?"
    Leia, nesta ordem: [Primeiro dia](comecando/index.md) →
    [Como uma tarefa chega até você](comecando/fluxo-da-tarefa.md) →
    [Branches](git/index.md) → [O que a review cobra](git/revisao.md).
    Leva uns 30 minutos e cobre o que mais volta em PR.

## Como ler cada regra

Toda regra deste site carrega uma de três marcas. Elas dizem **quem cobra** e **o que
acontece se você não seguir**:

| Marca | Significa | Se você não seguir |
|---|---|---|
| 🔒 **Cobrado pelo GitHub** | um hook, o CI ou o ruleset verifica sozinho | o push é recusado ou o PR não mergeia — nem chega na review |
| ✅ **Padrão do time** | decidido (ADR aceito, cartilha de contribuição, decisão registrada) ou seguido de forma consistente no código | a review pede a mudança |
| 🟡 **Em consolidação** | o código ainda diverge, ou a decisão está aberta | a review **não** bloqueia por isso. Siga o vizinho mais próximo e o molde — ver [Em consolidação](referencia/em-consolidacao.md) |

Uma regra 🟡 vira ✅ quando o time decide. Até lá, ela está aqui para você saber que o
ponto existe, e não para virar cobrança de surpresa.

## O que mora onde

| Você procura | Onde está |
|---|---|
| Padrão de código, arquitetura, git, PR, processo, tutorial | **aqui** |
| Visão geral do projeto, cronograma, atas, materiais da AGES | wiki do projeto, no GitLab da AGES |
| Specs e tasks de uma tarefa, ledger de premissas, ADRs completos | [`creed-ai-context`](https://github.com/creed-educa-ai/creed-ai-context) |
| A tarefa em si | ClickUp |
| O código | [`creed-backend`](https://github.com/creed-educa-ai/creed-backend) · [`creed-frontend`](https://github.com/creed-educa-ai/creed-frontend) · [`creed-infrastructure`](https://github.com/creed-educa-ai/creed-infrastructure) |

!!! info "E o `creed-ai-context`?"
    É o repositório que as ferramentas de IA do time leem. Usar IA é **opcional**, e
    nada neste site exige o `creed-ai-context` aberto. Quando uma regra daqui e uma de
    lá divergem, **vale a daqui**. Ver [IA no projeto](processo/ia.md).

## Quando duas fontes discordam

1. **O que o GitHub cobra (🔒).** A máquina não lê documentação: se o texto diz uma
   coisa e o CI faz outra, o PR segue o CI, e a doc precisa de correção.
2. **ADR com status Aceito** ([Decisões](referencia/decisoes.md)).
3. **Este site.**
4. **O molde**: [`app/domains/users/`](https://github.com/creed-educa-ai/creed-backend/tree/dev/app/domains/users)
   no back e [`src/features/authentication/`](https://github.com/creed-educa-ai/creed-frontend/tree/dev/src/features/authentication)
   no front.
5. **O domínio ou a feature vizinha** mais parecida com o que você está fazendo.

Achou uma divergência entre este site e o código numa regra ✅? Um dos dois está
errado. Avise no PR ou abra um PR aqui ([Contribuir](referencia/contribuir.md)).
