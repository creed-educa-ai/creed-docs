# Branches

## O fluxo

```
main  ← produção. Só recebe merge vindo de dev.
 ↑
dev   ← branch padrão. Alvo de todo PR de tarefa.
 ↑
sua branch de tarefa
```

| Branch | Regra |
|---|---|
| `main` | 🔒 sem push direto, sem force push, não pode ser apagada. Recebe só o merge de release vindo da `dev` (ou um `hotfix`) |
| `dev` | 🔒 as mesmas proteções. É a branch padrão: o GitHub já sugere ela como alvo do PR |
| de tarefa | uma por subtarefa do ClickUp. Sai da `dev` e volta para a `dev` |

Isso vale para `creed-backend`, `creed-frontend` e `creed-infrastructure`. Os dois
repositórios de documentação — `creed-ai-context` e este, `creed-docs` — não têm
`dev`: o PR vai direto para a `main`.

Começando uma tarefa:

```bash
git checkout dev
git pull
git checkout -b feat/86ab12-filtrar-respondentes
```

## Nome da branch 🔒

```
<slug>/<id-clickup>-<contexto-em-2-a-4-palavras>
```

| Parte | O que é | Regra |
|---|---|---|
| `feat` | o tipo de trabalho | um dos slugs da tabela abaixo |
| `86ab12` | ID da tarefa no ClickUp | minúsculas e números, com **pelo menos um dígito** |
| `filtrar-respondentes` | contexto em 2 a 4 palavras | minúsculas, sem acento, separado por hífen |

| Slug | Quando usar |
|---|---|
| `feat` | funcionalidade nova |
| `fix` | correção de bug |
| `refactor` | muda o código sem mudar o comportamento |
| `perf` | melhora desempenho |
| `test` | adiciona ou corrige teste |
| `docs` | documentação |
| `style` | formatação, sem efeito em comportamento |
| `chore` | manutenção, dependências, configuração |
| `ci` | pipeline, automação |
| `build` | build, empacotamento, Docker |
| `hotfix` | correção urgente que sai de `main` |
| `release` | preparação de versão |

```
✅ feat/1-criar-usuarios
✅ fix/86ab12-corrigir-login-expirado
✅ docs/12-documentar-setup-local

❌ feature/criar-usuarios       slug inválido e sem ID
❌ feat/criar-usuarios          falta o ID do ClickUp
❌ feat/1-Criar_Usuários        maiúscula, underscore e acento
❌ minha-branch                 sem slug
```

### Onde isso é cobrado

1. **No `git push`**, por um hook local que recusa a branch antes de subir. É
   conveniência: `--no-verify` passa por cima.
2. 🔒 **No PR**, pelo check obrigatório `nome-da-branch`. Esse não tem escape: sem ele
   verde, o PR não mergeia.

Se cair em qualquer um dos dois, renomeie e empurre de novo:

```bash
git branch -m feat/1-criar-usuarios
git push -u origin feat/1-criar-usuarios
```

O PR antigo continua preso à branch velha: feche-o e abra outro a partir da nova.

!!! note "Tarefa sem card no ClickUp"
    Nos repos de documentação, mudança sem card é o caso comum. Lá a convenção é usar o
    **número do PR** como ID: `docs/8-camadas-do-backend`. O próximo número aparece em
    `gh pr list --state all --limit 1`.

## Depois do merge

Apague a branch de tarefa (o botão aparece no próprio PR). `main` e `dev` 🔒 não podem
ser apagadas.

## Detalhe que pega uma vez

**Sempre parta da `dev` atualizada.** Branch que saiu de uma `dev` velha traz no diff
coisas que já mudaram, e o revisor vai perguntar por elas. Se a `dev` andou enquanto
você trabalhava, traga-a para a sua branch antes de abrir o PR:

```bash
git checkout dev
git pull
git checkout feat/86ab12-filtrar-respondentes
git merge dev
```
