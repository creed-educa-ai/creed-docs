# creed-docs

Padrões do time do **CREED.ai Educa** — git, PR, review, arquitetura, convenções de
código, processo e tutoriais —, publicados em:

**https://creed-educa-ai.github.io/creed-docs/**

É a fonte normativa do time: o que a review cobra está escrito aqui antes. O conteúdo é
Markdown em [`docs/`](docs/), e o site é gerado com
[Material for MkDocs](https://squidfunk.github.io/mkdocs-material/).

## Rodar na sua máquina

```bash
py -3.12 -m venv .venv          # macOS/Linux: python3.12 -m venv .venv
.venv\Scripts\activate          # macOS/Linux: source .venv/bin/activate
pip install -r requirements.txt
mkdocs serve
```

Abre em <http://127.0.0.1:8001/creed-docs/>. Antes de abrir PR, rode o que o CI roda:

```bash
mkdocs build --strict
```

## Como contribuir

Ver [`CONTRIBUTING.md`](CONTRIBUTING.md). Em resumo: PR contra a `main`, branch no padrão
de sempre, e toda regra nova com a marca 🔒, ✅ ou 🟡.

## Publicação

| Workflow | Quando | O que faz |
|---|---|---|
| `publicar.yml` | todo PR · todo push na `main` | constrói com `--strict` (link quebrado reprova). Na `main`, publica no GitHub Pages |
| `nome-da-branch.yml` | todo PR | o mesmo check de nome de branch dos outros repos |
| `espelhar-gitlab.yml` | todo push | espelha no GitLab da AGES, como os outros repos |

### Configuração única no GitHub

Feita uma vez, por um admin da organização:

1. **Settings → Pages → Build and deployment → Source: GitHub Actions.**
2. **Settings → Rules**: proteger a `main` como no `creed-ai-context` (PR obrigatório).
   Para exigir o build verde, marque o check `build` como obrigatório.
3. Para o espelho: Environment `GITLAB_MIRROR` com `GITLAB_TOKEN` (secret),
   `GITLAB_REPO` e `GITLAB_USER` (variáveis) — igual aos outros repos. Sem ela, o job
   termina verde sem espelhar.

## Trocar de gerador

O Material for MkDocs entrou em manutenção em 2025-11, e o suporte termina em
2026-11-05. O site continua funcionando depois disso — é HTML estático, e as versões
estão travadas em `requirements.txt`.

O sucessor, [Zensical](https://zensical.org/), lê o mesmo `mkdocs.yml`. Por isso o
`mkdocs.yml` só usa a busca e extensões de Markdown, sem plugins. Quando o Zensical
sair da fase alpha, a troca fica assim:

```bash
pip install zensical==<versão>
zensical build --clean          # gera o mesmo site/, a partir do mesmo mkdocs.yml
```

Isso troca duas linhas: o `requirements.txt` e o passo de build em `publicar.yml`. Antes,
confira se o `zensical build` gera o site sem aviso.

## Relação com o `creed-ai-context`

O [`creed-ai-context`](https://github.com/creed-educa-ai/creed-ai-context) é o contexto
que as ferramentas de IA do time leem. Quando ele e este site divergem num padrão, vale
**este site**, e a divergência é bug a corrigir lá.
