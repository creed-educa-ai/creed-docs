# Mudar o banco numa tarefa

Para toda subtarefa que mexe em algum `models.py` — tabela, coluna, índice, migração.
A regra por trás está em [Banco e migrations](../arquitetura/banco.md); aqui está a
sequência.

!!! danger "O PR não leva a migration 🔒"
    Você gera a migration para **testar no seu banco** e a remove antes do PR. Se ela
    for junto, o check `qualidade` falha com `PR de tarefa não traz migration
    (ADR-0009)`. Quem escreve a migration que vai para o repositório é o AGES III, na
    consolidação — a partir do que você escrever na seção **Banco** do PR.

## 1. Anote de onde você parte

Com a sua branch atualizada com a `dev`, e o banco local em dia:

```bash
alembic upgrade head
alembic heads
```

Anote a revisão que o `alembic heads` mostrou: é o **head da `dev`**, para onde o seu
banco vai voltar no fim.

## 2. Mude o `models.py`

E, se o domínio é novo, registre-o em `app/models.py` — sem isso o autogenerate não vê a
tabela.

## 3. Gere a migration temporária e leia linha a linha

```bash
alembic revision --autogenerate -m "descricao-curta"
```

Abra o arquivo gerado em `alembic/versions/` e leia **cada operação**. O que procurar:

- [ ] Um `drop_column` + `add_column` onde você queria **renomear**? O autogenerate não
      entende rename: ele apaga a coluna e cria outra, e o dado some. Troque por
      `op.alter_column(..., new_column_name=...)` e **anote isso**.
- [ ] Coluna `nullable=False` nova numa tabela que já tem linhas? Precisa de
      `server_default`, ou de três passos. **Anote.**
- [ ] Precisa preencher dado em linhas que já existem (backfill)? **Anote.**
- [ ] Índice para coluna nova usada em filtro ou `JOIN`?
- [ ] Alguma operação que você não pediu? O autogenerate às vezes enxerga diferença
      antiga entre o model e o banco — não deixe isso passar calado.

Tudo o que você marcou como "anote" vai para a seção Banco do PR.

## 4. Aplique e teste

```bash
alembic upgrade head
uvicorn app.main:app --reload
```

Exercite a mudança: rode a rota no Swagger (<http://localhost:8000/api/v1/docs>), o
fluxo no front, o que a subtarefa pedir.

## 5. Antes do PR: desça, **depois** apague

A ordem importa. Com o arquivo apagado, o Alembic não acha mais a revisão para descer.

```bash
alembic downgrade <head-da-dev>                 # 1º: o banco volta ao head da dev
rm alembic/versions/<arquivo-temporario>.py     # 2º: o arquivo sai
alembic current                                 # 3º: tem que mostrar o head da dev
```

`alembic current` igual ao head que você anotou no passo 1: pronto.

!!! tip "Apagou antes de descer?"
    O banco ficou numa revisão que não existe mais em arquivo. Recrie o banco local do
    zero: `docker compose down -v`, `docker compose up -d db keycloak` e
    `alembic upgrade head`. Os dados locais se perdem — por isso a ordem.

## 6. Escreva a seção Banco

Na descrição do PR ([modelo completo](../git/pull-request.md#descricao)):

```markdown
## Banco

- O que mudou: tabela `participants` ganha `birth_date` (date, nula).
- Ajustes manuais na migration temporária: nenhum ajuste manual.
```

Com ajuste, ele aparece nomeado:

```markdown
- Ajustes manuais na migration temporária: rename `vinculo_id` → `link_id` em
  `form_responses` (autogenerate gerou drop + add; troquei por `op.alter_column`).
```

Seção vazia não vale: é por ela que quem consolida sabe o que o autogenerate não sabe.

## Mudança destrutiva

Remover coluna, trocar tipo com dado, separar uma tabela em duas: são **passos em PRs
diferentes** — adicionar → migrar os dados → remover. Diga na seção Banco em qual passo
este PR está. Na dúvida, fale com um AGES III antes de começar.
