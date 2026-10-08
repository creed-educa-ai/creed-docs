# Premissas

A cliente é professora e fala com o time **em reunião marcada**. Uma dúvida de produto
no meio da sprint não tem resposta em minutos, como teria num time de empresa. Parar a
tarefa até a próxima reunião custa dias; decidir em silêncio custa retrabalho.

A saída é a mesma dos dois lados: ✅ **decidir, registrar a decisão como premissa e
seguir.**

## A regra

Apareceu uma dúvida de **produto** no meio da tarefa:

1. **Não pare.** Escolha a interpretação mais provável e mais barata de reverter.
2. **Registre a premissa** — como afirmação, não como pergunta.
3. **Marque no código** onde ela foi usada.
4. **Siga a tarefa.**
5. Na próxima reunião, a premissa entra na pauta, e a cliente confirma ou refuta.

## É premissa ou não é?

| Dúvida | Premissa? |
|---|---|
| "O prognóstico considera respondentes inativos?" | ✅ produto — a cliente decide |
| "O relatório exporta PDF ou CSV?" | ✅ produto |
| "Esse campo é obrigatório na primeira versão?" | ✅ produto |
| "Isso vai no service ou no repository?" | ❌ técnica — está em [Backend](../arquitetura/backend.md) |
| "Que nome dou para a branch?" | ❌ técnica — está em [Branches](../git/index.md) |

**Dúvida técnica não vira premissa.** Ela está neste site, no molde, ou é pergunta para
um AGES III — e, se não estava escrita, a resposta vira página aqui.

## Como escolher a interpretação

Nesta ordem:

1. **O que o produto já faz** em situação parecida.
2. **O mais barato de reverter.** Entre "guardar o campo e não mostrar" e "não guardar",
   guarde: coluna nova depois é migration simples; dado que nunca foi gravado não volta.
3. **O menor escopo.** Na dúvida entre fazer uma coisa ou três, faça uma.
4. **O que não trava outra pessoa.** Prefira a interpretação que mantém o contrato de
   API.

Nunca escolha "o mais completo, porque dá mais trabalho". Premissa errada com escopo
grande é a mais cara de desfazer.

## Onde registrar

O registro de todas as premissas — o **ledger** — fica no `creed-ai-context`, em
[`decisoes/premissas.md`](https://github.com/creed-educa-ai/creed-ai-context/blob/main/decisoes/premissas.md).
Cada uma tem um ID `P-NNN` que nunca se reaproveita, a data, a interpretação adotada,
o motivo, o custo de reverter e o status.

- **Você trabalha com o `creed-ai-context`?** Acrescente a linha no ledger, com o
  próximo número livre, e abra o PR de documentação logo — premissa sem commit é
  invisível para o time, e duas pessoas acabam pegando o mesmo número.
- ✅ **Não trabalha?** Escreva a premissa na seção **Premissas** do seu PR, como
  "premissa nova, sem número", e marque um AGES III no PR. Ele a leva ao ledger e
  devolve o número — você não espera por isso para seguir a tarefa.

## Como marcar no código

Um comentário no ponto em que a premissa decide o comportamento — o mesmo formato que já
está no código:

```ts
// > 🟡 Premissa P-004 — mínimo de 8 caracteres, sem regra extra de
// complexidade. Confirmar com o time.
```

Quando a premissa é confirmada, o marcador sai. Quando é refutada, vira tarefa de
correção — e o comentário diz exatamente onde mexer.

## O ciclo de vida

```
🟡 aberta ──reunião──▶ ✅ confirmada   (vira regra; o marcador sai do código)
                  └──▶ ❌ refutada     (vira tarefa de correção no ClickUp)
                  └──▶ 🟡 segue aberta (entra na pauta seguinte)
```

Premissa refutada **não é erro do time** — é o mecanismo funcionando. Erro é a premissa
que nunca foi registrada e virou comportamento silencioso do produto.
