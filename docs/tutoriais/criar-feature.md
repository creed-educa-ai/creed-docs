# Criar uma feature no front

**Molde:** [`src/features/authentication/`](https://github.com/creed-educa-ai/creed-frontend/tree/dev/src/features/authentication)
para a estrutura. Para fluxo com várias telas, veja como
[`src/features/responses/`](https://github.com/creed-educa-ai/creed-frontend/tree/dev/src/features/responses)
navega entre onboarding, perguntas, revisão e envio.

Regras que mandam: [Frontend](../arquitetura/frontend.md) ·
[Contrato front ↔ back](../arquitetura/contrato.md) ·
[Formulários](../codigo/formularios.md) · [UI e responsividade](../codigo/ui.md).

## Passo 0 — o contrato existe?

**Antes de qualquer código**, abra `creed-backend/app/domains/<dominio>/router.py`:

| Você encontra | Caminho |
|---|---|
| Rotas de verdade | transcreva o contrato (passo 2) e siga |
| Docstring "STUB", ou a pasta não existe | o contrato precisa vir **escrito na subtarefa**. Codifique contra ele, e o back ganha quando chegar |

Pular este passo é o que produz tela pronta contra campo que nunca existiu.

## A ordem

1. **Nome da pasta** — o mesmo do domínio do back. Sem acento.
2. **Tipos** em [`src/types/api.ts`](https://github.com/creed-educa-ai/creed-frontend/blob/dev/src/types/api.ts)
   — transcreva os schemas: `snake_case` continua `snake_case`, nulo é `| null`, campo
   gerado pelo servidor só no tipo de resposta.
3. **`<feature>Api.ts`** — funções puras sobre o `apiClient`. É o único lugar com HTTP.
   Sem Redux, sem React, sem `try/catch`.
4. **`<feature>Slice.ts`** — thunks com `createAsyncThunk`, a máquina
   `idle · loading · ready · error`, e o erro já virando mensagem. Registre o reducer em
   [`src/app/store.ts`](https://github.com/creed-educa-ai/creed-frontend/blob/dev/src/app/store.ts).
5. **`<Feature>View.tsx`** — a tela. Desenha os **quatro** estados, inclusive `ready`
   com lista vazia. Sem `fetch`, sem agregação, sem texto escrito direto.
6. **Componentes** — procure antes de criar ([UI](../codigo/ui.md)). Formulário segue
   [Formulários](../codigo/formularios.md).
7. **Textos** — um namespace com o nome da feature em **cada** arquivo de
   `src/i18n/locales/`; o que é compartilhado vai em `comum`.
8. **Rota** em [`src/app/routes.tsx`](https://github.com/creed-educa-ai/creed-frontend/blob/dev/src/app/routes.tsx)
   — dentro da área protegida, se a tela exige login.
9. **Testes** ao lado dos arquivos: o slice (reducers e transições) e o que tiver
   interação de verdade.

## Fatie

Feature inteira num diff só é irrevisável. Corte por **fatia vertical**: contrato +
estado + a tela do caso de uso principal primeiro; os outros casos de uso depois.

## Sinais de que algo saiu do lugar

| Sinal | O que fazer |
|---|---|
| Integrando com um `router.py` sem rota nenhuma | parar: o domínio é STUB |
| Nome em `types/api.ts` que não aparece em nenhum `schemas.py` | campo inventado — volte ao contrato |
| `.reduce(` ou `.filter().length` para montar um número | agregação: é do back |
| `fetch(` na View ou no slice | o HTTP é do `<feature>Api.ts` |
| A View só trata o caso feliz | faltam `loading`, `error` e o vazio |
| Texto em português dentro do JSX | vai para `src/i18n/locales/` |

## Antes de fechar

```bash
npm run check
npm run build
```

E confira a tela em **375, 768 e 1280** de largura no navegador — o teste automatizado
não faz layout.
