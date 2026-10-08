# Processo

O time trabalha com **especificação antes do código**: entre o card do ClickUp e a
primeira linha escrita existe uma etapa em que a ambiguidade é resolvida no papel, mais
cedo e mais barato. Quem faz essa etapa são os AGES III e IV, e o que chega a você é o
resultado dela — a subtarefa.

```
card cru ──▶ spec ──▶ decomposição ──▶ subtarefas no ClickUp ──▶ código ──▶ PR
            (AGES III/IV)                  │                      (você)
                                           └─ Definition of Ready: a subtarefa se basta
```

| Página | Para quê |
|---|---|
| [Definition of Ready](definition-of-ready.md) | o que uma subtarefa precisa ter para você começar hoje |
| [Definition of Done](definition-of-done.md) | o que precisa estar verdade para você abrir o PR |
| [Premissas](premissas.md) | o que fazer com uma dúvida de produto, já que a cliente só fala em reunião |
| [IA no projeto](ia.md) | as regras para quem usa IA — que é opcional |

## Nem toda tarefa passa pela especificação inteira

A profundidade é proporcional à incerteza. Uma tarefa que mexe num repo só, fica dentro
do molde, não cria domínio, não muda contrato de API, não mexe no banco e já tem
critério de aceite verificável vai direto para o código. Domínio novo, mudança que back
e front precisam combinar, regra de quem-pode-o-quê: essas passam pela spec.

Quem especifica tem um manual próprio, no `creed-ai-context`:
[jornada de discovery](https://github.com/creed-educa-ai/creed-ai-context/blob/main/playbooks/jornada-de-discovery.md).
