# Maggie Mac — Brow Atelier (v2)

Landing page única e autocontida: `index.html` (HTML + CSS + JS, sem build) + `assets/`.

Projeto **independente** do `maggie-mac-beauty` antigo — nenhum asset, cor, fonte ou estrutura reaproveitada.

## Rodar

```bash
npx --yes serve -l 8943 /Users/mac/dev/maggie-mac-beauty-v2
```

## Direção — "Champagne e espresso"

Luxo por **restrição**: muito ar, fio de 1px, entreletra aberta em caixa alta pequena, movimento lento, nada de preto ou branco puro.

| | |
|---|---|
| Champagne | `#EFE9E0` |
| Champagne fundo | `#E6DED2` |
| Espresso | `#2A211B` (nunca preto puro) |
| Bronze | `#9A7B4F` (só em detalhe e itálico) |
| Suave | `#7A6E62` |
| Display | Marcellus |
| Corpo | Jost 200/300 |

Detalhes que sustentam o luxo: durações escritas por extenso ("Forty-five minutes"), numerais romanos nas etapas, easing de 1,1s (`cubic-bezier(.16,1,.3,1)`), carrossel a 40s em vez de 32s, fotos com `saturate(.9)` em vez de filtro forte.

No hero a moldura usa `aspect-ratio:16/9` no desktop — mesma proporção do arquivo — então a foto aparece inteira e o fundo dela funde com o champagne da página. No mobile vira `4/5`, que aproxima a sobrancelha.

## Hero — adaptação do "Horizon Estates"

O hero é a adaptação de um quinto prompt (uma landing de imobiliária de luxo). O mecanismo foi mantido inteiro; só o conteúdo virou sobrancelha.

**Mantido do prompt, valor por valor:**
- `height:200dvh` externo + `sticky top-0 height:100dvh` interno — é a altura extra que dá ao `scrollY` por onde se mover, e é isso que dispara a troca de vídeo
- Vídeo 2 atrás (sem autoplay, sem loop, toca do zero quando `scrollY > 0`), Vídeo 1 na frente (loop, some em 700ms)
- Volta ao topo → Vídeo 1 reaparece, Vídeo 2 pausa
- Dois anéis concêntricos + marca central, revelando com delays de **0ms / 150ms / 350ms** e durações **1200 / 1200 / 1000ms**
- `padding-bottom:25vh` (`30vh` em sm) empurrando a marca para o terço superior
- Bloco inferior: H1 a **600ms**, parágrafo a **850ms**, ambos 1000ms
- Paddings do navbar: `20px` / `48px` md horizontal, `16px` / `20px` md vertical
- `gap:32px` entre links, botão pill com `pl-20px pr-6px py-6px`, círculo da seta `28px` / `32px` md
- Hambúrguer de 2 traços virando X, menu mobile com stagger de **60ms a partir de 100ms**, CTA a **420ms**, `overflow:hidden` no body

**Adaptado (decisões minhas):**
- Fundo **`#14100D`** (espresso profundo) em vez de preto puro — casa com a paleta em vez de brigar
- **Marcellus + Jost** mantidas. "Magical Source Demo" **não carrega** naquele CDN (testado), e **Geist** está na lista de fontes que o hook sinaliza como genéricas de IA
- Marca central é o arco de sobrancelha, não o chevron geométrico do prompt
- Vídeos gerados de sobrancelha. Os dois do prompt eram **a mesma villa à beira-mar** — imobiliária, sem relação com o negócio
- `.poster` embaixo de tudo: se um vídeo travar, a moldura nunca fica preta
- Navbar alterna para tema claro sobre o hero escuro e volta ao champagne depois — senão o texto sumia
- A revelação da marca dispara quando a cortina do splash abre, não aos 200ms fixos do prompt (aos 200ms ela aconteceria escondida atrás do splash)

O trio duração/CTA que ficava no hero antigo virou a faixa `.strip` logo abaixo.

## De onde veio cada parte

Mistura dos 4 prompts fornecidos:

