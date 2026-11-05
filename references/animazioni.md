# Animare quello che si dice

Icone al tratto che si disegnano da sole di fianco alla testa quando la persona nomina qualcosa di concreto: dice
"calendario" e appare un calendario che spunta un giorno; dice "cose da fare" e appare una lista che si spunta.
Sono l'inserto `anim` di `reel.json` (le icone sono in `scripts/anims.py`).

```json
{"type": "anim", "icon": "calendar", "start": 8.45, "end": 10.3, "pos": "right"}
```
| Campo | Default | A cosa serve |
|---|---|---|
| `icon` | (obbligatorio) | una della tabella qui sotto |
| `pos` | `"right"` | `"right"` o `"left"`: di fianco alla testa (x 35-1045, y 780) |
| `x`, `y`, `size` | secondo `pos`, 780, 260 | a mano, se serve evitare qualcosa |
| `style` | `"line"` | `"badge"`: icona scura su un quadrato bianco arrotondato (si legge su fondi chiari o molto movimentati) |
| `label` | nessuna | testo corto sotto l'icona ("3 giorni", "24/7"); solo se aggiunge un dato che non è nel sottotitolo |
| `color`, `accent` | bianco, l'`emphColor` dei sottotitoli | colore del tratto e del dettaglio (la spunta, la freccia) |
| `sfx` | `"pop"` (basso) | `null` per nessun suono |

## Quale icona per quali parole
| icon | Si disegna | Quando si dice... |
|---|---|---|
| `calendar` | calendario che spunta un giorno | calendario, riunione, agenda, appuntamento, data, organizzare, settimana |
| `clock` | orologio con la lancetta che gira | tempo, ore, minuti, giorni, "in 3 giorni", veloce, risparmiare tempo, promemoria |
| `chat` | fumetto con i tre puntini che scrivono | WhatsApp, chat, messaggio, rispondere, conversazione, DM |
| `send` | aeroplanino di carta che fluttua | inviare, mandare, si invia, automaticamente, proposta |
| `bell` | campanella che si muove + pallino di notifica | notifica, avviso, promemoria, follow-up, "non me lo dimentico" |
| `list` | lista che si spunta | cose da fare, compiti, organizza, priorità, to-do, passaggi |
| `check` | cerchio con spunta | fatto, approvare, confermare, risolto, "la approvo" |
| `shield` | scudo con spunta | permesso, autorizzazione, sicuro, privacy, controllo |
| `lock` | lucchetto che si apre | password, accesso, sbloccare, privato |
| `bulb` | lampadina con raggi | idea, consiglio, trucco, "quello che nessuno ti dice", imparare |
| `sparkle` | scintille | IA, intelligenza artificiale, magia, automatico, nuovo |
| `robot` | robot che sbatte le palpebre | agente, assistente, bot, IA che fa le cose |
| `chart` | grafico che sale | crescere, vendite, risultati, più clienti, metriche |
| `money` | moneta con $ | soldi, prezzo, farsi pagare, gratis, risparmiare, fatturare |
| `search` | lente che si muove | cercare, trovare, controllare, ricercare, "trova i buchi" |
| `mail` | busta con pallino di notifica | mail, email, newsletter, casella |
| `heart` | cuore che batte | lo adoro, preferito, clienti felici, community |
| `phone` | telefono con notifica che pulsa | telefono, app, chiamata, "dal telefono" |
| `user` | persona | cliente, persona, team, profilo |
| `trophy` | trofeo con stella che pulsa | competere, vincere, il migliore, numero uno, guidare |
| `bolt` | fulmine che pulsa | veloce, potenza, energia, all'istante, "in pochi secondi" |
| `rocket` | razzo che decolla | lanciare, ha lanciato, crescere, scalare, decollare, nuovo prodotto |

## Quando sì e quando no
- **Sì** quando la frase nomina qualcosa che si può disegnare (un oggetto, un'azione concreta) e fa parte dell'idea.
  Parti ~0.1 s prima della parola; durata 1.5-2.5 s (che l'animazione riesca a fare il suo movimento).
- **Al massimo una ogni 4-8 secondi.** Se ogni frase ha un'icona, nessuna si nota. Scegli i momenti in cui nel
  video non succede altro.
- **No** se in quel momento c'è già a schermo uno screenshot, una scheda o una registrazione che mostra la stessa
  cosa, né insieme a un `title`, un `number` o una scheda dallo stesso lato.
- **Mai sul viso.** Sempre di fianco alla testa (`pos`), e alterna i lati se ce ne sono due di fila.
  Guarda `snaps.jpg`: se l'icona tocca il viso, i capelli davanti o la mano, spostala.
- **Mai la stessa icona due volte in un reel.** Se "IA" ha già avuto `sparkle`, la volta dopo usane un'altra che
  mostri l'idea di quella frase (`robot`, `bolt`, `trophy`...).
- Se sei in dubbio tra due icone, scegli quella che mostra l'azione ("trova i buchi" -> `search`, non `calendar`).
