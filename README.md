# promoavatar

**ðŸ‡§ðŸ‡· [PortuguÃªs](README.md) Â· ðŸ‡ºðŸ‡¸ [English](README.en.md) Â· ðŸ‡ªðŸ‡¸ [EspaÃ±ol](README.es.md)**

Repo de **domÃ­nio** do fluxo `/promoavatar` do inemaccbot: reels de divulgaÃ§Ã£o
para 12 pÃºblicos, com portÃ£o humano no meio.

> ## Descongelado em 2026-08-09
>
> Entre 2026-08-06 e 2026-08-09 este repo esteve **congelado** ("intocÃ¡vel"), e o
> trabalho todo ia para o [`promoavatar3`](https://github.com/inematds/promoavatar3).
> O dono liberou: **os dois voltam a evoluir**.
>
> Isso **nÃ£o** os torna um sÃ³. Eles continuam sistemas separados â€” cada um com seu
> motor de reel (`scripts/`), seus layouts (`templates/`), seus alvos e seu prompt.
> Mexer aqui nÃ£o afeta lÃ¡, e vice-versa. A diferenÃ§a de propÃ³sito continua: aqui Ã©
> **um vÃ­deo por pÃºblico** (12 pÃºblicos); lÃ¡ sÃ£o **trÃªs** (alcance, autoridade,
> promocional).
>
> **Uma coisa existe sÃ³ aqui:** a rota `| navega` â€” o agente pilotando o estÃºdio,
> que Ã© o caminho de volta caso o DOM do HeyGen mude e o script do `| estudio`
> quebre. O promoavatar3 nÃ£o tem esse plano B.
>
> **Custo da divergÃªncia dos trÃªs dias:** o que este repo recebeu no congelamento
> foi sÃ³ o mÃ­nimo para nÃ£o nascer quebrado fora desta mÃ¡quina â€” CTA versionado
> (`3e0da37`), caminhos como variÃ¡vel de ambiente (`4f10c84`) e o adaptador de
> imagem. O que **nÃ£o** veio junto foi tudo que o promoavatar3 ganhou de novo no
> perÃ­odo, e portar isso caso a caso Ã© decisÃ£o sua, nÃ£o automÃ¡tica.

## ðŸ“– Guia de uso

Guia completo (landing + passo a passo): **https://inematds.github.io/promoavatar/guia/**

O bot escreve os roteiros e PARA. VocÃª grava os avatares no HeyGen; quando
terminar, libera e ele baixa, monta os reels e entrega em cada canal.

Aqui nÃ£o hÃ¡ **TypeScript** â€” a definiÃ§Ã£o do pipeline (`flow.json`, prompts) Ã© o
coraÃ§Ã£o do repo, e um fluxo novo Ã© uma entrada no registry do bot mais um repo
como este. Mas hÃ¡ cÃ³digo Python em `scripts/` (8 arquivos): Ã© o motor do reel,
que roda como funÃ§Ã£o disparada pela fase `reel.montar`.

## O que tem aqui

| arquivo | o que Ã© |
|---|---|
| `flow.json` | as fases, os 12 pÃºblicos, o canal e o gatilho de cada um |
| `prompts/fase1-texto.md` | o que o agente recebe para escrever os roteiros |
| `templates/*.json` | os 4 layouts do reel 9:16 + o `mapa.json` formatoâ†’layout |
| `scripts/*.py` | os motores do reel (`preparar.py`, `montar-reel.py`) |
| `HELP.md` | a ajuda que aparece no chat em `/promoavatar help` |
| `textos/A<N>/` | os roteiros gerados, um arquivo por pÃºblico |
| `docs/pipeline.md` | **a tabela de tudo**: etapas, LLM/IA/worker, custo e tempo |
| `docs/canais-e-destinos.md` | para quem e onde fica cada reel |
| `docs/` | as demais decisÃµes e o que ainda estÃ¡ aberto |

## Para quem, e onde fica

Ã‰ a pergunta que mais gera dÃºvida, entÃ£o em resumo â€” o detalhe estÃ¡ em
[`docs/canais-e-destinos.md`](docs/canais-e-destinos.md):

| pergunta | quem responde | onde ajustar |
|---|---|---|
| **para quem** Ã© o reel | este repo | `flow.json`, campo `canal` do pÃºblico |
| **onde fica** o canal no disco | o bot | derivado: `~/projetos/yt-pub-<canal>/imports/videos` |

Exemplo: `mulheres` tem `"canal": "lives4"`, entÃ£o o reel dela Ã© entregue em
`~/projetos/yt-pub-lives4/imports/videos`.

**Essa entrega Ã© a Ãºltima etapa do pipeline** â€” ela acontece dentro da fase
`reel`, nÃ£o numa fase separada de "publicaÃ§Ã£o". NÃ£o existe fase `publicar` no
`flow.json`: quem diz o destino Ã© o `canal` de cada pÃºblico.

O caminho **nÃ£o estÃ¡ escrito em lugar nenhum** â€” Ã© sempre
`~/projetos/yt-pub-<canal>/imports/videos`. Por isso:

- **trocar o canal de um pÃºblico** = editar o `flow.json` (vale no prÃ³ximo fluxo);
- **criar um canal novo** = `mkdir -p ~/projetos/yt-pub-lives33/imports/videos`,
  sem tocar no bot.

## O ciclo, em uma tela

```
/promoavatar <assunto> [--alvo=mulheres]
        â”‚
        â–¼
  1. texto     UM roteiro por pÃºblico, gravado em textos/A<N>/<publico>.md
        â”‚
        â¸ï¸  PARA â€” o chat te manda cada roteiro com o TÃTULO do vÃ­deo
        â”‚      vocÃª grava no HeyGen com esse nome exato
        â”‚      (Ã© AQUI que se troca a capa: `capa: A#<N> <publico>` + a foto)
        â”‚      /aprovar A#<N>
        â–¼
  2.5 baixar   acha o vÃ­deo pelo tÃ­tulo e baixa (janela de 90 min)
        â”‚
        â¸ï¸  PARA â€” segundo portÃ£o: vocÃª confere os avatares baixados
        â”‚      antes de gastar fila de render
        â–¼
  3. reel      monta o reel 9:16 e ENTREGA no canal do pÃºblico
        â”‚
        â–¼
  âœ… link do vÃ­deo final no chat
```

## As fases e os portÃµes

SÃ£o **4 fases lÃ³gicas e 2 portÃµes**. Os portÃµes sÃ£o o `"pausa_apos": true` das
fases `texto` e `baixar` no `flow.json` â€” o pipeline para sozinho ali e sÃ³ anda
quando vocÃª libera.

| nÂº | fase | `id` no `flow.json` | fila | quem faz | portÃ£o depois? |
|---|---|---|---|---|---|
| 1 | texto | `texto` | `texto` | agente (opus) | âœ… `/aprovar A#N` |
| 2 | avatar | *5 rotas, abaixo* | â€” | **normalmente vocÃª** | â€” |
| 2.5 | baixar | `baixar` | `io` | funÃ§Ã£o `heygen.baixar` | âœ… vocÃª confere |
| 3 | reel | `reel` | `render` | funÃ§Ã£o `reel.montar` | fim (entrega no canal) |

A `baixar` Ã© meio-fase de propÃ³sito: nÃ£o cria nada, sÃ³ traz pro disco o que jÃ¡
existe no estÃºdio.

Aprovar antes vale mais que retentar depois â€” render de avatar e fila de reel
custam caro e nÃ£o se desfazem. Os dois portÃµes existem para isso: o primeiro
protege o gasto de avatar, o segundo protege o gasto de render.

### A fase 2 tem 5 rotas

SÃ³ uma roda por fluxo. As quatro automÃ¡ticas sÃ£o fases com a chave `opcional` no
`flow.json`; a quinta Ã© a ausÃªncia de todas elas.

| rota | `id` / flag `opcional` | quem gera o avatar |
|---|---|---|
| **manual** *(padrÃ£o do promoavatar)* | *nenhuma* | **vocÃª**, sem o bot |
| estÃºdio | `estudio` (`opcional: estudio`) | o bot abre, vocÃª conclui |
| API | `gerar` (`opcional: api`) | o bot, via API HeyGen |
| crÃ©ditos | `gerar-creditos` (`opcional: creditos`) | o bot, consumindo crÃ©ditos |
| navegador | `navega-avatar` (`opcional: navega`) | agente LLM clonando o `TEMPLATE-AVATAR` |

Duas dessas flags estÃ£o documentadas no chat: **`| api`** (ver a seÃ§Ã£o
"OS AVATARES: SUA MÃƒO OU A API" do `HELP.md`) e **`| estudio`**. As de
`creditos` e `navega` existem como fase no `flow.json`, mas **nÃ£o estÃ£o no
`HELP.md`** â€” o nome da flag no chat vem do campo `opcional`, entÃ£o confirme
antes de usar.

A rota `navega-avatar` Ã© a mais cara: **~17,8k tokens por pÃºblico**, ou ~214k no
fluxo de 12 (`docs/pipeline.md`).

**O que amarra as 5 rotas Ã© o tÃ­tulo.** Em qualquer uma delas o vÃ­deo tem que se
chamar `A<N>-<publico>-v1`. Na rota manual isso Ã© 100% responsabilidade sua â€” Ã©
o Ãºnico contrato entre vocÃª e o pipeline.

## Trocar a imagem de capa por uma sua

As imagens do reel sÃ£o decididas na fase de texto (seÃ§Ã£o `## IMAGENS`, regra
11b) e geradas pelo flux. Para pÃ´r uma arte **sua** no lugar, mande a **foto no
chat com esta legenda** â€” a legenda da imagem, nÃ£o uma mensagem separada:

| legenda | efeito |
|---|---|
| `capa: A#25 jovens` | IMAGEM 1 (a capa do feed) desse pÃºblico |
| `capa: A#25 *` | a mesma imagem em **todos** os pÃºblicos do fluxo |
| `capa: A#25 jovens 3` | troca a IMAGEM 3, nÃ£o a capa |
| `capa: A#25 jovens cover` | preenche **cortando** as laterais |

O bot escreve a linha `arquivo: <caminho>` na imagem certa do
`textos/A<N>/<publico>.md`, e o `preparar.py` passa a usar a sua foto em vez de
gerar uma.

**O padrÃ£o Ã© `contain`:** a imagem cabe INTEIRA e o resto da faixa Ã© preenchido
com uma cÃ³pia borrada dela mesma. Imagem enviada por vocÃª **nunca Ã© cortada sem
vocÃª pedir** â€” a gerada nasce no tamanho exato, entÃ£o cortar nÃ£o tira nada; a
sua jÃ¡ vem composta, e se traz texto ou um rosto enquadrado, cortar destrÃ³i o
trabalho. `cover` Ã© para imagem de fundo, sem texto.

### Quando mandar: no portÃ£o da fase de texto

Ã‰ a Ãºnica janela em que trocar a capa Ã© **de graÃ§a** â€” nenhum avatar gerado,
nenhuma imagem paga no flux, nenhum render. Depois que o reel monta, o comando
ainda escreve no roteiro, mas o vÃ­deo sÃ³ muda com `/refazer` â€” e o bot avisa
isso na resposta.

Sem foto anexada ele recusa em vez de gravar uma linha vazia. Se vocÃª jÃ¡ tem o
arquivo no disco, dÃ¡ para digitar o caminho:

```
capa: A#25 jovens | arquivo=/caminho/da/imagem.png
```

## O tÃ­tulo Ã© o contrato

No estÃºdio, o vÃ­deo precisa se chamar **exatamente**:

```
A<N>-<publico>-v1        ex.: A8-mulheres-v1
```

O download casa por igualdade exata de string. Nome diferente = vÃ­deo nunca
encontrado, e a fase expira em 90 minutos. O chat te manda o tÃ­tulo pronto junto
de cada roteiro, justamente para nÃ£o ser digitado de memÃ³ria.

## OpÃ§Ãµes na criaÃ§Ã£o

| opÃ§Ã£o | padrÃ£o | o que faz |
|---|---|---|
| `--alvo=jovens` (repetÃ­vel) ou `\| alvos=a,b` | todos os 12 | sÃ³ esses pÃºblicos |
| `\| legenda=nao` | **COM legenda** | desliga a legenda palavra a palavra do reel |
| `\| versao=N` | 1 | muda o `-vN` do tÃ­tulo do estÃºdio |
| `\| de=<fase>` | â€” | comeÃ§a no meio (vocÃª jÃ¡ fez texto e/ou avatar) |
| `\| sombra` | â€” | mostra o plano, nÃ£o enfileira nada |

```
/promoavatar <assunto> --alvo=jovens | legenda=nao
```

**O padrÃ£o inverteu (2026-08-07): legenda Ã© LIGADA.** Uma palavra por vez, caixa
alta, branca, com a palavra-chave em Ã¢mbar, na base da faixa do avatar. O
desenho, as decisÃµes e o lugar de mudar cor e formato estÃ£o em
`docs/legenda.md`.

> **AtenÃ§Ã£o â€” meia implantaÃ§Ã£o.** O **motor** jÃ¡ estÃ¡ pronto e verificado neste
> repo (`scripts/legendas.py`, camada no `montar.py`, nÃ³ no template,
> `--sem-legenda` no `preparar.py`/`montar-reel.py`). A **flag do bot ainda
> nÃ£o**: `| legenda=nao` sÃ³ passa a valer depois da mudanÃ§a em
> `inemaccbot/src/gateway/comandos-fluxo.ts` (`:282`, `:182-186`, `:336`) e do
> restart â€” que exige fila vazia. AtÃ© lÃ¡, quem roda pela linha de comando jÃ¡ sai
> com legenda; pelo bot, ainda nÃ£o.

### Legenda: o que vem da HeyGen e o que Ã© nosso

Essa opÃ§Ã£o controla **sÃ³ a legenda que o nosso editor desenha**. Ela nÃ£o vÃª o
MP4 que veio do HeyGen: se o avatar chegar com legenda **queimada nos pixels**,
ela sobrevive ao reel inteiro, mesmo com a opÃ§Ã£o desligada â€” nÃ£o existe etapa de
remoÃ§Ã£o, mÃ¡scara ou inpaint no pipeline.

**A legenda do avatar Ã© decidida no estÃºdio.** O que a API do HeyGen oferece
(`video_status.get`):

| campo | o que Ã© | usamos? |
|---|---|---|
| `video_url` | MP4 **sem** legenda queimada | sim, quando nÃ£o hÃ¡ versÃ£o legendada |
| `video_url_caption` | MP4 **com** legenda queimada | **sim, quando vem preenchido** |
| `caption_url` | legenda solta (arquivo), quando existe | nÃ£o |

O download do bot prefere o `video_url_caption` e cai no `video_url` quando ele
nÃ£o vem (`escolherUrl`, `inemaccbot/src/fila/tarefas/heygen.ts`). Ou seja:
gravou com legenda no estÃºdio, o reel sai com ela; gravou sem, sai sem.

**NÃ£o dÃ¡ para escolher nada na hora de baixar.** O download Ã© um `GET` numa URL
pronta â€” nÃ£o hÃ¡ `?estilo=`, `?formato=`, `?idioma=`. Cinco endpoints de legenda
foram sondados (`v1/video.caption`, `v2/video/caption`, `v1/video.subtitle`,
`v1/caption.list`, `v2/caption_styles`) e deram **404**. Estilo, fonte e posiÃ§Ã£o
da legenda queimada se decidem **no estÃºdio, antes de renderizar**; depois disso
estÃ£o nos pixels, e sÃ³ regravando.

**O que foi medido (2026-08-01, chave real da conta):** os 25 vÃ­deos completos
mais recentes â€” `video_url_caption` nulo e `caption_url` vazio em **todos**.

**O que foi medido (2026-08-07, no `A35-tecnicos-v1`, `901cc529â€¦`):** o vÃ­deo
**tem** legenda quando baixado pela UI, e mesmo assim:

- `GET /v3/videos/{id}` responde **200** (a linha anterior dizia 404 â€” era o
  legado `v2/video/{id}`, que segue 404). A resposta inteira Ã© `id`, `title`,
  `status`, `duration`, `created_at`, `completed_at`, `video_url`,
  `thumbnail_url`, `gif_url`, `video_page_url`. **NÃ£o existe
  `captioned_video_url` nem `subtitle_url`** â€” os campos que a doc pÃºblica
  descreve nÃ£o vieram;
- `v1/video_status.get` no mesmo vÃ­deo: `caption_url` vazio, `video_url_caption`
  nulo.

Ou seja: **legenda ligada no estÃºdio nÃ£o chega Ã  API**, nem como MP4 legendado
nem como arquivo. O MP4 com legenda que a UI baixa Ã© um render sob demanda,
inalcanÃ§Ã¡vel por API. Isso fecha a hipÃ³tese anterior de que `video_url_caption`
passaria a vir preenchido.

**O que continua NÃƒO medido:** o comportamento de um vÃ­deo criado pelo
`POST /v3/videos` **com `caption`** â€” esse Ã© outro caminho, e pode muito bem
devolver os campos da doc. Irrelevante aqui enquanto a fase 2 for humana: o bot
nunca chama o create.

**ConsequÃªncia para o pipeline:** legenda de vÃ­deo jÃ¡ renderizado sÃ³ sai do
arquivo local (ASR). Ver `docs/legenda.md`.

ConsequÃªncias prÃ¡ticas de gravar com legenda queimada:

- ela vem posicionada para o enquadramento 16:9, nÃ£o para a faixa do meio do
  9:16 â€” pode ser cortada ou colidir com a base;
- com a opÃ§Ã£o `legenda` ligada, saem **duas** legendas. **Ligar uma Ã© decidir
  desligar a outra**: legenda no estÃºdio â†’ reel sem `| legenda`; reel com
  `| legenda` â†’ estÃºdio sem legenda.

## Onde mudar o quÃª

A regra: **o que Ã© decisÃ£o de pÃºblico ou de campanha mora neste repo; o que Ã©
identidade visual da marca mora na skill.** A skill Ã© global â€” mexer nela muda
TODO reel, inclusive os disparados direto no chat.

| quero mudarâ€¦ | arquivo | camada |
|---|---|---|
| o canal de um pÃºblico | `flow.json` â†’ `alvos.<publico>.canal` | domÃ­nio |
| o gancho de um pÃºblico | `flow.json` â†’ `alvos.<publico>.gatilho` | domÃ­nio |
| **como os roteiros sÃ£o escritos** | `prompts/fase1-texto.md` | domÃ­nio |
| **o que este fluxo pede ao reel** | `prompts/reel-regras.md` + `templates/*.json` | domÃ­nio |
| **o clipe de CTA do fim** | `cta/cta-9x16.mp4` â€” troque o arquivo | domÃ­nio |
| a ajuda do chat | `HELP.md` | domÃ­nio |
| **como o reel Ã© MONTADO** (cores, fontes, posiÃ§Ãµes, SFX, modos) | `~/.claude/skills/reel-edita-inema/SKILL.md` | skill (global) |
| o que o agente recebe antes de chamar a skill | `inemaccbot/prompts/reel.md` | bot |
| filas, timeouts, modelo e esforÃ§o | `inemaccbot/config/skills.json` | bot |

### O prompt do texto (`prompts/fase1-texto.md`)

Ã‰ onde vivem, nesta ordem:

1. **CONTEXTO FIXO** â€” o que o agente jÃ¡ sabe (Nei e Tiza sÃ£o os gestores da
   comunidade), para nÃ£o citar nome sem funÃ§Ã£o nem inventar quem sÃ£o.
2. **NÃƒO MEXA NA MÃQUINA** â€” proibiÃ§Ã£o de instalar qualquer coisa. Um render
   instalou um binÃ¡rio errado seguindo dica de log e derrubou o render seguinte.
3. **PASSO ZERO** â€” tese central, elemento demonstrÃ¡vel e a escolha explÃ­cita de
   um formato entre 11. Sem escolher, o agente cai sempre em dorâ†’soluÃ§Ã£oâ†’CTA,
   que Ã© molde de anÃºncio, e anÃºncio nÃ£o Ã© compartilhado.
4. **OFICINA DE GANCHO** â€” cinco primeiras frases por pÃºblico, quatro
   descartadas POR ESCRITO. O critÃ©rio Ã© o teste da lacuna: depois da frase, a
   pessoa precisa da prÃ³xima para fechar o sentido? Se a frase se basta, Ã©
   afirmaÃ§Ã£o, nÃ£o gancho. Teto de 9 palavras.
5. **REGRAS DE ESCRITA** â€” as 16 regras: gancho nos 2 primeiros segundos, dor
   antes da soluÃ§Ã£o, nomear a profissÃ£o, benefÃ­cio antes da mecÃ¢nica, frases
   curtas, promessa do tamanho certo, CTA imperativo, nada de placeholder, nada
   de urgÃªncia inventada, as SOBREPOSIÃ‡Ã•ES como roteiro VISUAL com os quatro
   gatilhos (atenÃ§Ã£o Â· retenÃ§Ã£o Â· engajamento Â· CTA), a lacuna vivendo na FALA,
   valor completo antes da marca, a Ãºltima frase decidindo o compartilhamento, e
   escrever para UMA pessoa concreta.
6. **O contrato de saÃ­da** â€” `{{pasta}}`, `RESULT:`/`ERRO:`.

VariÃ¡veis que o bot injeta: `{{input}}` (o assunto), `{{publicos}}` (os alvos
REAIS do fluxo), `{{pasta}}` (onde gravar, absoluto), `{{ref}}`, `{{saida}}`.

#### Assunto que Ã© DEBATE: o prompt crava uma posiÃ§Ã£o

Assunto que chega como pergunta em aberto ("isso Ã© bom ou ruim?", "o que vocÃª
acha?") tinha um resultado previsÃ­vel: o agente explicava os dois lados e
fechava em "o importante Ã© se preparar". Correto e morno â€” ninguÃ©m comenta com
equilibrista, e o vÃ­deo Ã© visto e esquecido.

A causa nÃ£o era falta de talento: sÃ£o as regras 9 e 10 (nÃ£o invente dado, nÃ£o
invente urgÃªncia) fazendo o agente recuar atÃ© o meio-termo, que Ã© o Ãºnico lugar
onde ele tem certeza de nÃ£o estar afirmando nada.

EntÃ£o o prompt agora manda **tomar um lado** nesse caso, e **escrever no resumo
qual posiÃ§Ã£o cravou e por quÃª**. Isso nÃ£o afrouxa as regras 9 e 10: opiniÃ£o Ã©
permitida, fato inventado nÃ£o.

**A posiÃ§Ã£o que vocÃª mandar vence a dele.** Se vocÃª escreve a sua no assunto, ele
usa a sua; o bloco sÃ³ existe para quando vocÃª nÃ£o escreveu. Escrever a sua
continua sendo o melhor caminho â€” junto com um fato concreto (para a linha PROVA
nÃ£o ficar vazia) e a pergunta que vocÃª quer nos comentÃ¡rios.

Como o resumo diz a posiÃ§Ã£o escolhida, vocÃª discorda dela **no portÃ£o**, antes de
gerar avatar nenhum â€” `/refazer` custa um texto, nÃ£o um render.

### O estilo do reel â€” duas camadas

O que ESTE pipeline pede ao reel mora em `prompts/reel-regras.md` e nos
`templates/*.json`: os quatro gatilhos, a headline a partir do `{gatilho}` do
pÃºblico, e as faixas de cada layout.

A skill `reel-edita-inema` Ã© quem sabe montar: composiÃ§Ã£o empilhada 9:16, cores,
fontes, corte de silÃªncio, legenda palavra-a-palavra, SFX. Mudar ali muda a
marca inteira.

**Melhorar o reel, na ordem do mais barato:** trocar o clipe de `cta/`; ajustar
`prompts/reel-regras.md` ou o `templates/*.json`; e sÃ³ entÃ£o mexer na skill.

## O que NÃƒO Ã© deste repo (Ã© do inemaccbot)

Este repo Ã© **domÃ­nio**: ele *declara* o pipeline. Quem **executa** Ã© o
`inemaccbot`. Procurar aqui algo que Ã© de lÃ¡ Ã© a perda de tempo mais comum, entÃ£o
segue a lista do que **nÃ£o** estÃ¡ neste repo:

| o quÃª | onde vive de verdade |
|---|---|
| os comandos do chat (`/promoavatar`, `/status`, `/aprovar`, `/refazer`, `/cancelar`) e o parser de `\|` e `--` | o bot. Aqui sÃ³ existe o `HELP.md`, que Ã© o **texto** da ajuda, nÃ£o o cÃ³digo dela |
| o motor de fases: filas, tentativas, timeouts, o congelamento na criaÃ§Ã£o, o prÃ³prio conceito de portÃ£o | `inemaccbot/config/skills.json`. O `flow.json` sÃ³ declara; quem obedece Ã© o bot |
| as tarefas `heygen.gerar`, `heygen.estudio`, `heygen.baixar` â€” e o `escolherUrl`, que decide entre o MP4 legendado e o limpo | `inemaccbot/src/fila/tarefas/heygen.ts` |
| o estado dos fluxos e os avatares baixados (`state/artefatos/fluxos/A<N>/`) | o repo do bot |
| as pastas de canal `~/projetos/yt-pub-<canal>/imports/videos` | regra derivada pelo bot; aqui sÃ³ existe o **nome** do canal |
| **como o reel Ã© MONTADO** (cores, fontes, posiÃ§Ãµes, corte de silÃªncio, SFX) | a skill global `~/.claude/skills/reel-edita-inema/SKILL.md` â€” mexer ali muda TODO reel da marca |
| o prompt que o agente recebe antes de chamar a skill do reel | `inemaccbot/prompts/reel.md` |

**Regra de bolso:** se a mudanÃ§a afeta *todos* os fluxos, nÃ£o Ã© daqui. Se afeta
sÃ³ o promoavatar, Ã© daqui.

A exceÃ§Ã£o sÃ£o os `scripts/*.py` â€” eles moram aqui e sÃ£o o motor do reel deste
domÃ­nio, chamÃ¡veis direto na mÃ£o (ver "ParÃ¢metros").

## Onde defino o prompt, e onde defino os pÃºblicos

SÃ£o dois arquivos diferentes, e a confusÃ£o entre eles Ã© comum:

| quero mudarâ€¦ | arquivo |
|---|---|
| **como se escreve** (tom, regras, ganchos, imagens) | `prompts/fase1-texto.md` |
| **a dor / o Ã¢ngulo de um pÃºblico** | `flow.json` â†’ `alvos.<publico>.gatilho` |
| **para qual canal vai** | `flow.json` â†’ `alvos.<publico>.canal` |
| **o formato do arquivo de saÃ­da** (FALA / SOBREPOSIÃ‡Ã•ES / IMAGENS / ESTRUTURA) | a skill `inemaclub-textos` |

O `prompts/fase1-texto.md` manda usar a skill `inemaclub-textos` (linha 1), mas
**sobrescreve** a fÃ³rmula dela: "REGRAS DE ESCRITA (valem acima da fÃ³rmula
padrÃ£o da skill)". Ou seja, a skill dÃ¡ a estrutura; o prompt do fluxo dÃ¡ as
regras.

Os 12 pÃºblicos sÃ£o as chaves de `alvos` no `flow.json`:

```
pessoacomum Â· jovens Â· profissionais Â· mulheres Â· empreendedores Â· tecnicos
40mais Â· 60mais Â· educadores Â· criadores Â· recolocacao Â· familia
```

Adicionar um pÃºblico = mais uma entrada com `canal` e `gatilho`. A chave vira o
`<publico>` do tÃ­tulo `A<N>-<publico>-v1`, entÃ£o **nada de acento, espaÃ§o ou
hÃ­fen**.

## Como alterar: prompts, templates e alvos

As trÃªs coisas que vocÃª mais vai querer mexer. Vale para todas a mesma trava:
**tudo Ã© congelado quando o fluxo nasce** â€” editar vale para os PRÃ“XIMOS, e nem
`/refazer` pega a mudanÃ§a. Um fluxo em andamento nÃ£o muda de regra no meio.

### 1. Alterar o PROMPT (como os roteiros sÃ£o escritos)

Arquivo: **`prompts/fase1-texto.md`**. Ã‰ o documento inteiro que a fase 1
entrega ao agente. As partes, e o que acontece se vocÃª mexer em cada uma:

| bloco | mexa aqui paraâ€¦ | cuidado |
|---|---|---|
| CONTEXTO FIXO | mudar quem o agente "jÃ¡ conhece" (hoje: Nei e Tiza como gestores) | nome sem funÃ§Ã£o vira enfeite â€” o bloco existe para evitar isso |
| PASSO ZERO | mudar a lista dos 11 formatos, ou o que Ã© tese/prova | **os formatos sÃ£o chaves do `templates/mapa.json`** â€” mudou o nome aqui, mude lÃ¡ |
| OFICINA DE GANCHO | mudar o critÃ©rio do gancho (hoje: teste da lacuna, teto de 9 palavras) | Ã© o bloco que mais move alcance |
| REGRAS DE ESCRITA (as 16) | mudar tom, CTA, o que Ã© proibido | a 11b define o formato da seÃ§Ã£o `## IMAGENS` que o reel LÃŠ |
| contrato de saÃ­da | mudar onde grava, o `RESULT:`/`ERRO:` | quebrar isso quebra a fase inteira |

Cinco variÃ¡veis sÃ£o injetadas pelo bot e **nÃ£o devem sumir**: `{{input}}` (o
assunto), `{{publicos}}` (os alvos reais do fluxo), `{{pasta}}` (onde gravar,
absoluto), `{{ref}}` e `{{saida}}`.

Regra de convivÃªncia com a skill: `inemaclub-textos` dÃ¡ a **estrutura** do
arquivo (FALA / SOBREPOSIÃ‡Ã•ES / IMAGENS / ESTRUTURA); este prompt dÃ¡ as
**regras**, e sobrescreve a skill onde discordarem. Mudou a estrutura do arquivo?
Ã‰ na skill, e vale para todo mundo â€” nÃ£o sÃ³ para o promoavatar.

Os outros dois prompts do repo seguem a mesma lÃ³gica:
`prompts/fase-navega-avatar.md` (a rota navegador da fase 2) e
`prompts/reel-regras.md` (o que este fluxo pede ao reel).

### 2. Alterar os TEMPLATES (o layout do reel)

Dois arquivos diferentes, e a confusÃ£o entre eles Ã© comum:

**a) mudar como um layout se parece** â†’ edite o `templates/<nome>.json`.
O esquema Ã© sempre o mesmo:

