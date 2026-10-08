# IA no projeto

**Usar IA é opcional.** Nada neste site exige uma ferramenta de IA, e um PR escrito à
mão segue exatamente as mesmas regras de um escrito com ajuda de modelo.

## As regras que valem para quem usa ✅

1. **Você responde pelo diff.** Quem não sabe explicar uma linha não abre o PR. "Foi a
   IA que escreveu" não é resposta na review.
2. **Nada sensível vai para o modelo**: credencial, `.env` real, token, connection
   string, dado pessoal de respondente, material confidencial da cliente. Precisa colar
   um erro que contém segredo? Mascare antes.
3. **A IA não versiona por você.** `git add`, `commit`, `push` e abrir PR são ações
   suas, depois de ler o diff inteiro e rodar a suíte.
4. **Sem comentário `# gerado por IA`** no código. Ele envelhece no primeiro edit humano;
   o lugar dessa informação é o PR.
5. **O padrão é o deste site.** Se a ferramenta sugere uma forma diferente da do molde,
   vale o molde.

🟡 **Rastreabilidade no PR** — uma linha no fim da descrição, como
`IA: Claude Code — implementação e testes` ou `IA: nenhuma`. Ajuda a entender, quando
aparecer um bug, se ele nasceu de um padrão mal interpretado. Proposta, ainda não
adotada pelo time inteiro.

## O `creed-ai-context`

É o repositório de contexto que as ferramentas de IA do time leem: o mesmo conteúdo
para Claude Code, Codex, Copilot e Cursor, com comandos prontos para especificar,
implementar e revisar. Quem usa instala só o adaptador da própria ferramenta — veja o
[README](https://github.com/creed-educa-ai/creed-ai-context#readme).

Ele também guarda o que é material de trabalho, não regra: as specs e tasks de cada
tarefa, o ledger de premissas, a pauta da próxima reunião e os ADRs completos.

!!! info "Este site × o `creed-ai-context`"
    Os padrões de código, git e processo **moram aqui**, e é aqui que valem para todo
    mundo. Quando o texto do `creed-ai-context` divergir deste site, vale este site — e
    a divergência é um bug a corrigir lá.

## Por que o processo pede para "parar e explicar"

Quem usa os comandos do `creed-ai-context` vai notar que a IA, ali, **para** nas
bifurcações de design para você decidir, e termina explicando a abordagem e o que foi
descartado. É de propósito: código que o time não entende é dívida, não entrega. O
objetivo é que você saia da tarefa sabendo mais do que entrou — e conseguindo defender
o PR sozinho.
