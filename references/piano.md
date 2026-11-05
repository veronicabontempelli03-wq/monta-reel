# Formato dei file del progetto

Tutti i tempi di `captions.json` e `reel.json` sono in secondi del reel finale (quello di `aroll.mp4`).
Quelli di `edl.json` sono in secondi di ogni clip originale.

## edl.json (lo scrive Claude al passo 2)
```json
[
  {"id": "hook", "clip": "c5", "in": 0.57, "out": 1.99, "zoom": 1.0, "cx": 0.5, "cy": 0.45,
   "line": "Ti sei accorta che stiamo vedendo"},
  {"id": "hook-b", "clip": "c5", "in": 2.83, "out": 7.70, "zoom": 1.35, "cx": 0.5, "cy": 0.38,
   "line": "la morte degli strumenti..."}
]
```
`zoom` 1.0 = inquadratura intera; 1.3-1.8 = punch-in. `cx`/`cy` = centro del ritaglio (0-1). `line` serve solo da leggere.

## captions.json (lo genera captions.py; si modifica a mano)
```json
[
  {"id": "k1", "start": 0.0, "end": 0.6, "y": null, "emph": null,
   "words": [{"t": "Ti", "s": 0.0, "e": 0.2}, {"t": "sei", "s": 0.26, "e": 0.4}, {"t": "accorta", "s": 0.42, "e": 0.6}]},
  {"id": "k3", "start": 2.84, "end": 3.2, "y": null, "emph": {"color": 0},
   "words": [{"t": "morte", "s": 2.84, "e": 3.2}]}
]
```
- Cambiare una parola: modifica `t`. Unire o separare gruppi: sposta oggetti di `words` tra i gruppi e aggiusta
  `start`/`end` (start = `s` della prima parola).