```jsonc
{
  "nome": "empilhado-capa",
  "descricao": "â€¦",                       // texto livre, ajuda quem escolhe
  "canvas": { "largura": 1080, "altura": 1920 },
  "cores":  { "fundo": "#0E1116", "texto": "#FFFFFF", "acento": "#F5A623" },
  "faixas": {
    "topo": { "y": 0,    "altura": 704, "fonte": "imagens",
              "escurecer": 0.62,
              "headline": { "tamanho": 76, "peso": 900, "entrelinha": 1.04,
                            "margem_lateral": 48, "base_em": 56,
                            "maiusculas": true } },
    "meio": { "y": 704,  "altura": 608, "fonte": "avatar", "audio": true },
    "base": { "y": 1312, "altura": 608, "fonte": "texto", "painel": true,
              "hook": { "tamanho": 56, "peso": 800, "entrelinha": 1.16,
                        "margem_lateral": 56 } }
  },
  "transicao": { "flash": true, "duracao": 0.42,
                 "escala_entrada": 1.08, "pulso_max_s": 3.6 }
}
```

TrÃªs regras ao mexer:

- **`y` + `altura` das faixas tÃªm que fechar 1920.** Elas nÃ£o se empilham
  sozinhas â€” `y` Ã© posiÃ§Ã£o absoluta. Faixa faltando = faixa preta.
