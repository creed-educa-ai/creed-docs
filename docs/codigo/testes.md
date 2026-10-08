# Testes e qualidade local

## A suíte que o CI roda 🔒

O check obrigatório `qualidade` roda exatamente isto. Verde na sua máquina = verde no CI.

=== "Backend"

    ```bash
    ruff check .
    ruff format --check .
    mypy app
    pytest
    ```

    O CI também confere que o Alembic tem **uma head só** e que o PR de tarefa não traz
    migration ([Banco](../arquitetura/banco.md)).

=== "Frontend"

    ```bash
    npm run check
    ```

    É `lint` → `format:check` → `typecheck` → `test:run`, na ordem do CI. O CI ainda
    roda `npm run build` no fim, porque só o build exercita o bundling do Vite.

Rode a suíte **antes de abrir o PR**, não depois que o CI falhar: cada empurrão para
consertar o CI é uma notificação para quem revisa.

## Os hooks locais

Instalados no [Primeiro dia](../comecando/index.md). Eles barram o problema antes de ele
sair da sua máquina — mas `--no-verify` passa por cima, e por isso o CI existe.

| Repo | No commit | No push |
|---|---|---|
| backend (`pre-commit`) | `ruff` (com `--fix`), `ruff format`, `mypy --strict`, espaço no fim da linha, chave privada | nome da branch |
| frontend (`husky` + `lint-staged`) | `eslint --fix` e `prettier --write` nos arquivos alterados | nome da branch |

!!! warning "O mypy do hook não é o mesmo do seu `.venv`"
    O hook de commit roda o mypy num ambiente isolado, com dependências e argumentos
    próprios (`--strict`, e também nos testes). Já aconteceu de `mypy app` passar no
    `.venv` e o commit ser barrado logo depois. Para reproduzir o hook:

    ```bash
    pre-commit run mypy --all-files
    ```

## Princípio

Teste existe para **falhar no lugar certo**. Se um teste de rota quebra quando muda
uma regra de negócio, ele está no nível errado — e quando quebrar, ninguém vai saber
onde olhar.

## Backend ✅

`tests/domains/<domínio>/`, um arquivo por camada. **A suíte não fala com o banco**:
as camadas se testam com dublês, que é o que a separação de camadas compra.

| Arquivo | Testa | Com |
|---|---|---|
| `test_service.py` | regra de negócio e casos de borda | repository falso em memória (`FakeUserRepository`) |
| `test_router.py` | contrato: status, formato da resposta, validação de entrada | service falso |
| `test_models.py` | colunas, constraints, defaults declarados | o model, sem sessão |
| `test_repository.py` | o que o repository faz além de repassar (ex.: traduzir `IntegrityError`) | sessão dublê |

O molde é [`tests/domains/users/test_service.py`](https://github.com/creed-educa-ai/creed-backend/blob/dev/tests/domains/users/test_service.py).
Repare na docstring do módulo: ela diz **o que o teste prova e o que não prova**. É um
hábito do time que vale copiar.

!!! note "E query e agregação?"
    Mock de query não testa `GROUP BY`. Quando chegar o primeiro domínio de agregação
    (`dashboards`, `relatorios`), o teste do repository vai precisar de banco de verdade
    — e isso ainda não existe na suíte. 🟡 Ver [Em consolidação](../referencia/em-consolidacao.md).

```bash
pytest tests/domains/users -q        # um domínio
pytest tests/test_arquitetura.py -q  # só as regras de camada
```

## Frontend ✅

| Coisa | Como testar |
|---|---|
| Slice | reducers e transições de status, direto, sem render |
| View | render + interação com `user-event`, sem espiar o estado interno |
| Api | com a rede mockada — não testa o backend |
| Formulário | vazio mostra erro, válido envia uma vez, 409 cai no campo ([Formulários](formularios.md)) |

O teste fica **ao lado** do arquivo testado: `LoginView.tsx` e `LoginView.test.tsx`.

```bash
npm run test          # modo watch, enquanto desenvolve
npm run test:run      # uma vez, como o CI
```

## O mínimo por tarefa

- caso feliz;
- pelo menos um caso de borda: vazio, nulo, limite;
- bug corrigido: **o teste que falha sem a correção**. Escreva o teste, veja vermelho,
  depois corrija.

## Dado de teste

Sintético, sempre. Nunca dado real de pessoa — em fixture, seed, snapshot ou print de
tela no PR.