- `y`: altezza di quel gruppo (px dall'alto). `null` = quella di `reel.json`.
- `emph`: `null` oppure `{"color": n}` (indice di `accents`).

## reel.json (lo scrive Claude al passo 6)
```json
{
  "captions": {"y": 1240, "size": 92, "emphSize": 150},
  "autoZoom": true,
  "zooms":   [{"start": 3.2, "end": 5.0, "scale": 1.18, "ox": 0.5, "oy": 0.35}],
  "flashes": [7.76, 16.44],
  "music":   {"src": "assets/musica.mp3", "volume": 0.08},
  "sfx":     {"gain": 1.0},
  "inserts": [ ... ]
}
```
In `captions.json` un'evidenza può avere il suo colore: `"emph": {"color": "#9B7FD8"}` (un colore per sezione).
`captions.style: "clean"` cambia il look: bianco in Inter ExtraBold e minuscolo, evidenze in corsivo spesso di un
solo colore (`emphColor`, default `#FFE14D`) ed entrata morbida senza rimbalzo. Sta bene con `captions.py --max-words 1`
(una parola alla volta).

### Il look dei sottotitoli (`captions`)
`style` (`pop` o `clean`) dà la base; qualsiasi di queste chiavi la sovrascrive. È ciò che cambia lo studio
(`scripts/studio.py`), quindi un reel toccato lì può avere qualunque di queste:

| Chiave | Valori | pop / clean |
|---|---|---|
| `font`, `emphFont` | `cal`, `inter`, `montserrat`, `montserrat-m` (Medium), `poppins`, `grotesk`, `anton`, `bebas`, `dmserif`, `caveat`, `bootzy` (lo prende da `~/Library/Fonts/BootzyTM.ttf`: ha una licenza propria e non è nel repo; senza, Inter) | `cal` / `inter` |
| `color` | `"#hex"` dei sottotitoli (`null` = `config.json` `caption`) | giallo / `#FFFFFF` |
| `emphColor` | `"#hex"` per tutte le evidenze, `null` = ruotano gli `accents` | `null` / `#FFE14D` |
| `case`, `emphCase` | `asis` (come detto), `auto` (minuscolo a inizio frase), `upper`, `lower` | `asis`, `upper` / `auto`, `upper` |
| `emphItalic` | `true` / `false` | `false` / `true` |
| `shadow` | `light`, `soft`, `strong`, `box` (box scuro dietro), `none`. Mai contorno | `soft` / `light` |
| `anim` | `pop` (rimbalzo e inclinazione), `soft` (sale morbido), `none` | `pop` / `soft` |
| `emphSfx` | `pop`, `click`, `ding` o `null` | `pop` / `null` |
| `size`, `emphSize`, `y` | px | 92, 150, 1240 / 80, 130, 1240 |
| `curve` | px di cui scendono le punte dell'arco: 0 = dritto; 150-250 = sottotitolo ad arco sopra la testa, come un'aureola (con `y` ~430) | 0 |
| `tilt` | fino a quanti gradi si inclina ogni sottotitolo (uno diverso per sottotitolo) | 0 |
| `emphBox` | `"pill"`: l'evidenza su una pillola di `emphColor` con lettere nere o bianche. Non va con `curve` | `null` |
| `sparkles` | `true`: brillantini ✦ (uno bianco piccolo e uno del colore dell'evidenza) ai lati delle evidenze | `false` |
| `emphStickers` | nome di un pack di `~/reel-stickers` (`"girly"`): su ogni evidenza appare con un pop uno sticker del pack (stelle, brillantini, cuori, palle da discoteca), alternando sinistra e destra | `null` |

Gli stili di base dello studio (`assets/studio/options.json` -> `presets`) sono combinazioni di queste chiavi: Pop,
Clean, Bold, Impatto, Editorial, Box, Arco (minuscolo giallo pallido ad arco e chiusa in serif corsivo), Arco
bianco, Cinema, Girly e Girly serif.

### Colore dell'immagine (`grade`, `gradeAmount`)
`"grade"`: `cine` (verde salvia su sfondi e mezzitoni, pelle dorata, neri morbidi, grana e vignettatura), `calido`,
`film` (slavato e desaturato) o `frio`; `null` = naturale. `"gradeAmount"` 0.2-1.5 (default 1). Si cuoce con ffmpeg
sul video principale, sul ritaglio e sul b-roll (una volta per look, in `assets/graded/`; ~1 min in 4K). La grana va
a parte, come livello semitrasparente (`assets/grain-*.webm`): dentro il video lo farebbe pesare giga. Nello studio
(`build.py --preview`) il colore si applica solo alle copie leggere dell'anteprima. Uno sfondo che non è verde non
diventa verde: il look tinge, non ridipinge.

Sopra `captions`, `titleFont` (stessi font) cambia quello dei titoli dietro la testa.

### Tipi di inserto
Tutti hanno `type`, `start`, `end`. Opzionale in tutti: `sfx` (nome del suono, o `null` per nessuno).
I testi visibili (titoli, note, scribble, scene, steps, bio) vanno nella lingua del video.

| type | Campi | Note |
|---|---|---|
| `note` | `text`, `x`, `y`, `w` (640), `rot`, `paper` (`lined`/`pink`), `font` (`caveat`/`dmserif`), `size`, `stickers` (pack per gli angoli, `null` = nessuno), `color` | scheda da quaderno con righe, margine, buchi, graffetta e nastro; il testo si scrive da solo. Va dove c'è spazio (sopra la testa o di lato), mai sul viso: mentre è a schermo, abbassa i sottotitoli di quel momento al petto (`"y": 1240` in `captions.json`) |
| `title` | `text`, `behind`, `color`, `y`, `size`, `font` (`cal`/`hand`), `clipTop` | `behind: true` richiede `cutout.py`. `clipTop` = da che altezza la testa copre (default: lascia ~80 % della lettera) |
| `image` | `src`, `mode` (`card`/`sticker`/`full`), `x`, `y`, `w`, `maxH`, `rot` | `src` relativo al progetto, in `assets/`, oppure `"sticker:<pack>/<nome>"` da `~/reel-stickers` (`scripts/stickers.py list`) |
| `broll` | come `image` + `mediaStart` | video senza audio; `full` copre tutta l'inquadratura |
| `scribble` | `text`, `x`, `y`, `w`, `rot`, `size`, `color` | Caveat, si scrive parola per parola |
| `steps` | `items` (2-4 testi corti), `times` (uno per voce), `title`, `highlight` (indici), `y`, `color` | chip con frecce |
| `profile` | `y`, `followers`, `posts`, `following`, `bio`, `avatar` | nome e username vengono da `config.json`; i numeri solo se la persona li dà; `avatar` default `assets/avatar.jpg` (se manca, iniziali) |
| `webm` | `src`, `x`, `y`, `w`, `h` | animazione con trasparenza fatta a parte |
| `scene` | `kind` (`chat`/`calendar`/`checklist`/`stat`/`text`) + i campi di ognuna, `accent` | scena animata a schermo intero che spiega quello che si dice; dettagli in `references/schermo-intero.md` |
| `light` | `color`, `x`, `y` (centro della luce, 0-1), `size` (%), `blend` (`soft-light`/`overlay`), `strength` | luce colorata sulla parete dietro (richiede `cutout.py`): tinge lo sfondo, mai il viso. Più luci di fila che si sovrappongono di 0.3 s fanno una dissolvenza |
| `anim` | `icon`, `pos` (`right`/`left`), `x`, `y`, `size`, `style` (`line`/`badge`), `label`, `color`, `accent` | icona al tratto che si disegna da sola e si muove, per illustrare quello che si dice; icone e regole in `references/animazioni.md` |
| `number` | `text`, `x`, `y`, `size`, `stroke`, `color` | numero gigante solo col contorno ("1", "2", "3") per ogni punto di una lista; `x` = bordo sinistro della zona in cui si centra. Di fianco alla testa, mai sul viso |
| `handle` | `spots` (`[{"t", "x", "y"}]`) | icona di Instagram + @username come filigrana che cambia posto; solo se la persona lo vuole |
| `endcard` | | chiusura: scurisce il video e mostra il logo di Instagram + @username al centro; solo se la persona lo vuole |

### Screenshot come scheda
Prendi un fotogramma dalla registrazione e ritaglia solo la parte che si legge:
```bash
ffmpeg -ss 80 -i "registrazione.mov" -frames:v 1 -vf "crop=640:210:290:185,pad=iw+80:ih+60:40:30:white" assets/screenshot.png
```
Ritaglia testo che si legga sul telefono (lettere di almeno ~40 px nel reel). Niente nomi completi, telefoni o chat
di terzi.
