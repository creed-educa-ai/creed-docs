# Formulários

Boa parte do CREED é formulário: cadastro, login, o questionário que a pessoa responde.
Formulário feito à mão diverge rápido — cada um valida de um jeito e mostra erro de
outro. Esta página fecha a forma. ✅

## A pilha, que já está instalada

| Peça | Papel |
|---|---|
| `react-hook-form` | estado dos campos, submit, foco no erro |
| `zod` | o schema: o que é válido |
| `@hookform/resolvers/zod` | liga os dois (`zodResolver`) |
| `src/components/ui/field.tsx`, `input.tsx`, `label.tsx` | os campos |
| `react-i18next` | rótulo, placeholder e **mensagem de erro** |

Formulário não é motivo para trazer biblioteca nova.

## As regras

1. **Todo formulário nasce com schema zod.** Nada de `useState` por campo nem de
   `if (!valor)` espalhado no submit.
2. **O schema mora em [`src/lib/validadores.ts`](https://github.com/creed-educa-ai/creed-frontend/blob/dev/src/lib/validadores.ts)**,
   um `export` por schema (`loginSchema`, `cadastroSchema`).
3. **O tipo sai do schema**, não é escrito duas vezes:
   `export type CadastroForm = z.infer<typeof cadastroSchema>`.
4. **O schema espelha o contrato do back**: obrigatório no Pydantic é obrigatório no
   zod; `max_length=200` vira `.max(200)`. Divergiu, o back ganha.
5. **A mensagem de erro é uma chave de i18n**, não texto:
   `z.string().min(1, 'comum:erros.obrigatorio')`. A tela traduz.
6. **O estado do formulário não vai para o Redux.** Ele vive no `react-hook-form` até o
   submit; o que vai para o slice é o **resultado** da chamada.
7. **Erro do back cai no campo certo.** 409 de e-mail repetido vira erro no campo
   e-mail (`setError('email', ...)`), não um alerta solto no topo. Erro que não é de
   campo (5xx) vira mensagem do formulário.
8. **Botão de envio desabilitado enquanto envia**, com o estado vindo de
   `formState.isSubmitting` — não de um `useState` paralelo.

## Acessibilidade — que aqui é lint 🔒

O `eslint-plugin-jsx-a11y` roda no `npm run check`. Na prática:

- todo input tem `<Label htmlFor>` ligado ao `id`;
- a mensagem de erro é associada ao campo (`aria-describedby`) e anunciada
  (`role="alert"`);
- campo inválido carrega `aria-invalid`.

## Teste

Com Testing Library e `user-event`, no mínimo:

- enviar vazio → aparece a mensagem de cada campo obrigatório;
- preencher válido → a função de envio é chamada **uma vez**, com os dados validados;
- back devolve 409 → a mensagem aparece **no campo**.

Formulário sem teste de validação volta na review.

## Sinais de que a regra furou

| Erro | Sinal |
|---|---|
| Validação na mão | `if (!email.includes('@'))` dentro do componente |
| Tipo duplicado | `interface Form` escrita ao lado de um `z.object` igual |
| Mensagem cravada | `'Campo obrigatório'` no lugar da chave de i18n |
| Campo no Redux | `dispatch(setNome(e.target.value))` |
| Erro do back no lugar errado | 409 virando aviso genérico com o campo intacto |