- **`fonte` Ã© o que alimenta a faixa**: `imagens` Â· `avatar` Â· `texto` (o `hook`)
  Â· `explicativo` (clipe mudo em loop). Mudar a `fonte` muda o contrato com a
  fase de texto.
- **`escurecer`** Ã© o vÃ©u sobre a imagem para a headline ficar legÃ­vel. Baixar
  demais e o texto some no claro da foto.

Criar um layout novo = mais um `.json` em `templates/`, com `"nome"` igual ao
nome do arquivo. Ele passa a existir para `--template` na hora; para entrar no
automÃ¡tico, precisa do passo (b).

**b) mudar qual formato cai em qual layout** â†’ edite o `templates/mapa.json`.
As chaves sÃ£o os formatos do PASSO ZERO, **com e sem acento** (o `preparar.py`
casa pelo texto que a fase de texto escreveu, entÃ£o as duas grafias existem de
propÃ³sito):

```json
"mito versus realidade": "diptico",
"comparaÃ§Ã£o": "diptico",
"comparacao": "diptico",
```

Formato que nÃ£o estiver no mapa **cai no padrÃ£o da raiz do `flow.json`** â€” nÃ£o
inventa layout. EntÃ£o, ao criar um formato novo no prompt, mapeie-o aqui nas
duas grafias, senÃ£o ele nunca vai usar o layout que vocÃª quis.