| Prompt | O que entrou |
|---|---|
| **Axon** | Nav fixa com blur, hero de página inteira, par de CTAs |
| **LumiDerm** | Splash com barra de progresso + revelação em cortina, seção de dois estados com crossfade no scroll |
| **3D Carousel** | Galeria giratória em CSS puro — `perspective:35em`, máscara lateral, `--n`/`--i`, `rotateY`+`translateZ` com `tan()`, 160s em reduced-motion |
| **Aether Lane** | Texto que acende letra a letra no scroll, marquees duplos em direções opostas, stats com entrada escalonada, hambúrguer que vira X |

**Desvios conscientes:**
- LumiDerm pedia sequestro de `wheel` com `preventDefault` → troquei por `position:sticky`. Mesmo efeito, sem quebrar scroll no mobile.
- Marquees ganharam pausa no hover/foco.

## Os dois momentos de impacto

### 1. Entrada com tipografia recortada

O splash não é mais barra de progresso + cortina. Agora "MAGGIE MAC / BROW ATELIER" é **vazado de um painel champagne** via `<mask>` SVG, com o vídeo macro da sobrancelha correndo dentro das letras. O painel então escala 7× e dissolve, entregando o hero.

Detalhe técnico: `preserveAspectRatio="xMidYMid meet"` mantém o wordmark inteiro em qualquer proporção — com `slice` ele era cortado ("GGIE A" no mobile). Para o painel ainda inundar a tela, o `<rect>` é desenhado muito além do viewBox (`-2000` a `5000`) com `overflow:visible` no SVG.

### 1b. Hero — um único frame escolhido

O hero é uma **imagem parada**: `assets/hero-still.jpg`, extraída de `hero-reveal.mp4` no segundo **2.4** — o momento em que a câmera já abriu o suficiente para mostrar os dois olhos com a sobrancelha nítida.

```bash
ffmpeg -ss 2.4 -i hero-reveal.mp4 -frames:v 1 -q:v 1 hero-still.jpg
```

O scroll dá um **push lento** (`scale` 1 → 1.07) em vez de trocar a imagem. Desligado em `prefers-reduced-motion`.

Houve antes uma versão com os dois vídeos raspados pelo scroll (`currentTime` mapeado na posição). Foi substituída a pedido: o frame escolhido vale mais que o movimento. Os dois `.mp4` continuam em `assets/` — `hero-loop.mp4` ainda é usado dentro das letras do splash, e `hero-reveal.mp4` fica de origem do frame.

A resolução do still é **1280×720**, que é a do próprio vídeo — não dá para extrair mais detalhe do que a fonte tem. Como a imagem é de foco raso e suave, segura bem em tela cheia.

### 2. Antes/depois arrastável

O momento assinatura, e é literalmente o que um estúdio de sobrancelha vende. `clip-path:inset()` guiado por uma variável CSS, com Pointer Events para arrastar, setas do teclado, `Home`/`End`, e `role="slider"` com `aria-valuenow` acompanhando.

Na primeira vez que entra na viewport ele faz **uma varredura sozinho** (50 → 88 → 22 → 50, com easing cúbico) para que a interação seja descoberta sem instrução.

**Correção de exposição:** o par veio de duas gerações separadas. Medi as duas na canvas — luminância 179 vs 144, calor (R−B) 59 vs 36 — e levantei a foto "antes" com `brightness(1.2) sepia(.04)` para o wipe ler como uma sessão só, não como dois ensaios.

## Miolo do hero — adaptação do "Vektis Lab"

O centro do hero era um anel concêntrico com monograma. Substituído pela estrutura assimétrica do Vektis:

- Título grande à **esquerda**, cards de dados flutuando à **direita**
- Card pequeno sobrepondo o canto do card grande
- Divisória tracejada dentro do card
- Efeito `line-mask` na palavra final — `repeating-linear-gradient` + `background-clip:text`, a palavra lida como gravada na superfície

Adaptado: o Vektis é tech-brutalista (JetBrains Mono, magenta, borda branca de 2px). Aqui os cards usam fio de 1px, fundo espresso translúcido com blur e numeral em Marcellus. A estrutura é dele; o material é do atelier.

