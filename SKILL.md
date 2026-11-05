---
name: monta-reel
description: Monta reel verticali per Instagram/TikTok dall'inizio alla fine partendo dai clip grezzi del telefono, in italiano. Sceglie la presa migliore di ogni frase, taglia silenzi e inciampi, fa punch-in dal 4K, converte l'HDR dell'iPhone, mette sottotitoli Cal Sans gialli da 1 a 3 parole con le parole chiave grandi e colorate, titoli dietro la testa, schede di b-roll, sticker, testo a mano, passaggi animati, la scheda profilo per la CTA ed effetti sonori, tutto dentro la zona sicura dei Reels, con render in 4K. Ha uno studio dal vivo nel browser per scegliere stile, font, colori e animazione guardando il reel. Usala con /monta-reel o frasi come "montami questi clip", "fammi il reel", "monta il video della cartella X", "taglia questo in un reel", "aprimi lo studio", "voglio scegliere font e colori dei sottotitoli".
---

# /monta-reel

Clip grezzi dentro, reel finito fuori. La persona non apre mai un editor: guarda una copia sul telefono, dà le
note, e ogni giro è un rebuild + render.

Parla con la persona in italiano, in modo semplice: molti sono principianti e non hanno mai usato il Terminale.

`SK` = la cartella di questa skill (quella che contiene questo SKILL.md), con percorso completo.
`PY` = `SK/.venv/bin/python` (Windows: `SK/.venv/Scripts/python.exe`). Tutti gli script girano con `PY`.
`HF` = `npx --yes hyperframes@0.8.34` (HyperFrames: da HTML a video, Apache-2.0).

Prima di montare il primo reel della sessione leggi `references/stile.md` (il look), `references/animazioni.md`
(quale icona animata va con quali parole), `references/schermo-intero.md` (tagli che spiegano) e
`references/errori.md` (ogni trappola in cui siamo già caduti). `references/piano.md` ha il formato completo di
`edl.json`, `captions.json` e `reel.json`. Tutto esce in 4K (2160x3840): non serve fare niente per non perdere qualità.

## Passo 0: setup (la prima volta, e se qualcosa non va)

1. Se non esiste `SK/config.json`, chiedi in un solo messaggio: nome (va sulla scheda profilo; formato
   `Nome Cognome | sottotitolo`), username di Instagram, cartella dove finiscono i clip, cartella per i progetti
   (default `~/reel-edits`) e lingua dei video (default italiano). I colori hanno un default (`references/stile.md`);
   chiedili solo se la persona ne vuole altri.
2. Lancia `python3 "SK/scripts/setup.py"` (su Windows `py -3`). Stampa una riga STATUS per ogni cosa e READY alla
   fine. Se qualcosa dice MANCA, la riga contiene il comando esatto per installarlo: daglielo spiegando dove
   incollarlo (nel Terminale) e aspetta. Se manca Homebrew su Mac, il primo passo è installarlo da https://brew.sh.
   faster-whisper richiede Python da 3.10 a 3.13; se l'unico Python è 3.14+, la riga lo dice.
3. Salva `SK/config.json` (mai segreti):
   ```json
   {"name": "Nome Cognome | sottotitolo", "instagram": "username", "clipsDir": "~/Desktop/video",
    "projectsDir": "~/reel-edits", "colors": {"caption": "#FFD60A",
    "accents": ["#E2577F", "#E07A4F", "#9B7FD8", "#E8B53E", "#7FA86A"]}, "hdrExposure": 0.62,
    "whisperModel": "small", "language": "it"}
   ```
   `language` è il codice Whisper della lingua dei video (`it`, `es`, `en`...). Se la persona registra in un'altra
   lingua, cambialo lì: tutti gli script lo leggono.

## Regole non negoziabili
1. Tutto dentro la zona sicura dei Reels: in alto 220, in basso 1470, ai lati 35, a destra 100 da y 1155.
   `build.py --safe` la disegna in rosso per gli screenshot.
2. I sottotitoli dicono quello che è stato detto. Correggi ciò che Whisper capisce male (brand, nomi di tool in
   inglese, elisioni come "l'IA" e "un'app", accenti come "è" e "perché"). Non cambiare mai il senso. Se una parola è
   dubbia e cambia il senso, chiedi prima di pubblicare.
3. Niente contorno sul testo: solo un'ombra morbida (è già così in `build.py`).
4. Gli effetti sonori stanno sotto la voce, mai più forti.
5. Niente persone reali estranee a schermo senza l'ok della persona: se una registrazione dello schermo mostra nomi
   completi, telefoni, mail o chat di terzi, ritaglia o copri quella parte e avvisa.
