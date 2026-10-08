# O que a review cobra

Esta página serve aos dois lados do PR: **quem abre** sabe de antemão o que vai ser
perguntado, e **quem revisa** tem a mesma régua de todo mundo.

Com **uma** aprovação obrigatória, quem revisa é a única revisão do PR. Aprovar é
assinar embaixo.

## A régua: o que é exigência

Um item aberto em **Corretude, Camadas, Dados ou Segurança** é sempre exigência. O PR
não é aprovado com ele aberto.

### Corretude

- Faz o que a subtarefa diz — e só isso.
- Borda tratada: lista vazia, nulo, valor no limite, erro vindo do backend.
- Erro não é engolido: nada de `except: pass` nem `.catch(() => {})` sem tratamento.

### Camadas

- `router` sem regra de negócio, `service` sem query, `repository` sem decisão
  ([Backend](../arquitetura/backend.md)). Boa parte disso é 🔒: o
  `tests/test_arquitetura.py` quebra o CI.
- O front não agrega: soma, contagem e agrupamento vêm prontos do back
  ([Arquitetura](../arquitetura/index.md#as-tres-regras)).
- Domínio não importa outro domínio, fora das exceções declaradas no teste de
  arquitetura.

### Contratos

- `schemas.py` e `src/types/api.ts` batem ([Contrato front ↔ back](../arquitetura/contrato.md)).
- Mudança de contrato de API é **aditiva**, ou o par front/back está combinado na
  subtarefa.

### Dados

- 🔒 PR de tarefa sem arquivo em `alembic/versions/`.
- Se `models.py` mudou, a seção **Banco** do PR bate com o diff e nomeia todo rename,
  backfill e default ([Mudar o banco](../tutoriais/mudar-o-banco.md)).
- Coluna nova usada em filtro ou agregação tem índice.

### Segurança

- Nenhuma credencial, token, `.env` real ou dado pessoal real no diff — nem em teste,
  nem em seed.
- Entrada validada na fronteira: `schemas.py` no back, schema zod no formulário do front.

### Testes

- Existem, estão no nível certo e **falhariam sem a mudança**
  ([Testes](../codigo/testes.md)).
- Bug corrigido tem o teste que reproduz o bug.

### Legibilidade

- O revisor entende cada arquivo **em uma passada**, sem abrir outros três.
- Nada da lista de [nível de código](../codigo/index.md#nao-entra-sem-justificativa)
  entrou sem uma linha de justificativa no PR.

"Não entendi este trecho" é exigência válida, não implicância. A resposta certa é
deixar o trecho mais simples, não explicar no comentário.

## O que a review não cobra

- **Itens 🟡** ([Em consolidação](../referencia/em-consolidacao.md)). Se o código segue o
  vizinho mais próximo, está certo até o time decidir.
- **Gosto pessoal.** "Eu teria feito com `map`" é, no máximo, sugestão.
- **Problema que já existia antes do PR.** Vira anotação ou tarefa nova, não exigência
  neste PR.

## Mais cuidado em alguns PRs

Nem todo PR pede o mesmo esforço. Estes pedem leitura mais lenta, e o revisor deve
rodar a branch:

| PR que toca | Por quê |
|---|---|
| migration (PR de consolidação) | é o ponto com mais chance de perder dado |
| autenticação e permissão | erro aqui vira acesso indevido |
| contrato de API | quebra o outro repo sem o diff mostrar |
| agregação de indicador | número errado na tela da cliente parece certo |
| infraestrutura e CI | afeta todo mundo de uma vez |

## Para quem revisa

1. **Leia o diff inteiro**, arquivo por arquivo — não revise pelo resumo.
2. **Leia o arquivo ao redor** do que mudou: camada furada só aparece no arquivo inteiro.
3. **Rode a branch** se a mudança for visual ou de comportamento.
4. **Separe exigência de sugestão** em cada comentário. ✅ Isso está na cartilha:
   o autor precisa saber o que bloqueia o merge.
5. Aponte o **arquivo e a linha**, o problema e o que fazer:

```markdown
**Exigência** — `service.py:42`: a regra de "um vínculo por organização" está no
router. Mover para `LinkService.create`, que já recebe o `organization_id`.

**Sugestão** — `LoginView.tsx:17`: o nome `data` não diz o que é; `session` diria.
```

### Vereditos

| Veredito | Quando |
|---|---|
| **Aprovado** | nenhum item aberto |
| **Aprovado com ressalva** | só sugestões pendentes, ditas como sugestão |
| **Mudanças necessárias** | qualquer item de Corretude, Camadas, Dados ou Segurança aberto |

Não aprove por educação, nem por pressa da sprint. Uma aprovação com item aberto passa
o problema para quem mexer no código depois.
