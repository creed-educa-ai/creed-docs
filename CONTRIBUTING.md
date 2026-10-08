# Contribuindo com o creed-docs

O guia completo está no próprio site, em
[Contribuir com esta doc](docs/referencia/contribuir.md)
([versão publicada](https://creed-educa-ai.github.io/creed-docs/referencia/contribuir/)).
O essencial:

- **Toda mudança via PR**, contra a `main`. Este repositório não tem `dev`.
- **Nome da branch** no padrão dos outros repos: `<slug>/<id>-<contexto>`. Sem card no
  ClickUp, o ID é o número do PR — `docs/14-regra-de-paginacao`.
- **Commit** em Conventional Commits, em português, no imperativo:
  `docs(git): explicar o check de nome da branch`.
- **Toda regra nova tem marca**: 🔒 cobrado pelo GitHub · ✅ padrão do time ·
  🟡 em consolidação. Antes de marcar ✅, confira o código.
- **`mkdocs build --strict`** passa antes do PR. Link quebrado reprova no CI.
- **O site é público**: nada de credencial, token ou dado pessoal real.
