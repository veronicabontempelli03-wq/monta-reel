# Trappole in cui siamo già caduti (non ripeterle)

## Audio e trascrizione
- **Whisper mette la prima parola a 0.00** anche se c'è mezzo secondo di silenzio. I punti d'ingresso si prendono
  da `silencedetect`, non dalla trascrizione.
- **Una parola che "dura" 2-8 secondi** (`strumenti[5.24-13.50]`) nasconde una pausa, un inciampo o una presa
  ripetuta. Ritrascrivi quella presa con il modello medium e guarda cosa c'è dentro:
  ```bash
  PY - <<'EOF'
  from faster_whisper import WhisperModel
  import subprocess, numpy as np
  raw = subprocess.run(['ffmpeg','-v','error','-ss','3.5','-t','6','-i','CLIP.MOV','-ac','1','-ar','16000',
                        '-f','s16le','-'], capture_output=True).stdout
  a = np.frombuffer(raw, np.int16).astype(np.float32) / 32768
  m = WhisperModel('medium', device='cpu', compute_type='int8')
  s, _ = m.transcribe(a, language='it', word_timestamps=True)
  print(' '.join(f"{w.word}[{w.start+3.5:.2f}]" for x in s for w in x.words))
  EOF
  ```
  (Usa la `language` di `config.json`.) Se c'è un inciampo ("con i tuoi ser... Meta dal canto suo ha lanciato..."),
  spesso la persona ripete TUTTA la frase più avanti nella stessa presa: usa quella ripetizione intera invece di
  rattoppare.
- **Una parola dubbia che cambia il senso** ("gli strumenti CON l'IA" quando il video dice che muoiono quelli SENZA):
  non inventarla né correggerla in silenzio. Usa un'altra presa o chiedi.
- **Errori tipici di Whisper in italiano:** nomi di tool in inglese ("Claude" -> "cloud", "Higgsfield",
  "Midjourney", "Kling"), elisioni separate male ("l IA", "un app" -> "l'IA", "un'app"), accenti mancanti ("e" al
  posto di "è", "perche" -> "perché"). Passa i nomi in `--prompt` durante l'ingest e correggili con `--fix`.
- `words.json` viene dalla trascrizione del montaggio finale (non dei clip), così un taglio a metà parola non sposta
  mai i sottotitoli. Leggilo tutto dopo `assemble.py`: è il controllo che il montaggio si capisce.
- faster-whisper si rompe con PyAV recente (`metadata_errors`): per questo decodifichiamo l'audio noi con ffmpeg.
- faster-whisper non si installa su Python 3.14+. `setup.py` cerca una 3.10-3.13.

## Immagine
- **iPhone = HDR (HLG, BT.2020, 10 bit).** Senza conversione resta grigio e slavato. `assemble.py` usa una LUT
  propria perché molti ffmpeg non hanno `zscale`. Esposizione 0.62; con 1.0 la pelle si brucia.
- ffmpeg 8+ non accetta più `-vsync`: usare `-fps_mode passthrough`.

## Ritaglio dello sfondo e titoli dietro la testa
- La maschera include ciò che è "attaccato" alla testa: una lampada a soffitto, una mensola. Se il ritaglio copre
  tutto, la lampada copre il titolo.
- **HyperFrames ignora `clip-path` (CSS o GSAP) sul video del ritaglio**: compone i fotogrammi del video per conto
  suo. Per questo `build.py` "cuoce" la linea di taglio in una copia del webm con ffmpeg (`drawbox ... replace=1`).
- Misurare la testa dalla maschera non funziona (la lampada la rovina). I titoli vanno in cima alla zona sicura
  (y 228) e la linea di taglio lascia visibile ~80 % della lettera.
- Nelle inquadrature con zoom la testa arriva quasi al bordo superiore: non c'è spazio per titoli "molto" dietro.
  Non abbassare la linea di taglio sotto il punto in cui si legge il 70 % di ogni lettera.

## HyperFrames / lint
- Ogni `<audio>` ha bisogno di `id`, `data-duration` e del proprio `data-track-index` (altrimenti: tracce sovrapposte).
- Ogni animazione d'uscita che finisce proprio quando finisce la clip ha bisogno di un `tl.set(..., {opacity:0}, fine)`
  (`gsap_exit_missing_hard_kill`).
- Più `fromTo` sullo stesso elemento (il flash): `immediateRender:false` e un `tl.set` iniziale a 0.
- Le lettere di un titolo vanno dentro uno `span` per parola (`.tw`): altrimenti il titolo spezza le parole a metà.
- `nth-of-type` conta i tag, non le classi: i chip hanno una classe con indice (`.c0`, `.a1`).
- Il render dura molto più del video (vari minuti per 1 min con ritaglio, di più in 4K): sempre in background.
- Un render senza `--resolution portrait-4k` esce in 1080 anche se `aroll.mp4` è 4K: `check.py` lo segnala.
- Progetti montati prima del 4K hanno `aroll.mp4` in 1080: per un vero render 4K, rilancia `assemble.py`
  (e `cutout.py` se ci sono titoli dietro). Con il vecchio `aroll` il render esce 4K ma il video è un 1080 ingrandito.

## Contenuto
- Le registrazioni dello schermo spesso mostrano nomi completi, telefoni e chat di altre persone. Ritaglia.
- I numeri di follower non si inventano.

## Scene e b-roll
- **Niente emoji** nei testi di scene, schede o sottotitoli: il render non ha un font per le emoji (escono quadratini).
- I nomi delle classi CSS di ogni inserto sono globali: una classe ripetuta tra due inserti (`.num` del numero col
  contorno e il contatore di `stat`) rende invisibile uno dei due. Metti un prefisso alle classi nuove.
- Un contatore con `onUpdate` non si vede nel render (HyperFrames salta da un fotogramma all'altro): `stat` usa un
  `tl.set` di testo per fotogramma.
- Le registrazioni dello schermo di macOS hanno uno spazio strano (U+202F) prima di "PM" nel nome: usa un glob
  (`Screen*7.32*`) invece di scrivere il nome a mano.
- **`mix-blend-mode` (CSS) sul video si vede in `snap.py` ma NON nel render**: HyperFrames compone i fotogrammi del
  video per conto suo. Per questo la `light` si cuoce nell'a-roll con ffmpeg (`lit_aroll` in `build.py`): luce con
  `blend=overlay` sulla parete e il ritaglio sopra. Controlla la luce su un fotogramma del render, non sugli screenshot.
- In zsh, `$m[b]` dentro un filtro ffmpeg è un indice di array: scrivi `${m}[b]`.
- Con il punch-in la testa occupa quasi tutta la larghezza: le icone vanno in un angolo in alto (y ~235), non di lato.
