# Commits

✅ Padrão do time — está na cartilha de contribuição dos repos. Nenhum hook verifica
a mensagem; quem confere é a review.

## Formato

```
<tipo>(<escopo opcional>): <resumo no imperativo>

<corpo: por que a mudança existe>
```

O **tipo** é um dos mesmos slugs do [nome da branch](index.md#nome-da-branch):
`feat`, `fix`, `refactor`, `perf`, `test`, `docs`, `style`, `chore`, `ci`, `build`,
`hotfix`, `release`.

## O resumo

- verbo no **imperativo**: "adicionar", não "adicionado" nem "adiciona";
- começa em minúscula, sem ponto final;
- até 72 caracteres;
- descreve o **efeito**, não o arquivo mexido.

```
✅ feat: permitir filtrar respondentes por país
✅ fix: impedir duplicidade de e-mail no cadastro
✅ chore(deps): subir vite para 5.4

❌ ajustes                     não diz nada
❌ feat: Adicionado filtro.    particípio, maiúscula, ponto final
❌ fix: RespondentesView.tsx   nome de arquivo não é mensagem
```

**Escopo** é opcional. Quando usar, prefira o nome do domínio ou da feature (`users`,
`responses`, `auth`) ou `deps`.

## O corpo

Opcional para mudança óbvia, importante para o resto. Ele responde **por quê** — o diff
já mostra o quê. É onde entram a armadilha que você encontrou, a alternativa que
descartou e o motivo.

Um exemplo de como fica:

```
fix(users): impedir duplicidade de e-mail no cadastro

A busca por e-mail diferenciava maiúsculas, então "Ana@x.com" e
"ana@x.com" viravam dois usuários. O e-mail passa a ser normalizado
em minúsculas no schema de entrada, antes de chegar ao service.
```

## Um commit, uma ideia

Um commit que mistura a correção, a renomeação de uma variável e a formatação de três
arquivos é difícil de revisar e impossível de reverter pela metade. Quando der, separe:
a mudança de comportamento num commit, a arrumação em outro.

!!! warning "Commit novo depois da aprovação"
    🔒 Commit que chega depois da aprovação **descarta** a aprovação, e quem aprovou
    precisa revisar de novo. Antes de pedir review, confira se não falta nada.
