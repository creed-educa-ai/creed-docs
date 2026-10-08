# Abrir um PR

Do working tree pronto ao PR com review pedida. As regras estão em
[Pull request](../git/pull-request.md); aqui está a sequência.

## 1. Confira o que vai subir

```bash
git status
git diff dev...HEAD
```

Leia o diff **inteiro**. Arquivo que você não reconhece, `print` de depuração, teste
desligado, `.env`: tire agora. Nada fora do escopo da subtarefa.

## 2. Traga a `dev` para a sua branch

```bash
git fetch origin
git merge origin/dev
```

Conflito? Resolva na sua branch, agora — não no PR.

## 3. Rode a suíte

=== "Backend"

    ```bash
    ruff check . && ruff format --check .
    mypy app
    pytest
    ```

=== "Frontend"

    ```bash
    npm run check
    npm run build
    ```

Mudou `models.py`? Confira também que nada ficou em `alembic/versions/` e que
`alembic current` bate com `alembic heads` ([Mudar o banco](mudar-o-banco.md#5-antes-do-pr-desca-depois-apague)).

## 4. Commit

```bash
git add <arquivos>
git commit
```

Mensagem no [padrão](../git/commits.md): `feat(participants): permitir consultar
participante por documento`, com o porquê no corpo.

Prefira `git add <arquivos>` a `git add .`: é o último ponto em que um arquivo que não
devia subir ainda pode ficar para trás.

## 5. Empurre

```bash
git push -u origin feat/86ab12-consultar-participante
```

O hook de pre-push confere o nome da branch. Recusou? Renomeie
([Branches](../git/index.md#onde-isso-e-cobrado)).

## 6. Abra o PR

=== "Pelo terminal"

    ```bash
    gh pr create --base dev --reviewer <colega>
    ```

    O `gh` pede o título e abre o editor para a descrição. Sem saber a quem pedir, use
    `--reviewer creed-educa-ai/maintainers`.

=== "Pelo GitHub"

    1. Abra o repositório: o GitHub mostra um aviso com a sua branch e o botão
       **Compare & pull request**.
    2. Confira que a base é `dev`.
    3. Em *Reviewers*, escolha um colega — ou `creed-educa-ai/maintainers`.

Em qualquer dos dois:

- **Título** no padrão de commit — troque o que o GitHub sugere a partir do nome da
  branch.
- **Descrição** com o [modelo](../git/pull-request.md#descricao): o que muda, por quê,
  como verificar, premissas, banco, link da tarefa.

## 7. Acompanhe

- Os checks `qualidade` e `nome-da-branch` rodam sozinhos. Vermelho? Clique em
  *Details*, corrija na branch e empurre de novo.
- Comentário de review: responda a cada um. **Exigência** se corrige; **sugestão** se
  discute. Resolva a conversa quando estiver tratada — conversa aberta trava o merge 🔒.
- Aprovado e verde: o merge pode ser feito por você ou por quem aprovou. Depois,
  **apague a branch**.
