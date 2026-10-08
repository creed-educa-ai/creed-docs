# Definition of Ready

O portão da **subtarefa publicada no ClickUp**: ela está pronta para alguém pegar e
começar hoje. ✅

## A régua

> **A subtarefa se basta.**

Ela é escrita para quem **não** participou da especificação e **não** usa o
`creed-ai-context`. Se ela serve a essa pessoa, serve a todo mundo. Anexos e links
podem aprofundar; nunca podem ser a única fonte de algo necessário para começar.

## O teste rápido

Lendo só a subtarefa e os anexos dela, responda:

1. **O que muda para quem usa o sistema?**
2. **Por onde eu começo — que arquivo eu abro primeiro?**
3. **Como eu sei que terminei?**

Precisou abrir outra coisa, ou perguntar a alguém? A subtarefa **não está pronta**.
Comente nela, marque quem publicou e espere o ajuste — ou combine com essa pessoa o que
falta. Não preencha a lacuna no palpite.

## O que uma subtarefa pronta tem

### Escopo
- [ ] Título que é um **resultado** ("Relatório de adesão por organização"), não uma
      atividade ("Criar service e repository").
- [ ] Uma frase dizendo o que a entrega faz, e o que muda para quem usa, sem jargão.
- [ ] O que **não** entra.
- [ ] Um repo só. Back e front são subtarefas separadas.

### Contexto
- [ ] Todo termo do domínio (vínculo, prisma, prognóstico) **com a definição** na
      primeira vez em que aparece — não "ver glossário".
- [ ] Decisão de produto tomada sem a cliente escrita **em texto**, não só o número da
      premissa: `P-012` sozinho não diz nada a quem não tem o ledger.
- [ ] Divergência conhecida entre documentação e código, avisada.

### Recorte técnico
- [ ] Arquivos a criar ou mudar, **com caminho**, e o papel de cada um.
- [ ] O que reusar, com caminho — nunca "aproveite o que já existe".
- [ ] Onde copiar a forma, como **link que abre** (permalink do GitHub).
- [ ] Endpoint novo ou alterado com **método, rota, entrada, saída e erros**. A
      subtarefa do front traz o mesmo contrato, visto de quem consome.

### Como saber que terminou
- [ ] Critérios de aceite verificáveis: dá para responder sim ou não olhando a tela, a
      resposta da API ou o teste.
- [ ] "Como verificar" com **comando copiável**, não prosa.
- [ ] Caso feliz e pelo menos um caso de borda, nomeados.

### Dependências
- [ ] De que outra subtarefa esta depende, pelo nome — e se ela já está concluída.
- [ ] Dependência fora do board (um acesso, um ambiente, uma decisão) declarada.

## Não é motivo para segurar a tarefa

**Falta de resposta da cliente.** A dúvida de produto vira [premissa](premissas.md)
escrita, e a tarefa começa. O que segura a tarefa é a premissa **não** escrita: aí a
escolha acontece na cabeça de quem implementa, sem ninguém saber que foi feita.
