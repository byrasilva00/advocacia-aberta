---
name: orquestrar
description: Coordena um trabalho jurídico de várias etapas (organizar, transcrever, diagnosticar, pesquisar, redigir, revisar, diagramar) dividindo-o em tarefas delimitadas, delegando cada uma a um subagente especialista que segue o protocolo correspondente, conferindo cada entrega e consolidando o resultado. Use quando o pedido envolver mais de uma etapa do método ou material grande demais para uma só leitura.
---

# Orquestrar — coordenar subagentes sobre um caso

Esta skill transforma um pedido amplo ("prepare a contestação deste caso", "monte a
fundamentação e revise a inicial") em um **plano de tarefas**, delega cada tarefa a um
**subagente especialista** e confere o que volta antes de seguir. O orquestrador não
substitui os protocolos: ele decide **quem faz o quê, em que ordem e com qual
material**, e garante que nada passe adiante sem conferência.

> Quem orquestra não redige, não pesquisa e não diagnostica. Planeja, entrega a cada
> especialista só o que ele precisa, confere a entrega contra a fonte e devolve ao
> advogado um resultado rastreável.

Se o seu agente não consegue criar subagentes, execute o mesmo plano em sequência,
você mesmo, uma tarefa por vez — e comece cada tarefa relendo apenas os arquivos que
ela indica. O método é o mesmo; muda só quem executa.

## Regra-raiz

Tudo o que vale para os protocolos vale para os subagentes, e o orquestrador é quem
responde por isso:

- **Nenhum fato, data, número ou citação sem fonte.** Entrega de subagente com
  afirmação sem fonte é devolvida, não aceita "por enquanto".
- **Fonte, síntese e conclusão separadas** (ver `GERENCIAR-CONTEXTO.md`).
- **Dado mínimo.** Cada subagente recebe só os arquivos da sua tarefa. Nunca misture
  casos diferentes no mesmo subagente.
- **Decisão do advogado fica com o advogado.** Subagente não conversa com o usuário;
  toda aprovação passa pelo orquestrador.

## Os especialistas

Cada especialista executa **um** protocolo e grava o resultado na pasta do caso.

| Especialista | Protocolo que segue | Entrada mínima | Saída |
|---|---|---|---|
| organizador | `organizar-caso` | `autos/` (ou um lote dela) | `analise/SUMARIO.md` |
| transcritor | `transcrever` | uma gravação por vez | `.srt` e `.md` ao lado da mídia |
| diagnosticador | `diagnosticar` | `SUMARIO.md` e documentos citados nele | `analise/DIAGNOSTICO.md` |
| pesquisador | `buscar-fontes` e `buscar-tjpr` | uma pergunta jurídica delimitada | `fundamentacao/LEGISLACAO.md`, `fundamentacao/JURISPRUDENCIA.md` |
| redator | `redigir-peca` | `SUMARIO`, `DIAGNOSTICO`, fundamentação e tipo de peça | plano da peça; depois, a peça em `pecas/` |
| revisor | `revisar-peca` | a peça e os arquivos do caso | `pecas/revisao-<peca>.md` |
| diagramador | `diagramar-peca` | peça já revisada e aprovada | PDF em `pecas/` |

`preparar-ambiente` e `criar-protocolo` **não são delegados**: instalar programas e
criar um protocolo exigem conversa e consentimento do usuário, então o orquestrador
conduz esses dois diretamente.

## Instrução

Considere `<caminho-do-caso>` o caminho informado pelo usuário (ex.: `casos/meu-caso`).
Se não tiver sido informado, peça-o antes de continuar. Todos os caminhos são relativos
à raiz do projeto.

### Passo 1 — Delimitar o pedido

Registre, antes de abrir qualquer documento:

- qual é o caso e qual é o resultado final esperado;
- quais etapas do método esse resultado exige;
- o que já existe na pasta do caso (`SUMARIO.md`, `DIAGNOSTICO.md`, fundamentação,
  peças anteriores) — liste a pasta, não presuma;
- prazo ou prioridade que o usuário tenha indicado.

Não refaça etapa cujo arquivo já existe e está atual. Se houver dúvida se está atual
(por exemplo, chegaram documentos novos depois do `SUMARIO.md`), pergunte.

### Passo 2 — Montar o plano de tarefas

Escreva o plano em `<caminho-do-caso>/analise/PLANO-DE-TRABALHO.md`:

```markdown
# Plano de trabalho — [pedido em uma linha]
Data: DD/MM/AAAA

| # | Tarefa | Especialista | Depende de | Entrada | Saída | Situação |
|---|---|---|---|---|---|---|
| 1 | Sumariar autos | organizador | — | autos/ | analise/SUMARIO.md | pendente |
| 2 | Transcrever audiência | transcritor | — | autos/audiencia.mp4 | autos/audiencia.md | pendente |
| 3 | Diagnosticar | diagnosticador | 1, 2 | SUMARIO.md | analise/DIAGNOSTICO.md | pendente |
| ... | | | | | | |

## Pontos de aprovação do usuário
- Depois da tarefa X: aprovar o plano da peça.
```

Ordem natural do método (adapte ao pedido, sem pular dependência):

```text
organizar ─┐
           ├─→ diagnosticar ─→ pesquisar ─→ redigir (plano) ─→ [aprovação] ─→ redigir (peça) ─→ revisar ─→ [aprovação] ─→ diagramar
transcrever┘
```

Apresente o plano ao usuário em poucas linhas e siga, salvo se ele pedir para ajustar.

### Passo 3 — Decidir o que roda em paralelo

Rode em paralelo apenas tarefas **independentes** — que não leem a saída uma da outra
e não gravam no mesmo arquivo:

- várias gravações → um transcritor por gravação;
- autos muito extensos → um organizador por lote (por volume ou por tipo de documento),
  cada um gravando um sumário parcial em `analise/sumarios-parciais/`; depois, um
  último organizador consolida no `SUMARIO.md`;
- perguntas jurídicas distintas → um pesquisador por pergunta, cada um gravando em
  `fundamentacao/pesquisas/<tema>.md`; o orquestrador consolida em `LEGISLACAO.md` e
  `JURISPRUDENCIA.md`.

Nunca rode em paralelo duas tarefas que gravem o mesmo arquivo, nem uma tarefa e outra
que dependa dela.

### Passo 4 — Escrever o briefing de cada subagente

O subagente começa sem memória da conversa. Tudo o que ele precisa vai no briefing, e
nada além disso:

```text
Tarefa: [uma frase — o que entregar]
Protocolo: siga .agents/skills/<pasta-do-protocolo>/SKILL.md, no modo repositório.
Caso: <caminho-do-caso>
Leia: [lista exata de arquivos ou pastas]
Grave em: [caminho exato do arquivo de saída]
Foco: [a pergunta ou o recorte — ex.: "só a questão da prescrição"]
Não faça: [o que fica fora — ex.: "não redija a peça", "não altere SUMARIO.md"]
Pare e devolva sem concluir se: faltar documento indispensável, a fonte não puder ser
confirmada ou um arquivo decisivo estiver ilegível — diga exatamente o que falta.
Ao terminar, devolva: o registro de cobertura (lido, não lido, fontes usadas, dúvidas
abertas) e o caminho do arquivo gravado.
```

Regras do briefing:

- Indique **o protocolo**, não o reescreva. O conhecimento jurídico mora no protocolo.
- Indique **arquivos**, não cole conteúdo de documentos do cliente no briefing.
- Uma tarefa por subagente. Se o briefing precisa de "e também", são duas tarefas.

### Passo 5 — Pontos de aprovação

Subagente não pede aprovação ao usuário. Quando um protocolo manda "pare e peça
aprovação", o orquestrador divide a tarefa em duas:

- **Redigir peça:** primeiro delegue só a Fase 1 do `redigir-peca` ("planeje e grave o
  plano em `pecas/PLANO-<peca>.md`; não redija"). Mostre o plano ao usuário. Só depois
  da aprovação delegue a Fase 2, com o plano aprovado como entrada.
- **Diagramar:** só depois que o usuário aceitar a peça revisada.
- **Qualquer envio de dados a serviço externo novo** ou instalação de programa: pergunte
  ao usuário antes de delegar.

### Passo 6 — Conferir cada entrega

Ao receber o retorno de um subagente, **antes** de liberar a próxima tarefa:

1. Abra o arquivo gravado. Retorno que não gravou o arquivo prometido não conta.
2. Confira por amostragem as afirmações decisivas contra a fonte: datas, valores,
   quem disse o quê, número de lei, súmula ou tema.
3. Abra os links de fonte citados. Link que não abre o documento é ausência de fonte.
4. Verifique se lacunas e arquivos ilegíveis foram declarados.

Se a entrega falhar, devolva ao mesmo especialista com o problema apontado
("o item 4 cita a Súmula X sem link que abra; confirme ou retire"). Depois de duas
devoluções sem solução, pare e leve a questão ao usuário.

Atualize a coluna **Situação** do `PLANO-DE-TRABALHO.md` a cada tarefa
(`pendente` → `em andamento` → `conferida` ou `bloqueada: motivo`).

### Passo 7 — Consolidar e entregar

Quando todas as tarefas estiverem conferidas:

1. Consolide o que precisar de consolidação (sumários parciais, pesquisas por tema).
2. Acrescente ao `PLANO-DE-TRABALHO.md` o registro de cobertura do trabalho todo:

```markdown
## Registro de cobertura
- Lido: ...
- Não lido / ilegível: ...
- Fontes usadas: ...
- Arquivos produzidos: ...
- Dúvidas abertas: ...
- Revisão humana necessária: ...
```

3. Informe o usuário em linguagem direta: o que ficou pronto (com os caminhos), o que
   ficou bloqueado e por quê, e qual decisão depende dele.

## Condições de parada

Pare a orquestração e leve ao usuário quando:

- um especialista devolver bloqueio por documento indispensável ausente ou ilegível;
- a mesma entrega falhar na conferência duas vezes;
- houver contradição material entre entregas de especialistas diferentes;
- o próximo passo exigir aprovação (plano da peça, diagramação, serviço externo);
- houver risco de misturar fatos de casos diferentes.

Parar a orquestração não descarta o que já foi conferido: registre no plano o que está
pronto e o que falta.

## Checklist de saída

- [ ] `PLANO-DE-TRABALHO.md` existe e reflete a situação real de cada tarefa.
- [ ] Cada subagente recebeu só os arquivos da sua tarefa.
- [ ] Toda entrega foi aberta e conferida antes de alimentar a seguinte.
- [ ] Nenhuma etapa com ponto de aprovação avançou sem o usuário.
- [ ] O registro de cobertura está no plano.
- [ ] A necessidade de revisão profissional está explícita.