6. I numeri della scheda profilo (follower, post) solo se la persona li dà. Mai inventati.
7. Non cancellare né modificare mai gli originali. Ogni render è una nuova versione in `renders/`.
8. I tagli si verificano sul render finale (`check.py`), non sugli screenshot.

## Flusso

### 1. Importazione
Clip: quelli che nomina la persona, o la cartella che indica dentro `clipsDir`. Progetto: `<projectsDir>/<slug>/`.
```bash
PY SK/scripts/ingest.py <progetto> "<cartella o clip>" --prompt "brand, nomi, parole strane"
```
Guarda `work/sheets.jpg` (inquadrature, angolazioni, quali sono registrazioni dello schermo) e leggi `transcript.txt`.

### 2. Scegliere le prese -> `edl.json`
- Ricostruisci il copione: la gente ripete ogni frase finché non esce bene. Usa l'ULTIMA presa completa e pulita,
  salvo che la persona dica altro.
- Punti esatti: il primo `s` di Whisper spesso è 0.00 anche se c'è silenzio; conferma con
  `ffmpeg -i clip -af silencedetect=n=-35dB:d=0.25 -f null -` ed entra ~0.06 s prima che finisca il silenzio.
  Esci dove comincia il silenzio dopo l'ultima parola.
- Una parola che dura più di ~1 s nella trascrizione nasconde una pausa o un inciampo. Ritrascrivi quella presa con
  il modello medium (`references/errori.md` ha lo snippet) e taglia l'inciampo in due segmenti.
- Due segmenti di fila con la stessa inquadratura: alterna zoom 1.0 / 1.35 (cx 0.5, cy 0.38 nello zoom).
- Mostra alla persona la tabella dell'EDL (id, frase, clip, zoom) e vai avanti senza aspettare.

### 3. Montare e trascrivere il taglio
```bash
PY SK/scripts/assemble.py <progetto>     # aroll.mp4 + segments.json + words.json (Whisper medium sul taglio finale)
```
Leggi `words.json` di seguito: è la prova che il montaggio dice bene la frase. Se una frase è rimasta tagliata o
ripetuta, sistema l'EDL e rilancia (i segmenti che non sono cambiati vengono riusati).

### 4. Ritaglio dello sfondo (solo se ci saranno titoli dietro la testa)
```bash
PY SK/scripts/cutout.py <progetto>       # in background: ~3 min per minuto di video su Apple Silicon
```

### 5. Sottotitoli -> `captions.json`
```bash
PY SK/scripts/captions.py <progetto> --force --emph "parola chiave,frase chiave" --fix "cloud=Claude;perche=perché"
```
- Evidenze: 1 ogni 4-8 secondi, le parole che portano l'idea (il brand, il numero, la chiusa). Escono da sole,
  grandi, in maiuscolo e in un colore d'accento, con lo zoom della camera.
- Poi modifica `captions.json` a mano se serve (unire "giorno per giorno", spostare un'evidenza, non lasciare un
  "di" o un "che" da solo a fine gruppo).

### 6. Piano creativo -> `reel.json`, build e screenshot
Scrivi `reel.json` con gli inserti (vedi `references/piano.md` e le idee di `references/stile.md`): titoli dietro la
testa per ogni sezione, schede con screenshot veri, sticker, testo a mano, passaggi, la scheda profilo nella CTA,
flash ai cambi di tema. Tutti i testi visibili vanno nella lingua del video.

**Anima quello che si dice.** Leggi `words.json` cercando cose che si possono disegnare (un calendario, una chat,
una lista di cose da fare, un permesso, l'IA) e metti un `anim` di fianco alla testa proprio quando vengono nominate:
l'icona si disegna da sola e si muove. La tabella icona-parole e le regole (una ogni 4-8 s, mai sul viso, mai sopra
uno screenshot che mostra già quella cosa) sono in `references/animazioni.md`. Anche se il video arriva già montato
e la persona chiede "solo sottotitoli", proponi quelle che aggiungono qualcosa: è ciò che lo rende più dinamico.

