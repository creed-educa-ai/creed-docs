# Backend

FastAPI · SQLAlchemy (async) · Alembic · Pydantic · pytest · ruff · mypy (strict).

**Molde:** [`app/domains/users/`](https://github.com/creed-educa-ai/creed-backend/tree/dev/app/domains/users).
Abra os arquivos dele antes de escrever um domínio novo. Ele é canônico na **forma** —
camadas, injeção, schemas, documentação da rota. Os **campos** dele valem para usuário,
não para o assunto novo.

## Anatomia de um domínio ✅

`app/domains/<nome>/`, uma pasta por domínio:

| Arquivo | Faz | Não faz |
|---|---|---|
| `router.py` | recebe, valida pelo schema, chama o service, traduz erro de domínio em status HTTP | regra de negócio, query, importar `models` |
| `service.py` | decide, orquestra, levanta erro de domínio | importar `fastapi` ou `sqlalchemy`, montar query |
| `repository.py` | consulta, agrega, grava (com `flush`) | decidir regra, levantar erro de domínio |
| `schemas.py` | Pydantic, **um schema por direção**, e o `de_model()` que monta a resposta | — |
| `models.py` | a tabela SQLAlchemy | validar entrada |
| `dependencies.py` | monta a cadeia sessão → repository → service e exporta `ServiceDep` | — |

Arquivo nasce quando tem conteúdo: domínio sem rota não tem `router.py`, e `utils.py`
vazio não entra.

## A pergunta que resolve o empate

> Se a resposta muda quando o **produto** muda de ideia, é **service**.
> Se muda quando o **banco** muda de forma, é **repository**.

"Já existe usuário com esse e-mail? Então 409" é produto: service. "Como eu descubro se
existe" é banco: repository.

## O que o CI cobra 🔒

O arquivo [`tests/test_arquitetura.py`](https://github.com/creed-educa-ai/creed-backend/blob/dev/tests/test_arquitetura.py)
roda no `pytest` e quebra o check `qualidade`. Vermelho ali não é estilo: é camada furada.

| Regra | O que o teste procura |
|---|---|
| Service não conhece HTTP nem ORM | `import` de `fastapi` ou `sqlalchemy`, `select(` ou `self.db` no `service.py` |
| Router não conhece a tabela | `import` de `.models` ou `sqlalchemy` no `router.py` |
| Repository não decide regra | `shared.exceptions` ou `HTTPException` no `repository.py` |
| Domínio não importa domínio | `from app.domains.<outro>` fora das exceções declaradas no próprio teste |
| `commit()` só no `get_db` | `.commit()` em qualquer arquivo fora de `app/core/database.py` |
| Sessão só pelo `SessionDep` | `Depends(get_db` solto em qualquer arquivo |
| Todo `models.py` está registrado | o domínio falta em `app/models.py` — sem isso, o autogenerate não vê a tabela |
| Reexportar tipo de `models.py` é declarado | `schemas.py` com `__all__` reexportando um Enum do model, sem motivo escrito no teste |
| Não existe util global | o arquivo `app/shared/utils.py` |

E o [`tests/test_openapi.py`](https://github.com/creed-educa-ai/creed-backend/blob/dev/tests/test_openapi.py)
cobra a documentação da API:

| Regra | Vale para |
|---|---|
| Toda rota tem `summary`, `description` e `operation_id` | **toda rota**, inclusive a sua nova |
| Toda tag usada por uma rota tem descrição em `OPENAPI_TAGS` (`app/main.py`) | **todo domínio novo** com rota |
| Campos com `description` e `examples`, respostas de erro documentadas | as rotas listadas no teste. Rota nova segue a mesma forma ✅ — veja o `router.py` do molde |

### Exceção legítima

Quando uma regra precisa de exceção de verdade, ela entra **na lista do próprio teste**,
com o motivo escrito ao lado. Hoje existem três listas:

| Lista | Permite |
|---|---|
| `COMPOE_COM_SERVICE_DE` | um domínio usar o **service** de outro (ex.: `users` lê o papel pelo `LinkService`) |
| `LEITURA_ENTRE_DOMINIOS` | um domínio de leitura fazer `JOIN` em tabela alheia no repository — vazia por enquanto |
| `REEXPORT_DE_TIPO_PERMITIDO` | `schemas.py` reexportar um Enum do model |

Acrescentar uma linha numa dessas listas é decisão de arquitetura: o revisor vai
perguntar por quê.

## Erros

O vocabulário está em [`app/shared/exceptions.py`](https://github.com/creed-educa-ai/creed-backend/blob/dev/app/shared/exceptions.py):
`NotFoundError`, `ConflictError`, `ValidationError`, `ForbiddenError`,
`AuthenticationError`, todos filhos de `DomainError`.

- O **service** levanta o erro de domínio. Ele não sabe o que é status code.
- O **router** captura e traduz: `except ConflictError as exc: raise
  HTTPException(status.HTTP_409_CONFLICT, exc.message) from exc`.
- O **repository** não levanta erro de domínio: devolve `None`, e o service decide
  se aquilo é 404. 🔒

## Transação

A unidade de trabalho é a **requisição HTTP**. O `get_db()` de `app/core/database.py`
faz `commit` no fim da requisição e `rollback` em qualquer exceção.

| Camada | Faz |
|---|---|
| `repository.py` | `flush()` quando precisa do id gerado; **nunca** `commit()` 🔒 |
| `service.py` | nada: não abre nem fecha transação |
| `router.py` | nada |

## Schemas ✅

- **Um schema por direção.** Entrada e saída nunca são o mesmo schema: `UserCreate`
  entra, `UserResponse` sai. Campo gerado pelo servidor (`id`, `created_at`) existe só
  na saída.
- **Sufixos**: `<Entidade>Create`, `<Entidade>Update`, `<Entidade>Response`. Um campo
  comum aos dois lados pode subir para `<Entidade>Base`.
- **A montagem da resposta é um `@classmethod de_model()`** no schema de saída, nunca
  no router.
- **Campo documentado**: `Field(description=..., examples=[...])`. É o que aparece no
  Swagger e o que o front lê para escrever o tipo.

## Onde mora o compartilhado

| O quê | Onde |
|---|---|
| Função pura de um domínio só | `app/domains/<x>/utils.py` — é aqui que tudo nasce |
| Constante nomeada de um domínio só | `app/domains/<x>/constants.py` |
| Erros de domínio | `app/shared/exceptions.py` |
| Guarda de papel (`require_role`) | `app/shared/authorization.py` |
| Enums compartilhados, schemas de erro, validadores | `app/shared/enums.py`, `schemas.py`, `validators.py` |
| Paginação | `app/shared/paginacao.py` (`PaginaDe[T]`) |
| Config, sessão de banco | `app/core/` — é fundação, não conhece domínio nenhum |
| Cliente de serviço de fora | `app/external_services/<serviço>/` |

**Regra dos dois usos:** o primeiro uso mora no domínio; no segundo domínio que
precisar, sobe para `app/shared/<assunto>.py`. Arquivo de compartilhado tem nome de
assunto. `app/shared/utils.py` é proibido 🔒 — "utils" não é assunto.

## Serviços externos

Pacote irmão de `domains/`, com a forma de um domínio menos o que ele não tem: sem rota,
sem tabela, sem regra. O exemplo existente é
[`app/external_services/keycloak/`](https://github.com/creed-educa-ai/creed-backend/tree/dev/app/external_services/keycloak).

- Só o `service.py` do domínio dono chama o cliente — nunca o router.
- Toda chamada tem **timeout**, vindo de `settings` (`KEYCLOAK_TIMEOUT_SECONDS`).
- Erro de biblioteca (`httpx.HTTPError`) morre no cliente: o que sai dele é erro com
  nome do serviço (`KeycloakUnavailableError`), e o service decide o status.
- Teste não toca a rede: o cliente tem um ponto de troca (`build_http_client`) para o
  teste injetar um falso.

O webhook que um serviço externo chama de volta é **rota do domínio dono** — quem entra
pela porta HTTP entra pelo domínio.

## Leitura entre domínios

| Operação | Domínio de leitura (`dashboards`, `relatorios`) | Demais domínios |
|---|---|---|
| Usar dado de outro domínio | `JOIN` no repository, declarado em `LEITURA_ENTRE_DOMINIOS` | pelo `service.py` do dono, declarado em `COMPOE_COM_SERVICE_DE` |
| Escrever em tabela de outro domínio | **nunca** | **nunca** — pelo service do dono |

## Domínios que ainda são casca

`organizacoes`, `prismas`, `prognosticos`, `relatorios` e `dashboards` têm a pasta e um
`router.py` marcado como **STUB**: a rota está registrada e responde 404. `insights` e
`documents` têm tabela, mas nenhuma rota. Antes de integrar o front com um domínio, abra
o `router.py` dele — ver [Contrato front ↔ back](contrato.md#o-dominio-existe-mesmo).

## Ainda em consolidação 🟡

- **Nome dos métodos.** Repository grava com `create` em quatro domínios e com `insert`
  em três; service mistura `create`, `create_user_service` e `get_link_by_id_service`.
  Até o time decidir, siga o domínio que você está mexendo.
- **`de_model` × `from_model`**, e o `FormRead` no lugar de `FormResponse`: uma
  exceção cada no código.

Detalhes e contagem em [Em consolidação](../referencia/em-consolidacao.md).

Passo a passo para criar um domínio: [Criar um domínio no back](../tutoriais/criar-dominio.md).
