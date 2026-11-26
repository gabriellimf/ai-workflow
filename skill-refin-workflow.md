#: 1
Onde: Pasta de transcrições
Você roda: /demanda <tarefa>
O que a skill espera de você: As transcrições em <raiz>/<tarefa>/. As chaves ou links dos
  cards, ou o texto colado, ou "sem card". Na 1ª vez na empresa, ela pode perguntar onde
  ficam os cards.
O que ela entrega: .demanda.md, negocio.md, refinamentos/duvidas.md. Depois ela pede os
  repositórios.
Skills que ela chama:
────────────────────────────────────────
#: 2
Onde: Pasta de transcrições
Você roda: /demanda <tarefa> + "conectei"
O que a skill espera de você: Um terminal com agente em cada repositório, conectado ao seu
  terminal no Maestri. Ou "sem Maestri" com os caminhos.
O que ela entrega: Um AGENTS.md em cada repositório, sem versionar. As respostas do código,
  duvidas-refinamento-tecnico.md e duvidas-refinamento-negocio.md.
Skills que ela chama: explica-repositorio (pelos agentes do Maestri ou por subagentes)
────────────────────────────────────────
#: 3
Onde: Reunião
Você roda: Nenhuma skill
O que a skill espera de você: O time de desenvolvimento e o negócio respondem as dúvidas
O que ela entrega:
Skills que ela chama:
────────────────────────────────────────
#: 4
Onde: Pasta de transcrições
Você roda: /demanda <tarefa> + "o refinamento acabou"
O que a skill espera de você: As respostas anotadas nos arquivos de refinamentos/, ou a
  transcrição da reunião de refinamento na pasta da tarefa
O que ela entrega: spec/demanda.md: regras, critérios, abordagens, contratos, pendências.
  Depois ela pede a escolha da abordagem.
Skills que ela chama: Builder check (um subagente)
────────────────────────────────────────
#: 5
Onde: Pasta de transcrições
Você roda: /demanda <tarefa> + a sua decisão
O que a skill espera de você: A abordagem escolhida ("vamos de A2") e as respostas das
  pendências bloqueantes
O que ela entrega: Um plano por repositório que muda, em spec/planos/<repo>.md
Skills que ela chama: superpowers:writing-plans
────────────────────────────────────────
#: 6
Onde: Cada repositório
Você roda: Uma sessão nova no repositório: "executa <caminho>/spec/planos/<repo>.md"
O que a skill espera de você: O caminho do plano. Execute primeiro o repositório que produz
  um contrato e depois o que o consome. A ordem está na resposta do passo 5.
O que ela entrega: O código, com testes e revisão
Skills que ela chama: superpowers:subagent-driven-development (mais revisão) ou
  superpowers:executing-plans (mais barato). Durante a execução entram sozinhas
  test-driven-development, requesting-code-review, verification-before-completion e, no fim,
  finishing-a-development-branch, que pergunta se faz merge, PR ou mantém a branch.

Atalhos
- Se você já tem tudo, junte os passos numa mensagem só, por exemplo: "/demanda X, card Y, repositórios sem Maestri em ../repos". A skill só para quando falta algo.
- Se o refinamento foi feito sem a skill, comece no passo 4. Mande as respostas e os repositórios juntos.

Próximo passo no Orange: rode /demanda voucher-no-portal em ~/Orange/Transcriptions. Antes, conecte o TravelOS e o be-more-connected no Maestri.