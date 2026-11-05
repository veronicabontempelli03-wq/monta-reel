# Il look: dinamico, colorato, animato

Viene dai reel delle creator: sottotitoli di poche parole, parole chiave giganti e colorate, inserti che entrano
con un "pop", testo scritto a mano, b-roll e screenshot veri sopra il video.

## Font (tutti OFL, inclusi in `assets/fonts`)
| Uso | Font |
|---|---|
| Sottotitoli, parole in evidenza, titoli, chip | **Cal Sans** |
| Testo a mano (`scribble`, `title` con `"font": "hand"`) | **Caveat Bold** |
| Scheda profilo, frecce | **Inter** SemiBold / ExtraBold |

Per scegliere un altro font per sottotitoli o titoli (`captions.font`, `emphFont`, `titleFont`, oppure lo studio):
Montserrat Black, Poppins ExtraBold, Space Grotesk Bold, Anton, Bebas Neue e DM Serif Display, anche questi OFL.

## Colori (`config.json` -> `colors`)
- `caption` `#FFD60A`: giallo, il colore base di tutti i sottotitoli.
- `accents`: rosa lampone `#E2577F`, terracotta `#E07A4F`, lavanda `#9B7FD8`, senape `#E8B53E`, salvia `#7FA86A`.
  **Niente fluo né neon** (né turchese `#00E0FF`, né lime `#7CFF4F`, né arancione `#FF7A00`, né rosa chewing-gum
  `#FF3EA5`): sembrano fatti con l'IA. Toni profondi e smorzati, che si leggano sia su pareti chiare sia su fondi
  scuri. Parole in evidenza e titoli ruotano in questa lista (`"color": 0..3` in `reel.json`, o un `"#hex"` diretto).
- **Niente contorno.** Tutto il testo ha solo un'ombra morbida. Se qualcosa non si legge, prima spostalo in una
  zona più scura o uniforme dell'inquadratura; solo dopo, al massimo, più ombra.

## Sottotitoli
- Da 1 a 3 parole, senza punti, virgole o segni. Ogni parola appare quando viene detta (pop corto).
- Altezza di default `y = 1240` (petto), dentro la zona sicura. Mai sopra la bocca o gli occhi.
- Parole in evidenza: da sole, in MAIUSCOLO, ~150 px, colore d'accento, leggera inclinazione, pop con suono e uno
  zoom rapido della camera (1.08). Una ogni 4-8 secondi: se tutto è in evidenza, niente lo è.
- **Italiano:** attenzione alle elisioni ("l'IA", "un'app", "c'è", "dell'algoritmo"): tienile come una parola
  sola, con l'apostrofo dritto, e non lasciare mai un articolo o una preposizione ("il", "di", "che") da soli alla
  fine di un gruppo.

## Inserti e quando usarli
| Momento del copione | Inserto |
|---|---|
| Hook con un'idea forte ("la morte di...") | `title` dietro la testa ("R.I.P.") |
| Ogni sezione / brand / numero ("Shopify", "#1") | `title` dietro la testa, un accento diverso ogni volta, + `flash` |
| Mostra qualcosa di reale (un'app, un risultato) | `image` o `broll` in modalità `card` sopra la testa, con bordo bianco |
| Un oggetto o un logo proprio | `image` in modalità `sticker` (PNG trasparente), pop + fluttua |
| Spiega qualcosa che si capisce meglio vedendolo (una conversazione, un'agenda, una lista, un numero, un luogo) | taglio a schermo intero: `scene` animata, stock (Pixabay o Pexels) o clip propria (`references/schermo-intero.md`) |
| Nomina qualcosa di concreto (un calendario, una chat, cose da fare, un permesso, l'IA) | `anim`: icona al tratto che si disegna e si muove di fianco alla testa (`references/animazioni.md`) |
| Una lista di punti ("primo", "la seconda", "e terzo") | `number` col contorno, di fianco alla testa |
| Battuta, chiusa, reazione ("occhio!", "zero dimenticanze") | `scribble` |
| Un processo o un prima/dopo | `steps` (chip con frecce che appaiono al ritmo della voce) |
| CTA finale | `profile` (scheda Instagram con "Segui" -> "Segui già") |

## Titoli dietro la testa
Vanno in cima alla zona sicura (y 228). La testa copre solo la base delle lettere (~20 %): si leggono sempre e
danno profondità. Non mettere titoli con più di ~10 lettere per riga: la dimensione scende e perdono forza.

## Un colore per sezione (quando la persona chiede più colore)
Un colore per ogni parte del copione (ogni brand, ogni idea), lo stesso per tutto ciò che appartiene a quella parte:
l'evidenza, il `number`, il dettaglio dell'`anim` e l'accento della `scene`. Al cambio di tema cambia il colore:
basta questo a far sentire il montaggio. Sempre i toni di `accents`, mai fluo.

## Luce sullo sfondo
Una sola `light` calda (arancione `#FF8A3D`, `soft-light`) per tutto il video, dall'alto di lato, come una lampada:
scalda la parete senza toccare il viso. **Non cambiare colore della luce per sezione**: uno sfondo che passa dal
verde al giallo al viola sembra un'accozzaglia.

## Movimento
- Punch-in alternati tra i segmenti (1.0 / 1.2-1.35, dal 4K originale), anche se il video è una sola presa: taglia
  all'inizio di una frase. Fai il punch-in sulle frasi senza nulla di fianco alla testa: con lo zoom la testa occupa
  di più e i numeri o le schede laterali finiscono sui capelli.
- Zoom rapido su ogni evidenza (`autoZoom`), zoom lenti a mano nei momenti chiave (`zooms`).
- Flash bianco corto al cambio di tema (`flashes`).

## Suono
Sintetizzati da `setup.py` (propri, liberi da condividere): `pop`, `click`, `ding`, `rise`, `bass`, `whoosh`.
Di default: pop su evidenze e schede, whoosh su titoli e b-roll a schermo intero, click sul testo a mano,
rise + click + ding sulla scheda profilo. Volume generale in `reel.json` `sfx.gain`.

## Colore della camera
Gli iPhone registrano in HDR (HLG). `assemble.py` lo porta in SDR con una LUT propria (`make_lut.py`). Esposizione in
`config.json` `hdrExposure`: 0.62 è naturale; abbassala (0.55) se la pelle si brucia, alzala (0.7) se è scuro.

## Estetica girly (quando la persona la chiede)
Viene dai reel di creator lifestyle: testo sopra la testa, bianco bold (Montserrat Bold) con la parola chiave in rosa
baby `#F9B4D0` o in serif corsivo giallo pallido `#F8EDA8`, brillantini ✦ ai lati, uno sticker (stella glitterata,
cuore cromato, palla da discoteca, brillo cromato) che appare con un pop su ogni evidenza, e ogni tanto una nota da
quaderno (`note`) per una lista o un consiglio. Preset "Girly" e "Girly serif" dello studio. Gli sticker vengono da
`~/reel-stickers/girly` (ritagliati dalle immagini che ha passato la persona con `scripts/stickers.py cut`); per
aggiungerne altri, `cut` con le nuove immagini e guarda `sheet.jpg`. Meno è meglio: 1 sticker per evidenza, 1 nota
ogni 15-20 s.