As listras da palavra gravada **escalam com a tipografia** (2px no mobile, 3px acima de 768px) — fixas, elas comiam a letra nos tamanhos pequenos.

## Scroll — nativo, de propósito

Houve uma tentativa de imitar o Lenis (do starter new-era) com scroll suave feito à mão: `preventDefault` no wheel + interpolação a 0.11 por frame. **Foi removido.**

Motivo: `preventDefault` no wheel mata o momentum nativo do trackpad e a interpolação deixa tudo pastoso. O Lenis trata dezenas de casos de borda (arrasto da barra, teclado, âncoras, ponteiro grosso) que 15 linhas não tratam. No macOS o scroller do sistema é melhor que qualquer aproximação.

**Não reintroduza scroll suave em JS aqui.** Se um dia for necessário, use a biblioteca de verdade, não uma imitação.

Também foi cortado o scroll "morto": as seções presas somavam **5,0 telas** (hero 200dvh + process 300vh). Agora somam **3,75** (160dvh + 215vh) — o efeito continua, sem a sensação de página travada.

## ⚠️ Bug corrigido: colisão de classe `in`

O JS de reveal adicionava a classe **`in`** aos elementos revelados. Mas `.in` também é o wrapper de largura de toda seção — e `.atelier .in{display:grid}` passou a valer para o bloco de texto revelado, quebrando os parágrafos em duas colunas.

O estado de reveal agora chama-se **`shown`**. Se for criar seções novas, não reutilize `in` como nome de estado.

## Histórico de direção

Quatro tentativas até chegar aqui. Registrado para não repetir:

1. **Preto quente + cobre, Instrument Serif** — descartada: reusava as fotos do projeto antigo.
2. **Índigo/violeta cósmico, Syne** — descartada: bonita, mas as imagens (íris, montanha) não tinham relação com sobrancelha.
3. **Papel + Bodoni + anotação técnica** — descartada a pedido.
4. **Champagne e espresso** ← atual, pedido explícito de "mais luxo" depois de uma versão brutalista preto/branco/vermelho.

## ⚠️ Placeholders — o que trocar antes de publicar

### 1. As fotos são geradas por IA
As 6 imagens em `assets/` foram geradas para este layout. São sobre sobrancelha e coerentes entre si, mas **não são o trabalho real da Maggie**. Num site de cliente, o antes/depois verdadeiro vende muito mais.

| Arquivo | Uso |
|---|---|
| `hero-portrait.png` | hero |
| `brow-macro.png` | fundo da seção Process |
| `brow-mapping.png` · `threading.png` · `lamination.png` · `tools.png` | carrossel |

### 2. Números inventados
**Não são reais** — preenchem o layout: `2.400` sobrancelhas · `Nine` anos · `4.9` · `98%` em `#results`. As durações (45 / 60 / 75 min) também são chute.

### 2b. Preços — AUD, mercado australiano

Os preços da tabela de serviços estão em **dólar australiano** e foram calibrados pela faixa real do mercado (lamination A$90–130 em Sydney, threading A$25–40, tint A$20–30), então o layout é plausível. **Continuam não sendo os preços da Maggie.**

Histórico do erro, para não repetir: a primeira versão saiu em **libra esterlina (£)**, porque deduzi Reino Unido pelo nome "Maggie Mac" e pelo texto em inglês, sem perguntar o mercado. O negócio é na Austrália. Trocar só o símbolo não bastaria — £65 são cerca de A$125, então os números também estavam errados.

**Regra:** nunca escrever preço sem saber o país. Se não souber, perguntar.

### 3. Links vazios
`data-book`, `data-whatsapp`, `data-instagram` — todos com `href="#"`.

### 4. Idioma
Copy em inglês (os 4 prompts vieram em inglês). Trocar para PT é só texto.

## Verificado

- Sem erros de console, nenhum asset quebrado
- Sem overflow horizontal em 375 / 1340 / 1360
- Menu mobile abre, trava scroll, fecha e destrava, com `aria-expanded`
- Fontes Marcellus + Jost carregando
- `prefers-reduced-motion` respeitado em todas as animações
