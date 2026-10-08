# Criar um domínio no back

**Molde:** [`app/domains/users/`](https://github.com/creed-educa-ai/creed-backend/tree/dev/app/domains/users).
Abra os arquivos dele lado a lado com os seus durante todo o caminho. No fim, a
diferença entre os dois deve ser só o assunto.

Regras que mandam: [Backend](../arquitetura/backend.md) ·
[Banco e migrations](../arquitetura/banco.md) · [Testes](../codigo/testes.md).

## Passo 0 — os campos existem?

**Antes de qualquer código.** O que a tabela guarda é decisão de produto:

| Você tem | Caminho |
|---|---|
| Subtarefa com os campos | siga; ela é a fonte |
| Subtarefa sem os campos | volte para quem publicou — a subtarefa não passou na [Definition of Ready](../processo/definition-of-ready.md) |
| Um campo que você acha que falta | é [premissa](../processo/premissas.md): registre antes de criar a coluna |

Copie do molde a **forma**, nunca os campos. Coluna inventada vira migration, e
migration vira dado.

## A ordem

1. **`models.py`** — a tabela. Classe no singular (`Insight`), tabela no plural
   (`insights`).
2. **Registre o model** em [`app/models.py`](https://github.com/creed-educa-ai/creed-backend/blob/dev/app/models.py),
   uma linha em ordem alfabética. 🔒 O teste de arquitetura reprova o domínio que ficar
   de fora — e sem o registro o autogenerate do Alembic não enxerga a tabela.
3. **Migration temporária**, para testar no seu banco — ver
   [Mudar o banco numa tarefa](mudar-o-banco.md). Ela **não** vai para o PR.
4. **`schemas.py`** — um schema por direção (`<Entidade>Create`, `<Entidade>Response`),
   cada campo com `Field(description=..., examples=[...])`, e o `@classmethod
   de_model()` no schema de saída.
5. **`repository.py`** — classe que recebe a sessão no construtor. Consulta, agrega,
   grava com `flush()`. Não achou? Devolve `None`. Nunca `commit()`, nunca erro de
   domínio. 🔒
6. **`service.py`** — classe que recebe o repository no construtor. A regra de negócio
   mora aqui, e o erro é de `app/shared/exceptions.py`. Sem `fastapi`, sem
   `sqlalchemy`. 🔒
7. **`dependencies.py`** — a cadeia `SessionDep` → repository → service, exportando
   `ServiceDep`. Copie a forma do molde.
8. **`router.py`** — recebe, valida pelo schema, chama o service, traduz o erro de
   domínio em `HTTPException`. Toda rota com `summary`, `description`, `operation_id`
   e as respostas de erro em `responses=` 🔒. Sem importar `models` 🔒.
9. **Registre o router** na tupla do `for _router in (...)` de
   [`app/main.py`](https://github.com/creed-educa-ai/creed-backend/blob/dev/app/main.py),
   e a **tag** do domínio, com descrição, em `OPENAPI_TAGS` no mesmo arquivo 🔒.
10. **Testes** em `tests/domains/<nome>/`: `test_service.py` com repository falso,
    `test_router.py` com service falso ([Testes](../codigo/testes.md)).

`utils.py` (cálculo puro) e `constants.py` (número com significado) nascem só quando
houver o que pôr dentro.

## Precisa de dado de outro domínio?

Use o **service** do dono, recebido em `dependencies.py` — como o `users` faz com o
`LinkService`. E declare a composição em `COMPOE_COM_SERVICE_DE`, em
`tests/test_arquitetura.py`, com o motivo. Sem a declaração, o CI falha 🔒.

## Fatie

Domínio inteiro num diff só é irrevisável. Corte por **fatia vertical**: model + o
primeiro caso de uso de ponta a ponta numa subtarefa; os demais endpoints depois, um por
vez. Não corte por camada ("uma task para os schemas, outra para o service"): uma task
que não entrega comportamento não dá para testar.

## Antes de fechar

```bash
ruff check . && ruff format --check .
mypy app
pytest tests/domains/<nome> -q
pytest tests/test_arquitetura.py tests/test_openapi.py -q
pytest
```

E a checagem que nenhum comando faz: abra o seu domínio e o `users` lado a lado. A
diferença deve ser só o assunto.