**c) fixar o layout de um pÃºblico**, ignorando o mapa â†’ campo `template` dentro
do alvo, no `flow.json` (exemplo na seÃ§Ã£o de parÃ¢metros). Vence o mapa, perde
para `--template`.

### 3. Alterar os ALVOS (os pÃºblicos)

Arquivo: **`flow.json`**, chave `alvos`. Cada entrada tem duas chaves
obrigatÃ³rias e uma opcional:

```json
"empreendedores": {
  "canal": "lives24",
  "gatilho": "Transforme IA em reduÃ§Ã£o de custos, vendas e novos negÃ³cios.",
  "template": "diptico"
}
```

| o que quero | onde |
|---|---|
| mudar a **dor/Ã¢ngulo** de um pÃºblico | `gatilho` â€” Ã© o que a regra 2 do prompt manda usar |
| mudar **para onde vai** o reel | `canal` â€” vira `~/projetos/yt-pub-<canal>/imports/videos` |
| fixar o **layout** daquele pÃºblico | `template` (opcional) |
| **adicionar** um pÃºblico | mais uma entrada; a chave Ã© o slug |
| **remover** um pÃºblico | apague a entrada |
| rodar **sÃ³ alguns** sem mexer em nada | `--alvo=jovens` ou `\| alvos=a,b` na criaÃ§Ã£o |

