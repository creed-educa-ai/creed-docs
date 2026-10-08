# Glossário

O vocabulário do produto e como cada termo aparece no código. Quase todas as definições
são **interpretação do time**, ainda não confirmada pela cliente — por isso a marca 🟡.
Quando a cliente confirma em reunião, a marca vira ✅.

Termo novo que vai virar nome de tabela, rota ou tela e não está aqui? Registre como
[premissa](../processo/premissas.md) antes de codar. Termo que muda de significado muda
**aqui primeiro**, depois no código.

## Pessoas e organizações

| Termo | Definição | No código | |
|---|---|---|---|
| **Organização** | A instituição à qual as pessoas se vinculam. | `organizacoes` (ainda STUB) | 🟡 |
| **Participante** | A pessoa, independente do vínculo. É onde as respostas da mesma pessoa em duas organizações se encontram. | `Participant`, tabela `participants` | 🟡 |
| **Vínculo** | O elo entre um participante e uma organização: guarda o papel, o tipo e o período. | **`Link`**, tabela `links`; no diagrama do time, `Vinculo` | 🟡 |
| **Setor** | Subdivisão de uma organização a que um vínculo pode pertencer. É dado da organização, não lista fixa. | **`Department`**, `links.department_id` | 🟡 |
| **Tipo de vínculo** | A natureza do vínculo. | `LinkType`: `emprego`, `mentoria`, `academico`, `pessoal` | 🟡 |
| **Papel** | O nível de acesso dentro da organização. Vem do vínculo, não do usuário. | `Roles`: `admin`, `gestor`, `respondente` | 🟡 |
| **Usuário** | O login na plataforma. Nasce de um vínculo, criado por um admin — não existe autocadastro de pessoa. | `User`, tabela `user`, `user.link_id` | 🟡 |
| **Respondente** | A pessoa que responde aos instrumentos. Hoje é um **papel** do vínculo. | `Roles.RESPONDENTE`; no front, a feature `respondentes` | 🟡 |

## O instrumento

| Termo | Definição | No código | |
|---|---|---|---|
| **Formulário** | O instrumento aplicado: um conjunto de perguntas. O dono é a organização, não uma pessoa. | `Form`, tabela `form` | 🟡 |
| **Pergunta** | Um item do formulário: enunciado, ordem, se é obrigatória, e o tipo. | `Question`; `QuestionType`: `objective`, `descriptive` | 🟡 |
| **Seção** | A parte do formulário em que a pergunta aparece na tela. Organiza a tela; o prisma organiza a análise. Valores provisórios. | `QuestionSection`: `profile`, `assessment`, `closing` | 🟡 |
| **Resposta de formulário** | A participação de um vínculo num formulário. Abre em andamento, recebe as respostas e é enviada uma vez; depois do envio, não muda. Uma por vínculo por formulário. | `FormResponse`, tabela `form_responses`; `in_progress` → `submitted` | 🟡 |
| **Resposta** | O que foi respondido em **uma** pergunta: o texto, se descritiva; a alternativa, se objetiva. | `Answer`, tabela `answer` | 🟡 |

## A análise

| Termo | Definição | No código | |
|---|---|---|---|
| **Plasticidade humana** | O conceito-base do produto. A cliente é a autoridade nele. | — | 🟡 |
| **Prisma** | Uma dimensão de análise aplicada às respostas. São cinco: plasticidade humana, empreendedorismo, multiculturalismo, neuroinovação e tomada de decisão. | `Prisma` (enum em `questions`); domínio `prismas` ainda STUB | 🟡 |
| **Insight** | O texto gerado a partir de uma resposta de formulário. | `Insight`, tabela `insights` | 🟡 |
| **Prognóstico** | A projeção gerada a partir dos prismas. | `prognosticos` (STUB) | 🟡 |
| **Relatório** | A saída consolidada e exportável para a organização. | `relatorios` (STUB) | 🟡 |
| **Dashboard** | A visão agregada e interativa dos indicadores. | `dashboards` (STUB) | 🟡 |
| **Esteira de IA** | O fluxo no N8N que o back aciona por webhook assíncrono. | N8N, no `docker-compose` | ✅ |

## Do jeito de trabalhar

| Termo | Definição |
|---|---|
| **Molde** | O domínio (`users`) e a feature (`authentication`) que servem de exemplo de forma. Copia-se a forma, nunca os campos. |
| **Premissa** | Uma interpretação de produto adotada sem a cliente, registrada como afirmação, com ID `P-NNN`. Ver [Premissas](../processo/premissas.md). |
| **STUB** | Um domínio com pasta e rota registrada, mas sem nenhum endpoint de verdade. Responde 404. |
| **Consolidação** | O PR em que um AGES III escreve a migration de todas as mudanças de banco da sprint. Ver [Banco](../arquitetura/banco.md). |
| **Discovery** | A etapa em que AGES III e IV transformam o card do ClickUp em spec e subtarefas. |
| **ADR** | *Architecture Decision Record*: o registro de uma decisão de arquitetura, com as alternativas descartadas. Ver [Decisões](decisoes.md). |
