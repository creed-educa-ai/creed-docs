# Primeiro dia

O objetivo do primeiro dia é um só: **back e front rodando na sua máquina, e a suíte de
testes verde nos dois.** Com isso, qualquer tarefa começa do mesmo lugar.

## Pré-requisitos

| Ferramenta | Versão | Para quê |
|---|---|---|
| git | qualquer recente | tudo |
| Python | **3.12** (o alvo do projeto) | backend |
| Node.js | 20 ou mais (o CI usa 22) | frontend |
| Docker | com `docker compose` | banco e Keycloak locais |
| gh (GitHub CLI) | opcional | abrir PR pelo terminal |

## Caminho rápido: o script de setup

O repositório `creed-ai-context` tem um script que confere os pré-requisitos, clona os
três repos lado a lado, instala as dependências e os hooks de git. **Ele não exige que
você use IA**: com `-f nenhuma`, nada de IA é instalado.

=== "Windows (PowerShell)"

    ```powershell
    git clone https://github.com/creed-educa-ai/creed-ai-context.git $HOME\ages\creed-ai-context
    powershell -ExecutionPolicy Bypass -File $HOME\ages\creed-ai-context\scripts\setup-workspace.ps1 -Ferramentas nenhuma
    ```

=== "macOS / Linux"

    ```bash
    git clone https://github.com/creed-educa-ai/creed-ai-context.git ~/ages/creed-ai-context
    bash ~/ages/creed-ai-context/scripts/setup-workspace.sh -f nenhuma
    ```

O workspace nasce **ao lado** do repo de contexto, então clone-o já dentro da pasta que
vai ser o seu workspace (`ages/` no exemplo). É seguro rodar de novo: repo com alteração
local não é tocado, e `.venv` e `.env` existentes são preservados.

## Caminho manual

Se preferir montar à mão, são estes passos — os mesmos que o script faz.

### Backend

=== "Windows (PowerShell)"

    ```powershell
    git clone https://github.com/creed-educa-ai/creed-backend.git
    cd creed-backend
    py -3.12 -m venv .venv
    .\.venv\Scripts\activate
    pip install -e ".[dev]"
    copy .env.example .env
    pre-commit install --hook-type pre-commit --hook-type pre-push
    ```

=== "macOS / Linux"

    ```bash
    git clone https://github.com/creed-educa-ai/creed-backend.git
    cd creed-backend
    python3.12 -m venv .venv
    source .venv/bin/activate
    pip install -e ".[dev]"
    cp .env.example .env
    pre-commit install --hook-type pre-commit --hook-type pre-push
    ```

O `--hook-type pre-push` instala a verificação do nome da branch. Sem ele, o erro só
aparece no PR.

Subir o banco, aplicar as migrations e rodar a API:

```bash
docker compose up -d db keycloak
alembic upgrade head
uvicorn app.main:app --reload
```

A documentação interativa da API fica em <http://localhost:8000/api/v1/docs>.

!!! warning "Já tem um PostgreSQL instalado na máquina?"
    Ele ocupa a porta 5432 e o container fica à sombra dele. O sintoma é
    `autenticação do tipo senha falhou para o usuário "creed"` com o container
    saudável. Pare o serviço local, ou publique o container em outra porta e ajuste
    `POSTGRES_PORT` no `.env`.

### Frontend

```bash
git clone https://github.com/creed-educa-ai/creed-frontend.git
cd creed-frontend
npm install
npm run dev
```

O `npm install` instala também os hooks de git (husky). O Vite faz proxy de `/api` para
`http://localhost:8000`, então o back precisa estar de pé para as telas que chamam a API.

## Identidade do git — confira antes do primeiro commit

**Os repositórios são públicos**, e o histórico é para sempre. Sem identidade local, o
commit herda o seu `git config --global` — se ele for de uma conta do trabalho, é ela
que fica registrada.

```bash
git config user.name
git config user.email
```

Rode dentro de cada repo. Se aparecer a identidade errada, fixe a local:

```bash
git config user.name "Seu Nome"
git config user.email "seu-email@exemplo.com"
```

## Pronto quando

- [ ] `pytest` verde no `creed-backend` (com o `.venv` ativo)
- [ ] `npm run check` verde no `creed-frontend`
- [ ] <http://localhost:8000/api/v1/docs> abre
- [ ] <http://localhost:5173> abre a tela inicial
- [ ] `git config user.email` mostra o e-mail certo nos dois repos

Travou em algum item? Pergunte a um AGES III ou IV ([Quem é quem](equipe.md)) — setup
quebrado é problema do time, não seu.