**Tagli a schermo intero che spiegano.** Ogni 8-15 s, quando una frase si capisce meglio vedendola, taglia per
1.5-3 s su una scena animata (`scene`: una chat che si scrive, un'agenda con i buchi liberi, cose da fare che si
spuntano, un numero che conta, una frase chiave), su una clip stock (Pixabay o Pexels) o su una clip della persona
(`scripts/broll.py`). Come scegliere, come scaricarle e le regole: `references/schermo-intero.md`.
```bash
PY SK/scripts/build.py <progetto> --safe
cd <progetto> && HF lint                 # 0 errori
PY SK/scripts/snap.py <progetto> auto    # oppure tempi: 3.3,8.4,17.2
```
Guarda `work/snaps.jpg`: niente copre il viso, tutto si legge, niente esce dalla zona rossa, i titoli dietro la testa
si vedono almeno al 70 %. Correggi e ripeti.

### 6b. Studio dal vivo (se la persona vuole scegliere il look, o chiede "aprimi lo studio")
```bash
PY SK/scripts/studio.py <progetto>       # in background; stampa "STUDIO http://127.0.0.1:4321/"
```
Apri quell'URL nel browser (o passalo alla persona). La persona vede il reel e sceglie stile di base, font, colori,
maiuscole, ombra o box, dimensione, altezza, animazione, zoom e suoni; ogni modifica si salva in `reel.json` ->
`captions` (e `titleFont`, `autoZoom`, `sfx`) e si ricostruisce da sola. Da lì può anche fare il render in 4K.
**Mentre lo studio è aperto, rileggi `reel.json` prima di toccarlo**: la persona lo cambia dalla pagina, non
sovrascrivere quello che ha scelto.
Lo studio ha delle schede: **Stile** (look e controlli), **Testi** (correggere parole e mettere o togliere evidenze:
scrive `captions.json`, quindi rileggi anche quello), **Avvisi** (ciò che esce dalla zona sicura, controllato dopo
ogni modifica) e **Render** (con tempo stimato, annulla e guarda il risultato). La timeline segna evidenze, tagli e
inserti; ha il loop della frase, il fotogramma per fotogramma, la vista con l'interfaccia di Instagram e la modalità
focus. Le opzioni dello studio sono in `assets/studio/options.json`.

### 7. Render in 4K e controllo
```bash
PY SK/scripts/build.py <progetto>        # senza --safe
cd <progetto> && HF render -o renders/<slug>-vN.mp4 --quality high --resolution portrait-4k --crf 14   # in background
PY SK/scripts/check.py <progetto> renders/<slug>-vN.mp4
```
Il render esce in 2160x3840: `aroll.mp4`, il ritaglio e il b-roll stock in 4K sono già 4K, e `--resolution
portrait-4k` disegna testo e grafiche a doppia risoluzione. Non fare mai un render senza `--resolution portrait-4k`
(`check.py` lo segnala). Guarda `work/cut_check.jpg` (ogni coppia prima|dopo mostra la nuova inquadratura pulita) e
l'audio (picco tra -1 e -3 dB).

### 8. Consegna
Passa la copia `-celu.mp4` (1080, da guardare sul telefono) e il render completo in 4K (è quello da caricare).
Racconta in modo semplice cosa hai fatto e quali decisioni spettano alla persona (prese dubbie, parole che non si
capiscono, dati che mancano). A ogni giro di note: cambia il JSON, build, render come `vN+1`. Non sovrascrivere mai
le versioni.

## Note frequenti e come risolverle
| Nota | Cosa toccare |
|---|---|
| "questa parola è sbagliata" | `captions.json` (o `--fix` e `captions.py --force`), oppure la scheda Testi dello studio |
| "il sottotitolo mi copre" | `"y"` di quel gruppo in `captions.json`, o `reel.json` `captions.y` |
| "togli questa presa / usa l'altra" | `edl.json` -> `assemble.py` -> (cutout se ci sono titoli dietro) -> captions |
| "più/meno effetti sonori" | `reel.json` `sfx.gain` (0 spegne tutto) |
| "troppo scuro / troppo chiaro" | `config.json` `hdrExposure` (da 0.5 a 0.75) -> `assemble.py` |
| "niente zoom sulle parole" | `reel.json` `"autoZoom": false` |
| "più cinematografico" / "un altro colore d'immagine" | `reel.json` `"grade": "cine"` (o `calido`, `film`, `frio`) e `gradeAmount`; o lo studio |
| "voglio vedere altri font / colori / stili" | lo studio (passo 6b), o `reel.json` `captions` (`references/piano.md`) |

## Crediti
Basata su [editar-reel](https://github.com/nicolebrener/editar-reel) di Nicole Brener (licenza MIT), adattata
all'italiano.
