# Contribuir com esta doc

Este site é a régua do time. Uma frase ambígua aqui vira uma cobrança errada na review
de alguém, então ela é tratada com o mesmo rigor do código: **toda mudança passa por PR**.

## O gatilho mais comum

A review pediu uma coisa que não estava escrita aqui. Isso é falha da documentação, não
de quem abriu o PR — e o conserto é um PR **neste** repositório, com a regra que faltou.

## Como propor

1. Edite pelo GitHub — o ícone de lápis no topo de cada página abre o arquivo — ou
   clone o repositório:

    ```bash
    git clone https://github.com/creed-educa-ai/creed-docs.git
    ```

2. Branch no padrão de sempre ([Branches](../git/index.md#nome-da-branch)). Sem card no
   ClickUp, o ID é o número do PR: `docs/14-regra-de-paginacao`.
3. PR contra a **`main`** — este repositório não tem `dev`.
4. O CI constrói o site com os links conferidos. Link quebrado reprova o PR.
5. Mergeado na `main`, o site se atualiza sozinho em um ou dois minutos.

## Rodar o site na sua máquina

```bash
py -3.12 -m venv .venv          # macOS/Linux: python3.12 -m venv .venv
.venv\Scripts\activate          # macOS/Linux: source .venv/bin/activate
pip install -r requirements.txt
mkdocs serve
```

Abre em <http://127.0.0.1:8001/creed-docs/> e recarrega a cada arquivo salvo. Antes do
PR, rode o que o CI roda:

```bash
mkdocs build --strict
```

## Onde cada coisa vai

| Você quer documentar | Vai em |
|---|---|
| Regra de branch, commit, PR, review | `docs/git/` |
| Como o sistema é organizado e por quê | `docs/arquitetura/` |
| Como se escreve código (nomes, testes, forma) | `docs/codigo/` |
| Como uma tarefa anda, do card ao merge | `docs/processo/` |
| Uma sequência de passos com comandos | `docs/tutoriais/` |
| Vocabulário, decisões, pontos abertos | `docs/referencia/` |

Página nova entra também no `nav:` do `mkdocs.yml`.

Na dúvida entre regra e tutorial: **regra** diz como deve ser ("migration é lida linha a
linha"); **tutorial** diz em que ordem fazer ("gere, leia, aplique, desça, apague").

## Toda regra tem marca

| Marca | Use quando |
|---|---|
| 🔒 | um hook, o CI ou o ruleset cobra. Diga **qual** |
| ✅ | ADR aceito, cartilha de contribuição, decisão registrada do time — ou o código inteiro já segue |
| 🟡 | o código diverge ou a decisão está aberta. Registre também em [Em consolidação](em-consolidacao.md) |

**Antes de marcar uma regra como ✅, confira o código.** Se ele diverge, a regra é 🟡 —
mesmo que esteja escrita em outro lugar. Este site descreve o que o time faz e decidiu,
não o que um documento gostaria que fosse.

## Estilo

- **Português**, frase curta e afirmativa: "Router não tem regra de negócio", não
  "recomenda-se evitar".
- **Diga o sinal, não só a regra**: "sinal de que furou: `router.py` importando
  `models`".
- **Exemplo concreto** vale mais que princípio abstrato. Tabela de ✅/❌ é melhor que
  parágrafo.
- **Diga o porquê.** Regra sem motivo vira folclore na segunda turma.
- **Link que abre**: para apontar código, use o link do GitHub (`.../blob/dev/...`), não
  só o caminho.
- **Não copie o que o código já diz.** Lista de rotas e de arquivos envelhece na primeira
  sprint; o motivo de uma estrutura, não.

## Checklist do PR de documentação

- [ ] `mkdocs build --strict` passa.
- [ ] Toda regra nova tem marca, e as 🟡 estão em [Em consolidação](em-consolidacao.md).
- [ ] A regra nova não contradiz um ADR aceito. Se contradiz de propósito, o PR diz
      isso e um AGES III aprova.
- [ ] Nenhuma credencial, token ou dado pessoal real — **o site é público**.
- [ ] Se a regra também está no `creed-ai-context`, o PR avisa: lá precisa de ajuste.
