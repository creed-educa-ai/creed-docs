# Arquitetura

## O que roda hoje

Na sua máquina, o `docker-compose.yml` do `creed-backend` sobe tudo de que o back
precisa:

| Serviço | Para quê |
|---|---|
| `db` | PostgreSQL — o banco da aplicação e o do Keycloak, cada um no seu schema |
| `keycloak` | login e emissão de token, com o realm importado de arquivo |
| `n8n` | a esteira de IA, chamada por webhook |

O back (FastAPI) roda com `uvicorn`, e o front (React + Vite) com `npm run dev`, que faz
proxy de `/api` para o back. Passo a passo em [Primeiro dia](../comecando/index.md).

## O desenho de produção 🟡

```
Navegador (celular ou desktop)
  │  HTTPS
  ▼
AWS Amplify — os arquivos estáticos do front (build do Vite)
  │  /api
  ▼
┌──────────────────────── EC2 (Docker Compose) ───────────────────────┐
│                                                                     │
│  Back (FastAPI/Uvicorn) ──── webhook ────▶ N8N (esteira de IA)      │
│    │   ▲                                      │                     │
│    │   └──────── webhook de volta ────────────┘                     │
│    │                                                                │
│    └── login e token ──▶ Keycloak                                   │
│                                                                     │
└────┼────────────────────────────────────────────────────────────────┘
     ▼
PostgreSQL no RDS — um schema por componente: app · keycloak · n8n
```

É a proposta do ADR-0007, ainda **Proposto** ([Decisões](../referencia/decisoes.md)).
O README do `creed-infrastructure` ainda descreve o desenho anterior, com EKS. As ideias
centrais:

- **Duas peças de compute, não um cluster.** O front é build estático no Amplify. Back,
  N8N e Keycloak são três containers numa EC2 só, subidos pelo **mesmo
  `docker-compose`** do ambiente local — o que sobe na sua máquina é o que sobe lá.
- **O estado mora no RDS.** Back e Keycloak podem ser recriados do zero sem perda. A
  exceção é o volume do N8N.
- **A esteira de IA é assíncrona.** O back dispara o N8N por webhook e responde na hora;
  o resultado volta por outro webhook. Se o N8N cair, a plataforma continua servindo.

## Princípios

Estes não se negociam numa tarefa. Mudar um deles é ADR, não PR de feature.

1. **Agregação no banco, cálculo no backend, renderização no front.**
2. **Migration nunca roda no startup do container** — é um passo dedicado, antes de o
   back novo subir.
3. **Migration gerada por autogenerate é lida linha a linha** antes de ir para o repo.
4. **CI é obrigatório.** O hook local acelera; o CI garante.
5. **Estrutura por domínio no back e por feature no front, espelhadas** — mesmo nome dos
   dois lados.

## As três regras

| Regra | Onde mora | Sinal de que vazou |
|---|---|---|
| **Agregação no banco** — `SUM`, `COUNT`, `GROUP BY`, janela | `repository.py` | `for` somando lista no service |
| **Cálculo no backend** — regra, derivação, decisão | `service.py` | `if` de regra de negócio no componente React |
| **Renderização no front** — recebe pronto e desenha | `src/features/` | `.reduce(` na View para montar um número de dashboard |

Se o front está somando array para montar um indicador, a arquitetura vazou: o número
devia ter chegado agregado.

## Fronteiras

- **Domínio não lê tabela de outro domínio.** Precisa de um dado alheio? Passe pelo
  `service.py` do dono. A exceção — domínios de leitura como `dashboards` e
  `relatorios` — é declarada no teste de arquitetura ([Backend](backend.md#leitura-entre-dominios)).
- **Front espelha o back.** Domínio novo no back, feature de mesmo nome no front.
- **Serviço externo é pacote à parte**, em `app/external_services/`, e só o `service.py`
  do domínio dono o chama ([Backend](backend.md#servicos-externos)).

## Autenticação

**Quem guarda a senha e emite o token é o Keycloak.** O back não guarda senha: ele
repassa o login ao Keycloak e confere o token em cada requisição.

| Hoje, na `dev` | Onde |
|---|---|
| Login e renovação de sessão pelo endpoint de token do Keycloak | `app/external_services/keycloak/` |
| Conferência do token e do papel do vínculo em cada rota protegida | `app/shared/authorization.py` |
| O usuário da plataforma aponta para o usuário do Keycloak por `keycloak_id` | `app/domains/users/` |
| Realm local importado de arquivo, nunca configurado clicando | `docker/keycloak/realm-creed.json` |

🟡 **Convite e e-mail ainda são desenho, não código.** A proposta (ADR-0006) é o
Keycloak mandar o e-mail de convite e a pessoa definir a senha na página dele, sem tela
de senha no nosso front. Nada disso está implementado: o cadastro recebe o
`keycloak_id` pronto, e o `docker-compose` não tem servidor de e-mail. Ver
[Em consolidação](../referencia/em-consolidacao.md).

## Deploy

O princípio 2 vale desde já, e o desenho proposto o cumpre assim:

1. O pipeline sobe um container descartável e roda `alembic upgrade head`.
2. Esse container termina com código 0.
3. **Só então** o container do back é recriado na imagem nova.

Migration falhou, o passo 3 não acontece, e a versão anterior continua servindo. O front
não entra nesse fluxo: o Amplify publica o build sozinho.

Mexer em infraestrutura — servidor, banco gerenciado, segredos, rulesets do GitHub — é
trabalho dos donos de infra, sempre por PR revisado.