**A chave Ã© um contrato, nÃ£o um rÃ³tulo.** Ela vira:
o nome do arquivo `textos/A<N>/<publico>.md` Â· o tÃ­tulo do vÃ­deo no estÃºdio
`A<N>-<publico>-v1` Â· o `--alvo` do reel Â· o `seed-key` das imagens. Por isso:
**minÃºsculas, sem acento, sem espaÃ§o e sem hÃ­fen** (foi por isso que
`pessoa-comum` virou `pessoacomum`).

### 4. Alterar o DESTINO (onde o reel Ã© entregue)

O destino nÃ£o Ã© um caminho escrito em lugar nenhum â€” ele Ã© **derivado do `canal`
do pÃºblico**, sempre pela mesma regra:

```
<canal>  â†’  ~/projetos/yt-pub-<canal>/imports/videos
```

A separaÃ§Ã£o Ã© proposital: o domÃ­nio (este repo) diz sÃ³ o **nome** do canal; a
traduÃ§Ã£o para pasta Ã© do bot (`src/dominio/destinos.ts`), num lugar sÃ³. Se o
`flow.json` guardasse o caminho completo, viraria uma segunda cÃ³pia da lista de
canais â€” e cÃ³pias divergem.

| queroâ€¦ | como |
|---|---|
| **trocar o canal** de um pÃºblico | edite `alvos.<publico>.canal` no `flow.json` |
| **criar um canal novo** | `mkdir -p ~/projetos/yt-pub-lives33/imports/videos` â€” sÃ³ isso, o bot nÃ£o precisa saber |
| **dois pÃºblicos no mesmo canal** | ponha o mesmo `canal` nos dois; nada impede |
| **mudar a REGRA** (a pasta base, o `imports/videos`) | nÃ£o Ã© aqui â€” Ã© o `destinos.ts` do bot, e muda TODOS os fluxos |
| **entregar num reel avulso**, fora do fluxo | rode `scripts/montar-reel.py --saida <caminho>` na mÃ£o |

