# Banco e migrations

PostgreSQL, mapeado com SQLAlchemy e versionado com Alembic: cada mudança de schema é
uma *migration* (migração), um arquivo em `alembic/versions/`. É o assunto com **maior
chance de perda de dado** do projeto, por isso tem as regras mais duras.

## PR de tarefa não traz migration 🔒

Decidido na retrospectiva da sprint 2 ([ADR-0009](../referencia/decisoes.md), Aceito) e
cobrado pelo CI desde 2026-10-06.

| | Migration **temporária** | Migration **consolidada** |
|---|---|---|
| Quem gera | quem desenvolve a tarefa | um AGES III, uma ou mais vezes por sprint |
| Para quê | testar a tarefa no seu banco local | levar o schema acumulado da `dev` para o repositório |
| Vai para o repositório? | **nunca** — sai com `downgrade` antes do PR | sim, numa branch `chore/<id>-consolidar-migrations` |
| O que leva a mudança adiante | a seção **Banco** da descrição do PR | o próprio arquivo |

O PR de tarefa muda `models.py` (e o que depende dele) e **nenhum arquivo em
`alembic/versions/`**. Se trouxer, o check `qualidade` falha com
`PR de tarefa não traz migration (ADR-0009)`.

### Por quê

Na sprint 2, cada PR trazia a própria migration, gerada a partir da `dev` do dia em que a
branch saiu. Dois PRs com o mesmo pai viravam dois *heads* no Alembic: o primeiro
mergeava, todos os outros caíam no CI. O conserto era sempre o mesmo `alembic merge`, um
commit novo — e commit novo **descarta a aprovação**. A sprint fechou com 20 migrations,
9 delas só de merge. A consolidação troca essa corrida por um passo só, feito por quem
lê todas as mudanças de banco juntas.

Passo a passo de quem desenvolve: [Mudar o banco numa tarefa](../tutoriais/mudar-o-banco.md).

## As regras

1. ✅ **Autogenerate nunca vai para o repositório sem leitura linha a linha.** Renomear
   coluna, para o autogenerate, é `drop` + `add` — e o `drop` apaga o dado. Vale também
   para a temporária: é lendo ela que você descobre o que escrever na seção Banco.
2. ✅ **Migration nunca roda no startup do container.** É passo dedicado do pipeline,
   antes de o back novo subir.
3. ✅ **Conflito de heads se resolve com `alembic merge`**, nunca editando
   `down_revision` à mão. Com a consolidação, isso vira exceção.
4. ✅ **Rollback em produção é avançar**: uma migration nova que corrige. O `downgrade`
   da temporária é a única exceção — ela nunca saiu da sua máquina.
5. ✅ **Mudança destrutiva em passos**: adicionar → migrar os dados → remover. Os passos
   caem em consolidações diferentes, e a seção Banco diz em qual passo o PR está.
6. ✅ **Antes do PR, o seu banco local volta ao head da `dev`**: `alembic current`
   mostra o mesmo que `alembic heads`.
7. 🔒 **Uma head só.** O CI falha se `alembic heads` devolver mais de uma.

## A seção Banco do PR

É o único canal entre quem entendeu a mudança e quem vai escrever a migration
consolidada. Um PR que muda `models.py` diz:

- **o que mudou**: tabela, coluna, tipo, índice, constraint;
- **o que o autogenerate não sabe** — todo ajuste que você fez à mão na temporária:
  rename (`op.alter_column(..., new_column_name=...)`), backfill, `server_default` em
  tabela que já tem linhas, passo de mudança destrutiva;
- ou **"nenhum ajuste manual"**, quando é o caso. Seção vazia não vale.

```markdown
## Banco

- O que mudou: tabela `links` ganha `department_id` (UUID, nulo), com índice.
- Ajustes manuais na migration temporária: nenhum ajuste manual.
```

## O modelo de dados

O time desenhou o modelo no dbdiagram.io. A fonte literal e a leitura dela — o que cada
tabela resolve, o que ainda está pendente — ficam no `creed-ai-context`:
[`modelo-de-dados.md`](https://github.com/creed-educa-ai/creed-ai-context/blob/main/context/modelo-de-dados.md).

!!! warning "O diagrama não é a migration"
    Não gere migration a partir do `.dbml`. Tabela entra no banco quando uma tarefa a
    cria em `models.py`. O diagrama usa nomes em português (`Vinculo`, `Setor`) que no
    código viraram `Link` e `Department`.

## Revisando a migration consolidada

Para quem consolida e para quem revisa o PR de consolidação, que é sempre de cuidado
máximo:

- [ ] Algum `drop_column` ou `drop_table` que deveria ser rename?
- [ ] `nullable=False` em coluna nova de tabela que já tem dados? Precisa de default, ou
      de três passos.
- [ ] Cada ajuste pedido nas seções Banco dos PRs mergeados desde a última consolidação
      está no arquivo?
- [ ] Índice em coluna nova usada em `WHERE`, `JOIN` ou agregação?
- [ ] O tipo bate com o `models.py`, e o `models.py` com o `schemas.py`?
- [ ] O CI rodou `alembic upgrade head` num banco limpo e `alembic check` não acusou
      diferença?
