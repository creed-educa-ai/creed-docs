# Idioma e nomes

## O que está decidido ✅

- **A interface é em português.** O que a pessoa lê na tela vem de `src/i18n/locales/`,
  com `pt-BR` como idioma principal. Nada de texto visível escrito direto no JSX.
- **Identificador sem acento e sem cedilha**, em qualquer idioma: `prognosticos`, nunca
  `prognósticos`.
- **Domínio novo do back e feature nova do front têm o mesmo nome** (princípio 5 da
  [arquitetura](../arquitetura/index.md#principios)).
- **Vínculo e setor, no código, são `Link` e `Department`** (`links`, `link_id`,
  `department_id`) — decidido pelo time em 2026-09-29. Os **valores** de enum continuam
  em português por enquanto (`emprego`, `gestor`, `respondente`).
- **Commits, PRs e documentação são em português**, que é a língua do time.

## O que está proposto 🟡

O [ADR-0005](../referencia/decisoes.md) propõe **inglês em todo identificador novo** —
pasta, classe, método, variável, rota, schema, chave de i18n — e português só para o
termo que a cliente usa em reunião (`Prisma`, `Prognostico`, `Respondente`, ainda em
aberto). O critério proposto é de exposição:

> Esta palavra é dita numa reunião com a professora? **Sim** → fica como ela fala.
> **Não** → inglês.

O ADR ainda está **Proposto**, e o código está no meio do caminho:

| Onde | O que o código faz hoje |
|---|---|
| Domínios do back criados desde a CREED-23 | inglês: `users`, `links`, `participants`, `forms`, `questions`, `responses`, `insights` |
| Domínios de scaffold | português: `organizacoes`, `prismas`, `prognosticos`, `relatorios` |
| Rotas | inglês nos domínios novos (`/form-responses`, `/organizations/{id}/links`) |
| Chaves de i18n | **português** em todos os namespaces (`autenticacao`, `formulario`, `comum`) |
| Pastas de feature do front | misturado: `authentication`, `responses`, `respondentes`, `boas-vindas` |

**Até o ADR fechar:** em código novo, siga o idioma do vizinho — o domínio ou a feature
que você está mexendo. Domínio novo no back nasce em inglês, como todos os criados
desde a CREED-23.
A review não bloqueia PR por idioma de identificador. Ver
[Em consolidação](../referencia/em-consolidacao.md).

## Backend ✅

| Coisa | Padrão | Exemplo |
|---|---|---|
| Pasta de domínio | `snake_case`, plural | `app/domains/participants/` |
| Model | `PascalCase`, **singular** | `class Participant(Base)` |
| Tabela | `snake_case`, plural | `participants`, `form_responses` |
| Schema | `<Entidade><Direção>` | `ParticipantCreate`, `ParticipantResponse` |
| Rota | plural, kebab-case quando composta | `/api/v1/participants`, `/api/v1/form-responses` |
| Arquivo de teste | `tests/domains/<domínio>/test_<camada>.py` | `tests/domains/users/test_service.py` |

!!! note "Exceções que já estão no banco"
    Três tabelas nasceram no singular — `user`, `form`, `answer` — antes de o plural
    se firmar; as outras seis estão no plural. Tabela nova nasce no plural.
    Renomear as três é migration com risco de dado e não está decidido.

## Frontend ✅

| Coisa | Padrão | Exemplo |
|---|---|---|
| View | `PascalCase` + `View` | `LoginView.tsx` |
| Slice | `<feature>Slice.ts` | `authenticationSlice.ts` |
| Api | `<feature>Api.ts` | `authenticationApi.ts` |
| Teste | `<arquivo>.test.ts(x)`, ao lado do arquivo | `authenticationSlice.test.ts` |
| Componente compartilhado | `PascalCase` em `src/components/` | `SeletorIdioma.tsx` |
| Primitivo do shadcn | o nome que o CLI gera, em `src/components/ui/` | `button.tsx` |

## A regra que resolve empate

**Espelhamento.** Nome novo? Escolha um que funcione dos dois lados — back e front —
antes de criar qualquer arquivo.
