# UI e responsividade

Duas regras mandam aqui, e as duas vêm do que foi combinado com a cliente: ✅

1. **Responsivo não é caso especial.** A plataforma é usada no celular e no desktop.
   Componente que só funciona no desktop está incompleto, não pronto.
2. **A biblioteca é fechada.** O que está no `package.json` resolve. Pacote novo de UI é
   decisão do time, não do componente da vez.

## O que já existe — use

| Para | Use | Onde |
|---|---|---|
| Primitivos de UI | shadcn/ui (estilo `radix-nova`, base `neutral`) | `src/components/ui/` |
| Ícones | `lucide-react` | — |
| Variantes de um componente | `class-variance-authority` (`cva`) | ver `src/components/ui/button.tsx` |
| Juntar classes | `cn()` de `@/lib/utils` | sempre — nunca template string crua |
| Formulário | `react-hook-form` + `zod` | [Formulários](formularios.md) |
| Animação | `tw-animate-css` e as transições do Tailwind | — |
| Gráfico | `recharts` | já instalado, ainda sem uso no código |
| Texto visível | `react-i18next` (`useTranslation`) | `src/i18n/locales/` |
| Teste | Vitest + Testing Library + `user-event` | `src/test/setup.ts` |

Detalhes que passam despercebidos:

- **Tailwind é v4, configurado em CSS.** Os tokens ficam em `@theme`, dentro de
  `src/index.css`. **Não existe `tailwind.config.js` — não crie um.**
- **A ordem das classes é automática**: o `prettier-plugin-tailwindcss` ordena.
- **Cor sai dos tokens do tema** (`bg-background`, `text-foreground`, `bg-primary`…),
  nunca hex no componente — nem no hover. A identidade visual muda num lugar só.

## Precisa de algo que não existe?

| Situação | O que fazer |
|---|---|
| Primitivo que o shadcn tem e o projeto ainda não instalou (`sheet`, `tabs`, `dropdown-menu`…) | `npx shadcn@latest add <nome>` — mesma registry, não é dependência nova |
| Variação visual de um componente existente | uma variante nova com `cva`, no próprio componente |
| Ícone que falta | procure no `lucide-react` |
| Pacote de UI fora dessa lista (outro kit, carrossel, `framer-motion`, date picker externo…) | **pare**: é dependência nova. Justifique no PR e combine com o time antes |

"Seria mais bonito" e "eu já usei em outro projeto" não justificam pacote novo.

## Responsividade

- **Mobile-first.** A base do `className` é a tela pequena; `sm:`, `md:`, `lg:` sobem a
  partir dela.
- **Breakpoints do Tailwind**, sem inventar: `sm` 640 · `md` 768 · `lg` 1024 · `xl` 1280.
- **Sem largura fixa em container**: `w-full` + `max-w-*`, `flex` ou `grid`.
- **Sem rolagem horizontal na página.** Conteúdo largo rola dentro do próprio
  container: `overflow-x-auto`.
- **Alvo de toque** de pelo menos ~40px de altura no celular. Os tamanhos padrão do
  shadcn já atendem; não reduza.
- **Confira em três larguras: 375, 768 e 1280.** O teste automatizado não cobre isso —
  o jsdom não faz layout. É verificação sua, no navegador, e entra no "Como verificar"
  do PR.

## Movimento

Movimento tem função — mostrar para onde a pessoa foi, confirmar um toque — e nunca é
enfeite.

| Regra | Por quê |
|---|---|
| O que se repete é curto (~300ms); o que aparece uma vez pode ir a 500–700ms | a décima troca de pergunta com 500ms já cansa |
| Avançar entra pela direita, voltar pela esquerda, tela nova entra de baixo | a pessoa sabe para onde foi sem ler nada |
| Só anima o que mudou | animar o que ficou igual é ruído |
| **Sempre `motion-reduce:animate-none`** | quem pediu menos movimento ao sistema não vê nenhum |

## Modais

| Tipo | Componente | Comportamento |
|---|---|---|
| Comum (informação, contato) | `Dialog` | fecha clicando fora ou com Esc |
| **Obrigatório** (a pessoa precisa escolher) | `AlertDialog` | nem clique fora nem Esc fecham; os botões dizem a ação ("Revisar dados"), não "Sim" e "Não" |

## Antes de dar o componente por pronto

- [ ] Ele já não existe? Procure antes de criar — componente duplicado é o erro mais comum.
- [ ] Mora no lugar certo: uma feature só → dentro dela; mais de uma → `src/components/`;
      primitivo → `src/components/ui/`, pelo CLI do shadcn.
- [ ] Nenhum texto escrito direto: tudo em `src/i18n/locales/`, em todos os idiomas.
- [ ] Nenhuma cor fora dos tokens do tema.
- [ ] Animação com `motion-reduce:animate-none`.
- [ ] Conferido em 375, 768 e 1280.
- [ ] Teste ao lado do componente.
- [ ] `npm run check` verde.
