# Nível de código

✅ Padrão do time. É o critério de **Legibilidade** da [review](../git/revisao.md).

> **Sênior é quem resolve o problema de um jeito que o time consegue manter.**
> Código que só o autor entende é um problema adiado, não uma entrega.

## A régua

> Um colega que não acompanhou a tarefa entende o arquivo **em uma passada**, sem
> perguntar nada e sem abrir outros três arquivos para descobrir o que ele faz.

Não passou? Reescreva mais simples — mesmo que a versão complexa seja mais elegante,
mais genérica ou mais curta.

## Onde a qualidade aparece de verdade

| Aparece em | Não aparece em |
|---|---|
| A regra ficar na camada certa | usar um recurso raro da linguagem |
| Nome que diz o que é, no vocabulário do domínio | abreviar para caber em 80 colunas |
| Borda tratada antes de virar bug | abstrair "para quando precisar" |
| Teste que falha no lugar certo | teste que cobre linha sem provar comportamento |
| Erro que aparece cedo e explica o que houve | `try/except` largo que engole tudo |
| Migration que não perde dado | one-liner que faz três coisas |

## Não entra sem justificativa

Estes padrões não são proibidos, mas **o ônus da prova é de quem escreve**: uma linha na
descrição do PR dizendo o que quebrava sem aquilo.

| Padrão | Por que não | O que fazer |
|---|---|---|
| Abstração para **um** caso de uso | a segunda implementação nunca chega | código direto; abstraia na segunda |
| Factory, Strategy ou Registry com uma opção só | indireção sem ganho | `if` ou `match` explícito |
| Metaprogramação (`__getattr__`, `setattr` dinâmico, decorator novo) | quebra o "ir para a definição" e o mypy | função com nome |
| Genérico TS com 3+ parâmetros ou tipo condicional | erro de tipo vira parágrafo ilegível | tipo concreto, ou dois tipos |
| Herança para compartilhar código | acopla e esconde de onde vem o comportamento | composição ou função utilitária |
| Comprehension aninhada em 2+ níveis | denso de ler, difícil de depurar | `for` com variável intermediária nomeada |
| Padrão que **nenhum arquivo do repo usa ainda** | vira dialeto pessoal | siga o molde, ou proponha ao time |
| `any`, `# type: ignore`, `except Exception: pass` | apaga a checagem que o CI faz | trate o caso, ou tipe de verdade |
| Dependência nova | ninguém revisou licença, peso e manutenção | pergunte antes de instalar |

## Simples não é primitivo

Estas coisas **são** esperadas e não contam como complexidade:

- Tipagem completa: Pydantic no back, tipos explícitos no front. Tipo é documentação
  que o CI confere.
- Função pequena com nome do domínio, em vez de um bloco de 60 linhas no service.
- *Early return* em vez de `else` aninhado.
- Constante nomeada em vez de número solto.
- Erro específico (`NotFoundError`) em vez de `Exception`.
- `select(...).group_by(...)` denso no repository — SQL no lugar certo é melhor que
  Python "legível" no lugar errado.

## Comentário

Comentário explica **por quê**, nunca **o quê**. Se o "o quê" precisa de comentário, o
nome está errado.

```python
# ruim: incrementa o contador
contador += 1

# bom: a cliente conta reentrada como resposta nova (P-004)
contador += 1
```

## Quando a solução é mesmo complexa

Existe: agregação de indicador, cálculo de prognóstico, migration com dado de verdade.
Nesses casos:

1. A complexidade fica **isolada** numa função ou módulo com nome claro.
2. O resto do diff continua trivial de ler.
3. A descrição do PR explica a lógica em português.
4. Um teste cobre o caso de borda que motivou a complexidade.

Três arquivos meio difíceis são muito piores que um difícil e dois óbvios.
