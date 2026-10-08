# Definition of Done

Uma subtarefa está pronta para PR quando **todos** os itens valem. É a lista que a
[review](../git/revisao.md) vai conferir — então confira antes.

## Código
- [ ] Critérios de aceite atendidos — todos, não a maioria.
- [ ] Segue a forma do molde: `users` no back, `authentication` no front.
- [ ] Sem regra de negócio no `router.py`, sem query no `service.py`, sem agregação no
      front.
- [ ] Sem credencial, `.env` ou dado pessoal real no diff.
- [ ] Nada da lista de [nível de código](../codigo/index.md#nao-entra-sem-justificativa)
      sem uma linha de justificativa no PR.

## Entendimento
- [ ] Você explica **cada linha** do diff sem ajuda — de onde veio, por que está naquela
      camada, o que acontece com entrada vazia.
- [ ] Você sabe qual teste falharia se a mudança principal fosse revertida.
- [ ] Você sabe citar uma alternativa que descartou, e o motivo.

Quem não sabe explicar o diff não abre o PR — vale para código escrito à mão, copiado
ou gerado por IA.

## Testes
- [ ] Caso feliz e pelo menos um caso de borda.
- [ ] Bug corrigido tem o teste que falha sem a correção.
- [ ] **Suíte local rodada por você** e verde ([Testes e qualidade local](../codigo/testes.md)).

## Banco — se `models.py` mudou
- [ ] 🔒 Nenhum arquivo em `alembic/versions/` no diff.
- [ ] A migration temporária foi lida linha a linha, testada local, desfeita com
      `alembic downgrade` **e só depois** apagada.
- [ ] `alembic current` igual a `alembic heads`.
- [ ] Seção **Banco** do PR preenchida, com todo ajuste manual — ou "nenhum ajuste manual".

Passo a passo em [Mudar o banco numa tarefa](../tutoriais/mudar-o-banco.md).

## Rastro
- [ ] Premissa usada está registrada e marcada no código ([Premissas](premissas.md)).
- [ ] Documentação tocada quando o comportamento mudou (README do repo, esta doc).

## PR
- [ ] 🔒 Nome da branch no padrão.
- [ ] Título no padrão de commit, descrição com o que muda, por quê e **como verificar**.
- [ ] Link da tarefa do ClickUp.

Os dois itens que mais falham na prática: **rodar a suíte antes de abrir** e
**saber explicar o diff**.
