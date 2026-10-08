# Quem é quem

## Quem revisa e mergeia

✅ **Qualquer pessoa do time pode revisar e aprovar** — o merge exige **uma** aprovação
de alguém que não seja o autor 🔒. Peça a um colega que conheça a parte do código que
você mexeu. Aprovado e com os checks verdes, o merge pode ser feito por você ou por quem
aprovou.

Não sabe a quem pedir? Peça ao time do GitHub **`creed-educa-ai/maintainers`**, os
responsáveis técnicos:

| Papel | GitHub |
|---|---|
| AGES IV | [@gabriellrmartins](https://github.com/gabriellrmartins) |
| AGES III | [@Kv1ecz](https://github.com/Kv1ecz) |

```bash
gh pr create --reviewer creed-educa-ai/maintainers
```

Pela interface do GitHub, é o mesmo: em *Reviewers*, escolha `creed-educa-ai/maintainers`.

## A quem perguntar

| Dúvida | Pergunte a |
|---|---|
| A subtarefa não diz por onde começar | quem publicou a subtarefa |
| Padrão de código, arquitetura, review | AGES III |
| Prioridade, prazo, escopo da sprint | AGES IV |
| Produto ("a cliente quer X ou Y?") | ninguém do time sabe — vira [premissa](../processo/premissas.md) |

## Fora do time

| Quem | Como funciona |
|---|---|
| **A cliente** | só em reunião marcada. Dúvida de produto no meio da sprint vira premissa registrada, e as premissas abertas entram na pauta da reunião seguinte |
| **O professor** | acompanha os repositórios com acesso de leitura; não aprova PR |

## Conferir a conta do `gh` antes do primeiro PR

O `gh` abre o PR com a conta que estiver ativa, e uma variável `GH_TOKEN` no ambiente
**tem prioridade sobre a conta logada**.

```bash
gh auth status
```

A conta mostrada precisa ser a sua conta pessoal, a que está no time. Se aparecer outra
conta com `Token: ghp_...`, é `GH_TOKEN` no ambiente: remova a variável da sessão.

A lista completa do time, com nomes, está em
[`equipe.md`](https://github.com/creed-educa-ai/creed-ai-context/blob/main/equipe.md).
