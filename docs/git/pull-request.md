# Pull request

## Antes de abrir

- [ ] Branch no padrão 🔒 ([Branches](index.md#nome-da-branch)) e saída da `dev` atualizada
- [ ] Você leu o **diff inteiro**, arquivo por arquivo: `git diff dev...HEAD`
- [ ] Suíte local verde ([Testes e qualidade local](../codigo/testes.md))
- [ ] Nada fora do escopo da subtarefa no diff
- [ ] Mudou `models.py`? **Nenhum arquivo em `alembic/versions/`** 🔒 e a seção "Banco" preenchida ([Mudar o banco](../tutoriais/mudar-o-banco.md))
- [ ] Você sabe explicar cada linha do diff sem ajuda ([Definition of Done](../processo/definition-of-done.md))

## Alvo e título

- **Alvo**: `dev` (já vem selecionado). Só PR de release ou `hotfix` aponta para `main`.
  Nos repos de documentação, o alvo é `main`.
- ✅ **Título**: o mesmo padrão do [commit](commits.md) — `feat: permitir filtrar
  respondentes por país`. O GitHub sugere o nome da branch como título
  (`Feat/48 integracao com backend`): troque antes de criar.

## Descrição

Copie e preencha. Seção que não se aplica fica com a frase indicada, não em branco —
seção vazia faz o revisor perguntar se você esqueceu.

```markdown
## O que muda

<uma frase: o que o sistema passa a fazer>

## Por quê

<o motivo. O link do ClickUp não substitui isto>

## Como verificar

1. <passo copiável: comando, rota, tela>
2. <o que deve aparecer>

## Premissas

- 🟡 P-NNN — <interpretação adotada>

<ou "nenhuma">

## Banco

- O que mudou: <tabela/coluna/tipo/índice/constraint>
- Ajustes manuais na migration temporária: <rename `a` → `b` · backfill de … ·
  `server_default` em … > ou "nenhum ajuste manual"

<ou "models.py não mudou">

---

IA: <ferramenta — o que fez> ou "nenhuma"
Tarefa: <link do ClickUp>
```

| Seção | Marca | Por quê |
|---|---|---|
| O que muda · Por quê · Como verificar | ✅ | está na cartilha dos repos. "Como verificar" é o roteiro do revisor: sem ele, a review vira adivinhação |
| Tarefa (link do ClickUp) | ✅ | cartilha dos repos |
| Premissas | ✅ | decisão de produto tomada sem a cliente precisa estar à vista de quem revisa ([Premissas](../processo/premissas.md)) |
| Banco | ✅ | [ADR-0009](../referencia/decisoes.md): o PR de tarefa sobe sem migration, e esta seção é o único jeito de quem consolida saber o que o autogenerate não sabe |
| Linha `IA:` | 🟡 | proposta, ainda não adotada por todo o time ([Em consolidação](../referencia/em-consolidacao.md)) |

!!! warning "A seção Banco não é opcional quando `models.py` muda"
    Um rename de coluna, feito pelo autogenerate, vira `drop` + `add` e **perde o dado
    da coluna**. Quem escreve a migration consolidada só sabe que era um rename se você
    disser aqui.

## Peça a review

A um colega que conheça a parte do código que você mexeu — qualquer pessoa do time pode
aprovar. Sem saber a quem pedir, peça ao time `creed-educa-ai/maintainers`
([Quem é quem](../comecando/equipe.md)):

```bash
gh pr create --base dev --reviewer creed-educa-ai/maintainers
```

## Para mergear 🔒

| Exigência | Onde é aplicada |
|---|---|
| 1 aprovação de outra pessoa (não dá para aprovar o próprio PR) | ruleset |
| CI `qualidade` verde | check obrigatório |
| Check `nome-da-branch` verde | check obrigatório |
| Todas as conversas resolvidas | ruleset |
| Aprovação descartada se chegar commit novo depois dela | ruleset |

Existe uma saída de emergência: o papel **admin** está na lista de bypass do ruleset e
mergeia sem aprovação. Ela anula a única revisão que o projeto tem, então serve para
destravar setup ou incidente — não para pular a fila.

## Tamanho

PR pequeno é revisado no mesmo dia; PR enorme espera e volta com comentário raso. Uma
subtarefa bem cortada cabe num PR que se lê de uma vez. Se o seu não cabe,
provavelmente são duas tarefas — converse com quem especificou antes de abrir.
