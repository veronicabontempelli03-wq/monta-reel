# Video a schermo intero che spiegano quello che si dice

Tagli di 1.5-3 s in cui la persona sparisce e tutto lo schermo mostra ciò di cui sta parlando, con i sottotitoli
sopra. Ci sono tre fonti; usa quella che spiega meglio ogni momento.

| Fonte | Quando | Come |
|---|---|---|
| **Scena animata** (`scene`) | Un processo, un'app, un dato: una chat, un'agenda, una lista, un numero, una frase chiave | si costruisce in HTML, non serve scaricare niente |
| **Stock** (Pixabay o Pexels) | Qualcosa del mondo reale: una città, gente che lavora, un ufficio, un telefono in mano, uno stadio | `broll.py search` + `get` |
| **Clip proprie** | Registrazioni dello schermo dell'app di cui si parla, b-roll girato dalla persona | `broll.py prep` |

## Scene animate: inserto `scene`
Sfondo scuro con un bagliore del colore d'accento; il contenuto va da y 270 a y 1280 (sotto restano i sottotitoli).
Tutti i `times` / `t` sono secondi del reel: fai coincidere ogni apparizione con la parola che la nomina.
I testi della scena vanno nella lingua del video (italiano).
```json
{"type": "scene", "kind": "calendar", "start": 8.45, "end": 10.95, "title": "La tua settimana",
 "busy": [[0,0],[1,2]], "free": [[2,0],[3,2]], "times": [9.55, 9.85]}
```
| kind | Campi | Per |
|---|---|---|
| `chat` | `title` (nome del contatto), `subtitle`, `messages`: `[{"from": "me"/"them", "text", "time", "t"}]` | "risponde al cliente", "mi scrive", una conversazione |
| `calendar` | `title`, `days` (default Lun-Ven), `slots` (default 4), `busy`, `free` (`[giorno, fascia]`), `times` | riunioni, agenda, buchi liberi, appuntamenti |
| `checklist` | `title`, `items`, `tags` (uno per voce, opzionale: "Alta"), `times` (quando si spunta ognuna) | cose da fare, compiti, passaggi, "me le organizza" |
| `stat` | `value`, `prefix`, `suffix`, `label`, `countFor` (s, default 1) | un numero che colpisce: "258 messaggi", "3x di più", "0 €" |
| `text` | `lines` (2-4 righe corte; `*parola*` va nel colore d'accento), `times` | un'idea forte da leggere, la chiusa |

I testi di una scena vengono da quello che dice il video o da ciò che si vede nei suoi screenshot: mai inventare dati,
nomi di clienti o numeri. Se serve un esempio ("Martina chiede una riunione"), che sia generico e senza persone
reali. Niente emoji: il render non ha un font per le emoji ed escono quadratini.

## Stock (Pixabay o Pexels)
```bash
PY SK/scripts/broll.py search <progetto> "woman texting phone" --n 8     # foglio: work/broll-<ricerca>.jpg
PY SK/scripts/broll.py get <progetto> pb123456 --name telefono --start 1 --dur 3  # -> assets/broll/telefono.mp4
```
- Cerca in inglese e in modo concreto ("calendar planner desk", non "organizzazione"). Guarda il foglio (mostra il
  ritaglio 9:16 che resterà) e scegli quello che sembra naturale, con una luce simile al video, senza testo né loghi
  di altri brand e senza volti in primo piano che distraggono. I verticali escono per primi; un orizzontale si
  ritaglia al centro.
- Usa il sito di cui è salvata la chiave: prima Pixabay, altrimenti Pexels. Le chiavi sono gratis e vanno in
  `SK/.pixabay_key` o `SK/.pexels_key` (una riga, in `.gitignore`), oppure in `PIXABAY_API_KEY` / `PEXELS_API_KEY`.
  Se manca, lo script lo dice: la persona la ottiene (Pixabay: da loggata, la chiave appare in
  https://pixabay.com/api/docs/) e la salva lei con `sh SK/scripts/stock_key.sh pixabay`, che non la mostra.
  Non chiederle mai di incollarla in chat. Pexels ha sospeso le nuove chiavi a ottobre 2026.
- Le clip di Pixabay e Pexels si usano gratis e senza attribuzione.

## Clip proprie
```bash
PY SK/scripts/broll.py prep <progetto> "<file>" --name schermo --start 12 --dur 3 --fit --crop 1200:900:400:150
```
- `--fit` per i video orizzontali (una registrazione dello schermo): entra intero sopra una copia sfocata.
- `--crop w:h:x:y` prima ritaglia la parte che si legge (in pixel dell'originale); senza, il testo di uno schermo di
  computer resta piccolo sul telefono.
- Copri o ritaglia nomi, telefoni e chat di terzi prima di usarla.

In `reel.json` la clip va come `{"type": "broll", "src": "assets/broll/schermo.mp4", "start": 20.5,
"end": 23.0, "mode": "full"}`.

## Quando sì e quando no
- **Uno ogni 8-15 s**, da 1.5 a 3 s ciascuno. Di più e la persona smette di stare nel suo stesso video.
- Mai nell'hook (i primi 2-3 s sono del viso) né nella CTA finale.
- Taglia in una pausa o all'inizio della frase che spiega, e torna alla persona prima della frase successiva.
- Se il video ha già uno screenshot o una registrazione che mostra la stessa cosa, non coprirlo con altro.
- Un `anim` (icona) che cade dentro un taglio a schermo intero resta coperto: toglilo o spostalo.
