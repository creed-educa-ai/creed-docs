# Frontend

React · TypeScript · Vite · Tailwind v4 · shadcn/ui · Redux Toolkit · i18next · Vitest.

**Molde:** [`src/features/authentication/`](https://github.com/creed-educa-ai/creed-frontend/tree/dev/src/features/authentication)
— a primeira feature ligada ao backend. Ele tem uma tela só; para fluxo com várias
telas, o exemplo é [`src/features/responses/`](https://github.com/creed-educa-ai/creed-frontend/tree/dev/src/features/responses)
(o questionário).

!!! warning "`respondentes` não é molde"
    O README do front ainda aponta `features/respondentes/` como exemplo. Ela é resíduo
    do scaffold: o `respondentesApi.ts` chama uma rota que **não existe mais** no back
    (responde 404), e o slice usa a máquina de status antiga. Copie dela, no máximo, a
    navegação entre as telas de demográficos — nunca a integração.

## Anatomia de uma feature ✅

```
src/features/<feature>/
├── <Feature>View.tsx       a tela
├── <feature>Slice.ts       estado (Redux Toolkit) e thunks
├── <feature>Api.ts         as chamadas HTTP
└── *.test.ts(x)            ao lado do que testam
```

A pasta tem **o mesmo nome do domínio do backend**: o back tem `responses`, o front
tem `responses`. Página que não espelha domínio (`boas-vindas`, `sobre`) é exceção
aceita: não chama o backend.

## As camadas

| Camada | Arquivo | Faz | Não pode |
|---|---|---|---|
| Integração | `<feature>Api.ts` | montar path, query e corpo sobre o `apiClient` | conhecer Redux ou React; `try/catch` |
| Estado | `<feature>Slice.ts` | guardar o que veio do servidor, o status e o erro já como mensagem | fazer `fetch`, formatar texto de tela |
| UI | `<Feature>View.tsx` e componentes | desenhar e capturar interação | fazer `fetch`, agregar dado |
| Compartilhado | `src/lib/`, `src/hooks/`, `src/components/` | o que mais de uma feature usa | conhecer uma feature específica |

### Sinais de que a camada furou

| Sinal | O que está errado |
|---|---|
| View importando `@/lib/apiClient` | a tela falando HTTP — passe pelo `<feature>Api.ts` |
| `fetch(` ou `axios` fora do `apiClient` | HTTP fora do lugar |
| `<feature>Api.ts` importando `@reduxjs/toolkit` | integração sabendo de estado |
| `.reduce(` na View para montar um indicador | agregação no front: é do backend |
| `src/components/` importando de `src/features/` | compartilhado dependendo de específico |

## O que vai — e o que não vai — para o Redux

| Estado | Onde mora |
|---|---|
| Dado que veio do servidor | slice da feature |
| Status da requisição | slice |
| Erro da requisição | slice, já como mensagem pronta — a View não interpreta `ApiError` |
| Campos de formulário | `react-hook-form` ([Formulários](../codigo/formularios.md)) |
| Aberto/fechado, aba ativa | `useState` no componente |
| Valor derivado (total, filtrado, ordenado) | seletor, nunca um campo a mais no state |

## A máquina de status ✅

```ts
status: 'idle' | 'loading' | 'ready' | 'error'
```

Uma só, igual em toda feature, decidida pelo time em 2026-09-29. A View cobre os
quatro casos — inclusive **`ready` com lista vazia**, que é uma mensagem de vazio, não
ausência de estado. Feature que só desenha o caso feliz volta na review.

Dois slices antigos ainda não seguem: o `authenticationSlice` usa `authenticated` no
lugar de `ready`, e o `respondentesSlice` usa a versão em português. Feature nova segue
a máquina acima.

## Onde mora o compartilhado

| O quê | Onde |
|---|---|
| Cliente HTTP (base `/api/v1`, cabeçalho, erro como `ApiError`) | `src/lib/apiClient.ts` — use, não reimplemente |
| Sessão guardada no navegador | `src/lib/session.ts` |
| Schemas zod | `src/lib/validadores.ts` |
| `cn()` para juntar classes | `src/lib/utils.ts` |
| Hook reutilizável | `src/hooks/` |
| Tipos do backend | `src/types/api.ts` ([Contrato](contrato.md)) |
| Primitivos de UI (shadcn) | `src/components/ui/` |
| Layout, modais e componentes de mais de uma feature | `src/components/` |
| Store, rotas, hooks tipados do Redux | `src/app/` |
| Textos (pt-BR, en) | `src/i18n/locales/` |

**Regra dos dois usos:** o primeiro uso mora na feature; o segundo sobe para `lib/`,
`hooks/` ou `components/`.

## Proxy da API

O Vite faz proxy de `/api` para `http://localhost:8000`. Nunca escreva URL absoluta de
backend no código: em produção o caminho é outro, e o `apiClient` já resolve.

## Ainda em consolidação 🟡

- **Nome da pasta de feature**: convivem `authentication`, `responses` (inglês),
  `respondentes` (português) e `boas-vindas`, `aguarde-confirmacao` (kebab-case).
- **Chaves de i18n**: hoje todas em português (`autenticacao`, `formulario`), e o
  ADR-0005, ainda proposto, pede inglês.

Ver [Em consolidação](../referencia/em-consolidacao.md). Passo a passo:
[Criar uma feature no front](../tutoriais/criar-feature.md).