Mapa atual dos 12 (de `docs/canais-e-destinos.md`, remapeado em 2026-07-31):

```
empreendedores lives24  pessoacomum lives2   recolocacao lives3   mulheres  lives4
tecnicos       lives6   40mais      lives7   60mais      lives8   educadores lives9
criadores      lives11  jovens      lives22  profissionais lives23  familia  lives31
```

Como tudo aqui, **vale no PRÃ“XIMO fluxo**: um fluxo em andamento nÃ£o muda de
destino no meio do caminho.

## Os templates do reel

Ficam em `templates/` (apontado por `"templates_dir": "templates"` no
`flow.json`). SÃ£o 4 layouts, todos 1080Ã—1920, fundo `#0E1116` e acento Ã¢mbar
`#F5A623`:

| template | topo | meio | base | para que serve |
|---|---|---|---|---|
| **`empilhado-capa`** *(padrÃ£o da raiz)* | imagem 704px + `headline` | avatar 608px (Ã¡udio) | painel de texto 608px (`hook`) | capa de impacto â€” o formato original |
| **`empilhado-explicativo`** | imagem 704px + `headline` | avatar 608px (Ã¡udio) | vÃ­deo explicativo 608px, mudo em loop | quando existe explicativo em vÃ­deo |
| **`diptico`** | imagem 960px + `headline` | avatar 960px | **nÃ£o tem** | mitoÃ—realidade e comparaÃ§Ã£o â€” a imagem carrega o contraste |
| **`imagem-plena`** | imagem ocupa os 1920px | avatar em recorte no **topo-direita** | â€” | pergunta incÃ´moda, previsÃ£o, consequÃªncia inesperada |

