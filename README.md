# /monta-reel

### Una skill di Claude Code by [@bontestudio.ai](https://www.instagram.com/bontestudio.ai)

Registri i tuoi clip col telefono, li metti in una cartella, scrivi `/monta-reel` e Claude ti restituisce il reel
finito: sceglie la presa migliore di ogni frase, taglia silenzi e inciampi, mette i sottotitoli col tuo stile,
titoli dietro la testa, screenshot, sticker ed effetti sonori. Non apri nessun editor: guardi il risultato sul
telefono, dici cosa cambiare e Claude rifà il render.

Pensata per reel in **italiano** (funziona anche in altre lingue, basta dirlo al primo avvio).

---

## Cosa fa

1. **Trascrive tutte le prese** con i tempi parola per parola (Whisper, sul tuo computer, senza mandare niente a nessuno).
2. **Monta il copione con la presa migliore** di ogni frase (puoi ripetere ogni battuta tutte le volte che vuoi) e
   toglie pause e inciampi, anche a metà frase.
3. **Taglia dall'originale in 4K**, con punch-in per dare ritmo, e converte l'HDR dell'iPhone in colore normale così
   su Instagram non sembra slavato.
4. **Sottotitoli dinamici**: Cal Sans gialla, da 1 a 3 parole, ogni parola appare quando la dici; le parole chiave
   escono da sole, giganti e colorate, con uno zoom della camera. Oppure lo stile **clean**: una parola bianca alla
   volta e le evidenze in corsivo di un solo colore.
5. **Anima quello che dici**: icone al tratto che si disegnano da sole di fianco alla testa (un calendario che spunta
   un giorno, una chat che scrive, una lista che si spunta) e numeri giganti col contorno per ogni punto di una lista.
6. **Tagli a schermo intero che spiegano**: scene animate (una chat che si scrive, un'agenda con i buchi liberi, cose
   da fare che si spuntano, un numero che conta), clip stock di [Pixabay](https://pixabay.com/api/docs/) o Pexels
   (chiave gratuita) o le tue registrazioni dello schermo.
7. **Ti ritaglia dallo sfondo** per mettere i titoli dietro la testa.
8. **Inserti animati**: schede con screenshot veri, b-roll, sticker, testo scritto a mano, passaggi con frecce
   ("prima: la tua app → ChatGPT → la tua app") e il tuo profilo Instagram con il pulsante "Segui" in chiusura.
9. **Effetti sonori** leggeri (pop, whoosh, click, ding), sempre sotto la voce.
10. **Controllo qualità**: tutto dentro la zona sicura dei Reels, controlla ogni taglio fotogramma per fotogramma e
    ti dà una copia leggera per il telefono.
11. **Esce in 4K** (2160×3840): il video si monta, si ritaglia e si renderizza in 4K dagli originali, e il testo si
    disegna a doppia risoluzione.
12. **Studio dal vivo**: una pagina sul tuo computer dove vedi il reel e scegli stile, font, colori, dimensione,
    altezza e animazione dei sottotitoli; ogni modifica si vede in un secondo e resta salvata. Da lì parte anche il
    render in 4K.

---

## Cosa ti serve

- Mac, Windows o Linux. Su Mac con chip Apple il ritaglio dello sfondo è molto più veloce.
- [Claude Code](https://docs.anthropic.com/en/docs/claude-code)
- [ffmpeg](https://ffmpeg.org), [Node.js 22+](https://nodejs.org), Python da 3.10 a 3.13

Non serve installarli prima a mano: la prima volta la skill controlla il tuo computer e ti dice esattamente quale
comando incollare se manca qualcosa.

| | Mac | Windows (PowerShell) | Linux |
|---|---|---|---|
| ffmpeg | `brew install ffmpeg` | `winget install --id Gyan.FFmpeg -e` | `sudo apt install ffmpeg` |
| Node.js | `brew install node` | `winget install --id OpenJS.NodeJS.LTS -e` | [nodejs.org](https://nodejs.org) |
| Python | `brew install python@3.12` | `winget install --id Python.Python.3.12 -e` | `sudo apt install python3.12 python3.12-venv` |

Su Mac, se il comando `brew` non esiste, prima installa Homebrew da [brew.sh](https://brew.sh).

---

## Installazione

Incolla questo in Claude Code:

```
Installa la skill di https://github.com/veronicabontempelli03-wq/monta-reel in ~/.claude/skills/monta-reel
```

Oppure nel Terminale:

```bash
git clone https://github.com/veronicabontempelli03-wq/monta-reel ~/.claude/skills/monta-reel
```

Poi apri Claude Code e scrivi `/monta-reel`. La prima volta ti chiede nome, username di Instagram e dove salvi i
clip (ci vogliono circa 2 minuti e non si ripete).

---

## Come si usa

```
/monta-reel i clip della cartella "reel ia" sul Desktop
```

Claude ti mostra quale presa ha scelto per ogni frase e va avanti. Quando ha finito ti passa il video. Chiedigli le
modifiche come le chiederesti a una montatrice:

- "al secondo 12 dice Claude, non cloud"
- "togli la parte su Meta"
- "il sottotitolo mi copre la bocca alla fine"
- "meno effetti sonori"
- "usa l'altra presa dell'hook"

Ogni giro è un nuovo render (`v2`, `v3`...). Le versioni precedenti non vengono mai sovrascritte e i tuoi file
originali non vengono toccati.

Se preferisci scegliere tu il look, chiedi **"aprimi lo studio"**: si apre una pagina con il reel e i controlli di
fianco (11 stili di base, tra cui uno ad arco sopra la testa, uno cinema e due girly; 11 font, colori, maiuscole,
ombra o box, dimensione, altezza, animazione, zoom e suoni). Quello che scegli resta in `reel.json`, così dopo puoi
continuare a chiedere modifiche a Claude.

---

## Personalizzare lo stile

Il modo più facile è lo studio (sopra). Altrimenti, tutto il look sta in `config.json` (colori),
`reel.json` -> `captions` (font, colori e animazione di ogni reel; vedi `references/piano.md`) e
`references/stile.md` (le regole che segue Claude). Per cambiare i colori di tutti i reel, modifica
`caption` (il colore dei sottotitoli) e `accents` (quelli delle evidenze e dei titoli) in `config.json`.

---

## Crediti e licenze

- Basata su [editar-reel](https://github.com/nicolebrener/editar-reel) di
  [@nicolebrener_digital](https://www.instagram.com/nicolebrener_digital) (licenza MIT), tradotta e adattata
  all'italiano da [@bontestudio.ai](https://www.instagram.com/bontestudio.ai).
- Render: [HyperFrames](https://github.com/heygen-com/hyperframes) (Apache-2.0), da HTML a video e ritaglio dello sfondo.
- Trascrizione: [faster-whisper](https://github.com/SYSTRAN/faster-whisper) (MIT).
- Animazioni: [GSAP](https://gsap.com) (si scarica durante l'installazione, non è nel repo).
- Font: Cal Sans, Caveat, Inter e gli altri in [assets/fonts](assets/fonts/), con [SIL Open Font License](assets/fonts/).
- Effetti sonori: sintetizzati da `scripts/setup.py`, propri.
- Il codice di questa skill: licenza MIT (vedi [LICENSE](LICENSE)).
