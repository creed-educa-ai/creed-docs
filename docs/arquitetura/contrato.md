# Contrato front ↔ back

O bug mais caro desta fase do projeto é **tela pronta contra um campo que o backend
nunca teve**. Ele aparece semanas depois, na integração, quando já tem tela, slice e
teste em cima do campo errado. Esta página existe para ele não acontecer.

## A fonte da verdade ✅

Nesta ordem:

1. `creed-backend/app/domains/<dominio>/schemas.py` — os schemas Pydantic **são** o
   contrato.
2. `creed-backend/app/domains/<dominio>/router.py` — rota, método, parâmetros, status de
   sucesso e de erro.
3. O Swagger, com o back rodando: <http://localhost:8000/api/v1/docs>. Bom para
   conferir; a fonte continua sendo o código.

Esta página não lista as rotas de propósito: lista de rotas envelhece na primeira
sprint. O Swagger nunca está desatualizado.

## O domínio existe mesmo?

Antes da primeira linha de integração, abra o `router.py` do domínio:

| Você encontra | O que fazer |
|---|---|
| Rotas de verdade, com `summary` e `responses` | transcreva o contrato e siga |
| Docstring **"STUB"** e um `APIRouter` sem rota | **pare**: o domínio não existe. O contrato precisa vir escrito na subtarefa |
| A pasta não existe | **pare**, pelo mesmo motivo |

Quando o contrato ainda não existe no back, ele é **decisão registrada**: vem na
subtarefa (rota, entrada, saída, erros), com uma premissa se algum campo foi decidido
sem a cliente. O front codifica contra esse contrato escrito. Quando o back chegar e
divergir, **o back ganha** e o front se ajusta — e a premissa registrada transforma a
divergência numa conversa de cinco minutos, em vez de arqueologia.

## Transcrever, não interpretar ✅

`src/types/api.ts` é **espelho manual** dos schemas — a geração automática a partir do
OpenAPI está adiada até os contratos estabilizarem. Espelho manual só funciona se for
transcrição:

| No `schemas.py` | No `types/api.ts` |
|---|---|
| `started_at: datetime` | `started_at: string` — **`snake_case` continua `snake_case`**, porque é o que chega no JSON |
| `submitted_at: datetime \| None` | `submitted_at: string \| null` — nulo não é opcional |
| campo com `default`, omitível no POST | `campo?: tipo` |
| campo gerado pelo servidor (`id`, `status`, `created_at`) | existe no tipo de **resposta**, não no de criação |

Não "arrume" os nomes para camelCase, nem junte o tipo de criação com o de resposta
para economizar linha.

## A chamada

Toda chamada passa pelo `src/lib/apiClient.ts`, que resolve a base `/api/v1`, o token,
a renovação de sessão e o erro. O `<feature>Api.ts` é uma lista de funções puras sobre
ele — o molde é o [`authenticationApi.ts`](https://github.com/creed-educa-ai/creed-frontend/blob/dev/src/features/authentication/authenticationApi.ts):

```ts
export const authenticationApi = {
  login: (credentials: LoginRequest) =>
    apiClient.post<SessionResponse>('/authentication/login', credentials),

  getCurrentUser: () =>
    apiClient.get<UserSessionResponse>('/authentication/session'),
};
```

Sem Redux, sem React, sem `try/catch`: quem trata o erro é o slice.

## Erro

O `apiClient` lança `ApiError` com o `status`. O **slice** decide a mensagem; a View
recebe a mensagem pronta.

| Status | O que significa na prática |
|---|---|
| 401 | sessão expirada ou credencial errada — o `apiClient` já tenta renovar uma vez |
| 403 | o papel do vínculo não permite — mensagem de acesso negado, não de falha |
| 404 | o recurso não existe — muitas vezes é estado de vazio, não erro |
| 409 | conflito de regra (e-mail repetido, resposta já enviada) — a mensagem do back vale |
| 422 | o corpo não bate com o schema — **é bug do front**: o contrato foi transcrito errado |
| 5xx | falha do servidor — mensagem genérica e "tente de novo" |

422 em desenvolvimento não vira tratamento bonito na tela: volte ao `schemas.py` e
corrija a transcrição.

## Mudar um contrato que já existe

Contrato publicado tem consumidor. Mudança **aditiva** (campo novo opcional, rota nova)
pode entrar a qualquer momento. Mudança que **quebra** (renomear campo, mudar tipo,
remover rota) precisa estar combinada na subtarefa, com o PR do front planejado junto —
senão a `dev` fica com o front quebrado até alguém perceber.