No `imagem-plena` o avatar vai no topo-direita por regra de produÃ§Ã£o, nÃ£o por
estÃ©tica: **o rodapÃ© Ã© proibido** â€” em produÃ§Ã£o a interface da rede (legenda, @,
botÃµes) cobre o canto inferior e o avatar simplesmente nÃ£o apareceria.

Cada faixa declara uma `fonte`: `imagens`, `avatar`, `texto` ou `explicativo`. Ã‰
daÃ­ que vem a exigÃªncia da regra 11b do prompt de texto â€” a `headline` vai na
faixa de topo, o `hook` vai no painel de base. **Layout com base e `hook`
faltando = base preta**: foi o que aconteceu no A#23 (`hook` em 0 de 8 imagens).
Por isso o prompt manda escrever `hook` sempre, mesmo nos layouts que nÃ£o tÃªm
base (`diptico` e `imagem-plena`).

### NinguÃ©m escolhe layout na hora do render

O layout decorre da linha `Formato escolhido:` que a fase de texto grava em cada
`<publico>.md` (o PASSO ZERO do prompt) â€” ou seja, de uma decisÃ£o editorial que
**vocÃª jÃ¡ aprovou no portÃ£o**. O `templates/mapa.json` faz a traduÃ§Ã£o:

```
"mito versus realidade" â†’ diptico        "pergunta incÃ´moda" â†’ imagem-plena
"comparaÃ§Ã£o"            â†’ diptico        ...
```

