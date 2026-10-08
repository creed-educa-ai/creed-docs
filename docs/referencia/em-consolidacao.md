# Em consolidação

Pontos em que **o código ainda não tem um padrão único**, ou em que a decisão está
aberta. Toda regra 🟡 do site aponta para cá.

!!! abstract "Enquanto um ponto estiver aqui"
    - Siga o **vizinho mais próximo**: o arquivo, o domínio ou a feature que você está
      mexendo.
    - A review **não bloqueia** PR por isso, e não pede para "arrumar de passagem" o
      que já existe.
    - Renomear em massa para alinhar é tarefa própria, depois da decisão — nunca parte
      de outra tarefa.

Levantamento feito na `dev` do `creed-backend` (`4e30d36`) e do `creed-frontend`
(`686299f`) em 2026-10-08. Quando um ponto for decidido, ele sai daqui e a regra vira
✅ na página dela.

## Backend

### Nome do método que grava, no repository

| Hoje | Onde |
|---|---|
| `create` | `forms`, `participants`, `users`, `responses` (resposta de formulário) |
| `insert` | `links`, `questions`, `responses` (resposta de pergunta) |

O `responses` usa os dois nomes no mesmo arquivo. **Falta decidir** um nome.

### Nome dos métodos do service

Convivem três formas: o verbo puro (`create`, `get` em `forms` e `questions`), o verbo
com o assunto (`create_participant`, `create_form_response`) e o sufixo `_service`
(`create_user_service`, `create_link_service`, `get_link_by_id_service`). A proposta
do `creed-ai-context` é o service nomear o **caso de uso** (`register`, `submit`), sem
sufixo. **Falta decidir.**

### `de_model` × `from_model`

Seis domínios montam a resposta com `de_model()`; o `links` usa `from_model()`. O ADR-0005
(proposto) pediria `from_model`, em inglês. **Falta decidir** — e, decidido, renomear é
uma tarefa só.

### O sufixo do schema de saída

Os schemas de saída terminam em `Response` (`UserResponse`, `LinkResponse`,
`QuestionResponse`…); o `forms` tem `FormRead`. A regra é `Response`; o `FormRead` é a
exceção a corrigir quando alguém mexer no `forms`.

### Valores de enum

| Em português | Em inglês |
|---|---|
| `LinkType` (`emprego`…), `Roles` (`admin`, `gestor`, `respondente`), valores de `Prisma` (`plasticidade_humana`…) | `QuestionType`, `QuestionSection`, `FormResponseStatus`, `RecordStatus` |

A decisão de 2026-09-29 manteve os valores de **vínculo** em português "por enquanto".
Para enum novo, **falta decidir** o idioma.

### Paginação em português

`PaginaDe[T]` (`app/shared/paginacao.py`), com os campos `itens`, `pagina` e
`tamanho_pagina`, ainda não tem nenhum uso. O ADR-0005 marcou o arquivo como a exceção
mais barata de renomear — **antes** do primeiro endpoint de listagem, que vai torná-lo
contrato com o front.

### Teste contra banco de verdade

A suíte não fala com o banco. Funciona para as camadas de hoje, mas não testa `GROUP BY`
— e os primeiros domínios de agregação (`dashboards`, `relatorios`) vão precisar. O CI já
tem um Postgres de serviço. **Falta decidir** como e quando a suíte passa a usá-lo.

### Tabelas no singular

`user`, `form` e `answer` nasceram no singular; as outras seis estão no plural.
Tabela nova nasce no plural. Renomear as três é migration com risco de dado — **não está
decidido**.

## Frontend

### Nome da pasta de feature

`authentication`, `responses` (inglês) · `respondentes`, `cadastro` (português) ·
`boas-vindas`, `aguarde-confirmacao` (kebab-case). **Falta decidir** idioma e caixa
para feature nova que não espelha domínio do back.

### Chaves de i18n

Todos os namespaces estão em português (`comum`, `autenticacao`, `formulario`); o
ADR-0005 (proposto) pede inglês. Mudar depois é renomear chave em todos os arquivos de
idioma. **Falta decidir.**

### Resíduos conhecidos

Não são decisão aberta — são código que ficou para trás de uma decisão já tomada:

| O quê | Decidido | Hoje |
|---|---|---|
| Máquina de status | `idle · loading · ready · error` | `authenticationSlice` usa `authenticated`; `respondentesSlice`, a versão em português |
| Molde do front | `authentication` | o README do `creed-frontend` ainda aponta `respondentes` |
| `respondentesApi.ts` | o domínio do back foi removido | a feature ainda chama `/respondentes`, que responde 404 |

## Arquitetura

### Convite por e-mail e tela de senha

A proposta (ADR-0006, P-012) é o Keycloak mandar o convite e a pessoa definir a senha na
página dele, **sem tela de senha no front**. Hoje: o back não chama o Keycloak para criar
usuário, o `docker-compose` não tem servidor de e-mail, e o front tem uma tela de senha
(`AlterarSenhaView`, nas rotas `/primeiro-acesso` e `/recuperar-senha`) que só valida no
navegador. **Falta decidir** o destino da tela.

### O desenho de produção

O ADR-0007 (Amplify + EC2) está proposto; o README do `creed-infrastructure` ainda
descreve EKS, e não há pipeline de deploy no repositório.

## Processo

| Ponto | Situação |
|---|---|
| Linha `IA:` na descrição do PR | proposta do `creed-ai-context`; quase nenhum PR traz |
| Template de PR nos repos | não existe `.github/pull_request_template.md`; a seção Banco, exigida pelo ADR-0009, depende de cada um lembrar |
| Título do PR no padrão de commit | é regra da cartilha, mas boa parte dos PRs ainda usa o nome da branch |
| Numeração do ledger de premissas | `P-003` e `P-004` aparecem duas vezes, com assuntos diferentes |