**PrecedÃªncia** (resolvida pelo `preparar.py`):

```
--template explÃ­cito  â€º  template do ALVO no flow.json  â€º  mapa.json  â€º  template da raiz
```

Formato que nÃ£o estiver no mapa cai no padrÃ£o â€” nÃ£o inventa. No A#19 real a fase
de texto escolheu **9 formatos diferentes para 12 pÃºblicos**, entÃ£o isso produz
variaÃ§Ã£o de verdade sem ninguÃ©m decidir nada em tempo de render.

## ParÃ¢metros

### No chat (o que o `HELP.md` documenta)

| opÃ§Ã£o | efeito |
|---|---|
| `--alvo=jovens` (repetÃ­vel) ou `\| alvos=a,b` | sÃ³ esses pÃºblicos |
| `\| sombra` | mostra o plano, nÃ£o enfileira |
| `\| legenda` | legenda palavra-a-palavra (padrÃ£o: sem) |
| `\| versao=N` | muda o `-vN` do tÃ­tulo |
| `\| de=baixar` | comeÃ§a no meio (texto e avatar jÃ¡ feitos) |
| `\| api` | o BOT gera o avatar (~US$ 1/min da carteira prÃ©-paga) |
| `\| api \| sem-portao` | gera E nÃ£o para para aprovar |
| `/status A#N` Â· `/aprovar A#N` Â· `/refazer A#N <publico>` Â· `/cancelar A#N` | acompanhar |

### Nos motores do reel (`scripts/`)

`montar-reel.py` â€” a fase 3 ponta a ponta:

| flag | |
|---|---|
| `--avatar` | **obrigatÃ³rio** â€” o MP4 do HeyGen |
| `--ws` | **obrigatÃ³rio** â€” workspace do reel |
| `--alvo` | pÃºblico; vira o `seed-key` das imagens (default `reel`) |
| `--textos` | o `<publico>.md` â€” Ã© de onde sai a seÃ§Ã£o `## IMAGENS` |
| `--template` | override de layout (vence tudo) |
| `--flow` / `--mapa` | de onde resolver template e mapa |
| `--qualidade` | `high` (default) Â· `standard` Â· `draft` |
| `--cta` / `--sem-cta` | o clipe de fecho (`cta/cta-9x16.mp4`) |
| `--pular-preparo` | reaproveita o preparo jÃ¡ feito no `--ws` |
| `--saida` | destino do MP4 |

`preparar.py` â€” sÃ³ a preparaÃ§Ã£o (imagens + transcriÃ§Ã£o). Tem as mesmas flags,
mais `--explicativo`, `--sem-imagens`, `--sem-transcricao` e `--sem-montar`.

### Exemplos

```bash
# reel padrÃ£o de um pÃºblico â€” layout sai do mapa
python3 scripts/montar-reel.py \
  --avatar state/artefatos/fluxos/A34/A34-jovens-v1.mp4 \
  --ws /tmp/ws-A34-jovens --alvo jovens \
  --textos textos/A34/jovens.md --flow flow.json

# rascunho barato, sÃ³ para conferir enquadramento
python3 scripts/montar-reel.py ... --qualidade draft --sem-cta

# forÃ§ar um layout, ignorando o mapa
python3 scripts/montar-reel.py ... --template imagem-plena

# trocar template/CTA sem regerar as imagens
python3 scripts/montar-reel.py ... --pular-preparo --saida saida/A34-jovens.mp4

# com vÃ­deo explicativo na faixa de base
python3 scripts/preparar.py ... --alvo tecnicos \
  --explicativo saida/explicativo-tecnicos.mp4 --template empilhado-explicativo

# sÃ³ preparar agora, montar depois
python3 scripts/preparar.py ... --sem-montar
```

Fixar o layout de um pÃºblico direto no `flow.json` (vence o mapa, perde para
`--template`):

```json
"empreendedores": {
  "canal": "lives24",
  "gatilho": "Transforme IA em reduÃ§Ã£o de custos...",
  "template": "diptico"
}
```

## AtenÃ§Ã£o: tudo aqui Ã© congelado na criaÃ§Ã£o

O `flow.json`, os prompts e as opÃ§Ãµes (`legenda`, `cta`) sÃ£o **congelados quando
o fluxo nasce**. Editar vale para os PRÃ“XIMOS â€” um fluxo em andamento nÃ£o muda
de regra no meio do caminho, e por isso nem `/refazer` pega a mudanÃ§a.

---

Projeto adaptado da comunidade INEMA TDS - Marca Arckhe