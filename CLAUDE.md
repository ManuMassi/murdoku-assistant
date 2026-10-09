# CLAUDE.md

Questo file fornisce indicazioni a Claude Code (claude.ai/code) per lavorare con il codice in questo repository.

## Cos'è

Un'app web autosufficiente in un unico file, [index.html](index.html), che sostituisce carta e penna per risolvere i puzzle **Murdoku** (murdoku.com) — puzzle logico-deduttivi in cui ogni sospettato sta su una sola casella, con al più una persona per riga e per colonna (come torri non attaccanti su una scacchiera). L'interfaccia ricalca volutamente quella del gioco originale: pannello dei sospettati a sinistra, scena del crimine al centro con etichette C1…Cn / R1…Rn, rail degli strumenti a destra, animazioni "da gioco".

Il puzzle si carica come **PDF** (i fogli stampabili di murdoku.com: l'app disegna la tavola dal PDF, ne trova la griglia e legge nomi, indizi, indizi generali e titolo) oppure come **foto** (la griglia viene riconosciuta automaticamente, con inclinazione e prospettiva). Non c'è build step, non c'è server, non c'è package.json, nessuna dipendenza — nemmeno font, CSS o librerie esterne, compresa la lettura dei PDF: il file si apre direttamente nel browser. L'app è pensata per PC (mouse e tastiera); usa i Pointer Events, ma non c'è un percorso di interazione dedicato al touch.

## Comandi

Non ci sono strumenti di build/lint/test. L'unica verifica automatica è un controllo di sintassi sul blocco `<script>`:

```bash
node -e "
const fs = require('fs');
const html = fs.readFileSync('index.html', 'utf8');
const match = html.match(/<script>([\s\S]*)<\/script>/);
fs.writeFileSync('_check.js', match[1]);
" && node --check _check.js && rm _check.js
```

Per provare l'app basta aprire `index.html` nel browser. Per le prove automatizzate in un browser pilotato conviene servire una copia con `python3 -m http.server --bind 127.0.0.1` invece di aprirla come `file://` o `data:`: in alcuni di quei contesti lo storage è disabilitato e lo stato non si salva. Foto e PDF di prova non vanno nel repository (`.gitignore` esclude immagini, PDF e le pagine usa-e-getta `_*.html`).

Le parti senza DOM si provano anche in Node: il modulo `Pdf` (lettura del testo, `Pdf.probe`) e `readCaseFromGlyphs`/`findBoardRect` si possono estrarre dal file ed eseguire su PDF veri o sintetici (`Pdf.render` e il riconoscimento della griglia invece richiedono un canvas, quindi il browser). Per i casi che i PDF di esempio non coprono (24 sospettati, layout diversi) è comodo generare un PDF sintetico con uno script Python: font standard con un array `/Widths` esplicito, testo posizionato con `Tm`, rettangoli e linee per la tavola.

## Architettura

Tutto risiede in una IIFE dentro il tag `<script>` di `index.html`, divisa in sezioni con intestazioni `// ---------- … ----------` (stato, i18n, helpers, cronometro, pannello sospettati, history, persistenza, rendering, interazione con le celle, gomma, chiudi il caso, riconoscimento della griglia, prospettiva, allineamento a mano, pannello ridimensionabile, dimensioni, immagine, editor della foto, modulo PDF, lettura del caso dal PDF, drag & drop, strumenti, modali, tastiera, export/import, init). Le parti dipendono l'una dall'altra: conviene scorrere l'intero script prima di modificare una funzione in isolamento.

### Modello dati

- `rows`/`cols` (possono differire, da 2 a `MAX_GRID` = 24), `cells` (`rows×cols` di `{x: bool, xBy: string|null, decision: string|null, candidates: string[], mark: string|null}`, indicizzato `cells[r][c]`). `xBy` è la lettera della decisione che ha messo quella `X` (vincolo torre), `null` se l'ha messa l'utente: serve alla gomma per togliere una decisione insieme alle sue `X` e solo a quelle.
- `gridBox = {x, y, w, h, rot, colFr, rowFr}`: x/y/w/h in % di `#imageContainer` (il rettangolo **non ruotato**), `rot` in gradi attorno al centro, `colFr`/`rowFr` proporzioni di colonne/righe (`null` = tutte uguali) ricavate dalla foto. `normalizeGridBox()` scarta proporzioni che non corrispondono alle dimensioni correnti; chi cambia righe/colonne deve azzerarle (`colFr = rowFr = null`).
- `img` (data URL o `null`) è la foto/tavola di sfondo.
- Metadati del caso, **non** stato di gioco: `puzzleTitle`, `suspectNames` e `suspectClues` (mappe lettera → testo), `generalClues`, `showPortraits`, `customColors` (lettera → colore scelto dall'utente).
- Lettere: `objectCount(rows, cols)` = `min(rows, cols)` persone. `validLetters(count)` restituisce le prime `count-1` lettere di `SUSPECT_LETTERS` (`'abcdefghijklmnopqrstuwyz'`: salta `v`, che è sempre la vittima, e `x`, che è sempre il tasto dell'esclusione) più `v` in fondo. Con 24 persone: A…U, W, Y, V. È l'unica fonte di verità sulle lettere valide.

### Le regole del gioco vivono in `applyActiveToolToCell(r, c, opts)`

È l'unico punto di passaggio per ogni modifica alle celle (clic, pressione prolungata, trascinamento, tastiera). Non duplicare questa logica altrove. `opts`: `mode` (forza `'pencil'`/`'pen'`, usato dalla pressione prolungata), `intent` (`'on'`/`'off'`: il trascinamento impone la stessa azione a tutte le caselle del tratto invece di alternarle), `silent` (niente toast né animazione di rifiuto, per il trascinamento), `noHistory` (il trascinamento fa un solo `pushHistory` alla fine), `tool` (usa questo strumento invece di `activeTool`: Canc/Backspace passano `'erase'` senza cambiare lo strumento scelto).

- Indizio (`activeMode === 'pencil'`): più indizi per casella; vietato per una lettera già decisa altrove (`isGloballyDecided`).
- Decisione (`'pen'`): solo su casella senza `X` e senza decisione, e solo se la lettera non è già decisa altrove. Svuota indizi e simbolo della casella, rimuove la lettera dagli indizi di tutte le altre celle e mette `x: true` su ogni altra cella della stessa riga e colonna (vincolo torre, con le X che compaiono "a onda"); a ognuna di quelle nuove scrive `xBy = lettera`. Non si ritira ricliccandola: solo Undo o la gomma. Dopo una decisione (o undo/redo/reset/import/cambio dimensioni) va richiamato `updateLetterButtonsState()`, che aggiorna timbri delle card, barra di avanzamento e lo stato del bottone "Chiudi il caso".
- `X`: si alterna liberamente tranne su una casella con decisione; impostarla svuota indizi e simbolo. Una `X` messa o tolta a mano ha `xBy = null`.
- Gomma (`'erase'`): svuota la casella (X, indizi, simbolo, decisione). Se c'era una decisione, `liftDecisionCrosses()` toglie le `X` con `xBy` uguale a quella lettera e poi fa richiudere righe e colonne alle decisioni rimaste: una `X` può servire a due decisioni (quella della prima registra solo la prima). Le `X` messe a mano restano. Le partite salvate prima di `xBy` non hanno la provenienza: lì la gomma toglie la decisione ma lascia le sue `X`.
- Simbolo nota (`activeTool = '#id'`, tasti `1`–`4`): annotazione libera, uno per casella, non ammesso su `X` o decisione, ripremerlo lo toglie.
- Ogni ramo che modifica una cella chiama `ensureTimerRunning()`.

### Interazione con le celle

`activeTool` vale `'select'`, `'x'`, `'erase'`, una lettera, oppure `'#'+id` (`isLetterTool()` esclude i primi tre e i simboli); `activeMode` vale `'pencil'` (indizio) o `'pen'` (decisione), scelto dall'interruttore INDIZIO/DECISIONE o dal maiuscolo/minuscolo del tasto.

- `onCellPointerDown` avvia, se lo strumento è una lettera, il timer della **pressione prolungata** (`PLACE_HOLD_MS` = 480 ms): allo scadere piazza la lettera come decisione qualunque sia la modalità, con un anello che si riempie (`.charging`).
- Il **trascinamento** (`strokeMove`, che cerca la casella sotto il puntatore con `document.elementFromPoint`) applica lo stesso strumento a più caselle: X, indizi e simboli, mai le decisioni (`canPaint()`). L'azione la decide la prima casella (`paintIntentAt`), tutto il tratto è un solo passo di history (`strokeEnd`), e `suppressClick` impedisce che il clic che segue il rilascio riapplichi lo strumento.
- `onCellClick` seleziona la casella e applica lo strumento, tranne con `'select'` ("Solo selezione").
- `renderToolHint()` tiene in fondo alla scena (`#toolHint`) una riga sempre visibile con cosa scriverà il prossimo clic: con il clic che compila, la differenza tra indizio e decisione deve leggersi senza cercarla.
- `updateGhost(div)` disegna nella casella sotto il mouse un'anteprima trasparente. È volutamente "stupida": guarda solo se la cella ha una decisione o una `X` e **non** replica le regole, che restano in `applyActiveToolToCell` (se una mossa è illegale si clicca e si legge il toast). Non aggiungere controlli di regole qui.
- Tenere premuta una card chiama `highlightLetterOccurrences(letter)`; i listener globali `pointerup`/`pointercancel` (`clearHoldHighlights()`) tolgono l'evidenziazione ovunque avvenga il rilascio.
- Il bottone della **gomma** (`#btnEraser`) ha due gesti. Un clic breve sceglie lo strumento `'erase'` (anche trascinando). Tenuto premuto `ERASE_HOLD_MS` (1,5 s) svuota tutte le caselle, con un riempimento che parte dopo `ERASE_ARM_MS` (così un clic non lo fa lampeggiare); foto, riquadro, nomi, indizi e cronometro restano, e l'operazione è un passo di history (Undo la annulla). `eraseFired` impedisce che il `click` che segue una pressione lunga scelga anche lo strumento.

### Layout

`#app` è una griglia CSS `grid-template-columns: var(--left) minmax(0,1fr) var(--right)` a tutta altezza, senza barra in alto: `aside#leftPanel` (fascicolo: logo, lingua, titolo, cronometro, sospettati, avanzamento, indizi generali, simboli nota), `main#stage` (la scena) e `aside#toolsPanel`. Il `minmax(0,…)` è essenziale: senza, il contenuto impedirebbe alla colonna centrale di restringersi e `#stage` non avrebbe una larghezza definita, che è ciò su cui `updateImageSize()` calcola lo spazio. I pannelli laterali scorrono internamente.

Il pannello sinistro è **ridimensionabile** con `#splitter` (`--left`, limitato da `clampLeft()` in modo che restino almeno 380 px alla tavola; doppio clic per tornare al default). Ogni cambio di spazio passa da `relayout()`: dimensione dell'immagine, riquadro, editor se aperto e rimisura degli indizi nelle card.

**Viste del pannello dei sospettati** (`#app[data-view]`, `applyView()`/`setView()`): `cards` (le schede del foglio originale), `list` (una riga per sospettato, pannello stretto) e `rail` (solo le lettere in due colonne, `RAIL_W` = 104 px; nome e indizio in `#railTip` passando sopra, la ★ per gli indizi generali, **»** riapre). È una preferenza del dispositivo (`murdoku_view_v1`); senza scelta vale `auto`: `list` oltre 12 persone, `cards` sotto. Le viste sono quasi solo CSS sugli stessi elementi delle card; ognuna ha la sua larghezza salvata (`murdoku_left_w_v1` per le schede, `murdoku_left_w_list_v1` per l'elenco). Servono alle griglie grandi: la tavola è limitata dalla larghezza che le lasciano i pannelli, e con 24×24 su 1440×900 passa da 23 px a 31 px per casella. Il bottone `#btnFullscreen` (Fullscreen API) toglie l'altezza di schede e barra del browser.

### Pannello dei sospettati

`renderSuspects()` genera una card per ogni lettera di `validLetters(...)`: lettera grande colorata (o ritratto), campo nome, nuvoletta dell'indizio, timbro "PIAZZATO" quando la lettera è decisa; la `v` ha il nastro "VITTIMA". Il colore di una lettera (`letterColor()`, tabella `PORTRAIT_COLORS` a 26 voci più `VICTIM_COLOR`) è indicizzato sull'alfabeto, non sulla posizione nella lista, così resta stabile cambiando dimensioni; lo stesso colore serve per la decisione nella cella e per i gettoni degli indizi. La tavolozza è fatta perché lettere vicine siano ben distinte: le prime 12 a mano, le altre scelte una alla volta come le più lontane (ΔE2000) da quelle già prese, tutte con contrasto ≥ 6,5 con la lettera scura e lontane dal rosa della vittima.
- **Colore a scelta**: tenendo premuta la lettera/ritratto di una card per `COLOR_HOLD_MS` si apre `#colorPop` (tavolozza con segnata la lettera che usa già ogni colore, colore personalizzato, predefinito). `setLetterColor()` scrive `customColors`, che `letterColor()` guarda per primo, e ridisegna card, celle e riga dello strumento: `renderCells()` riconosce il cambio perché il colore fa parte delle chiavi `div._tc`/`div._cand`, e la cache dei ritratti ha il colore nella chiave. `suppressCardClick` impedisce che il clic dopo la pressione lunga selezioni anche la lettera.

- **Ritratti**: spenti di default (`showPortraits = false`, la card mostra la lettera). Si accendono quando i sospettati arrivano dal PDF. `portraitOf(letter)` = `avatarSVG(letter, genderOfText(suspectClues[letter]))`: il sesso viene dal primo pronome dell'indizio (she/her → donna, he/his/him → uomo) e sceglie tra stili di capelli maschili o femminili; cambiando il pronome nell'indizio cambia il ritratto.
- `fitClue()` rimpicciolisce il corpo dell'indizio fino a 10 px e poi allunga la nuvoletta invece di tagliare il testo (nell'elenco il corpo è fisso e il riquadro è alto quanto il testo). Va misurato a lista completa (le ultime card fanno comparire la barra di scorrimento, che stringe tutte le altre): per questo `renderSuspects()` fa un secondo passaggio alla fine e un `ResizeObserver` rimisura quando cambia la larghezza della lista.
- Nomi, indizi, titolo e indizi generali sono metadati: vengono persistiti ed esportati, ma **non** entrano negli snapshot dell'undo. I loro campi chiamano `e.stopPropagation()` sul `keydown`, e il listener globale esce subito se il target è un `input`/`textarea`/`select` **prima** di gestire Ctrl+Z/Y (così dentro i campi resta l'undo del testo).

### La griglia sopra la foto

- `renderGridBox()` posiziona `#gridBox` (con `transform: rotate(...)`) e imposta le righe/colonne di `#cellsGrid` da `colFr`/`rowFr` (`frTemplate`).
- Le etichette degli assi stanno **fuori dalla cornice della foto**: `positionAxisLabels()` calcola dove cade il centro di ogni colonna/riga sul bordo del riquadro ruotato. `updateCellSizeVar()` imposta `--cell-size` (dalle dimensioni non trasformate) e la classe `compact-axis` (sotto i 34 px resta solo il numero, senza "C"/"R").
- `renderAxisHighlight()` evidenzia riga e colonna della selezione (`.on`) e marca `.done` quelle che hanno già una decisione.
- **I gettoni degli indizi si ridimensionano in base a quanti sono**: `chipScale(n)` è calibrato perché quel numero di gettoni riempia righe intere nel 92% di cella lasciato da `inset:4%` e dal `gap` di `.cell .candidates` (4 stanno meglio in 2×2 che in una riga da 4). Toccare inset o gap cambia come vanno a capo: con 1…9 indizi le righe devono venire 1, 1, 1, 2, 2, 2, 3, 3, 3.

### Allineamento a mano

`locked === false` è la modalità di allineamento (bottone `#btnAlign`, classe `unlocked` su `#app`, `renderLockButton()`): le celle non si modificano, il riquadro si sposta trascinandolo, si ridimensiona dagli angoli (lungo gli assi del riquadro ruotato, con l'angolo opposto fermo) e si ruota con la manopola `#rotHandle`. `#alignPanel` ha righe/colonne ±, rotazione a 0,1° con ripetizione tenendo premuto, "EVEN" (azzera `colFr`/`rowFr`) e "DONE". Da tastiera: frecce = sposta (Shift più veloce), `[` `]` = ruota, Invio/Esc = fine. `capture()` avvolge `setPointerCapture` in un try/catch perché può fallire (es. puntatore già rilasciato). `#alignPanel` sta in fondo alla scena sopra la tavola: entrando in allineamento `syncStagePadding()` alza il margine inferiore di `#stage` della sua altezza (più gli angoli che sporgono), così la tavola si rimpicciolisce e gli angoli in basso restano afferrabili; `relayout()` lo ricalcola, perché la barra può andare su due righe.

### Riconoscimento della griglia dalla foto

`detectGridFromImage(expect)` gira sul `load` di `bgImage` quando c'è una foto nuova (`pendingAutoDetect`, perché prima del `load` non ci sono né dimensioni né pixel) e a richiesta da "Detect grid". Il metodo, descritto anche nel commento in testa alla sezione:

1. **Inclinazione**: si cerca l'angolo che rende più netti i profili del gradiente (ricerca a 0,5° su ±5° a 380 px, poi rifinitura a 0,1° a 700 px). Gli angoli scoperti dalla rotazione si riempiono con il colore medio, per non creare bordi finti.
2. **Linee** (`axisLattice`): tra i picchi del profilo si sceglie, con programmazione dinamica, la catena dal primo all'ultimo bordo forte con la spaziatura più regolare **localmente** (la spaziatura può cambiare piano per la prospettiva, non a salti). Una linea troppo debole viene ricostruita come intervallo doppio. Il profilo è "a gradiente limitato" e la **presenza** (`presence`, su 12 fasce) premia le linee che attraversano tutta l'immagine: è ciò che distingue le linee vere dal bordo inferiore di una fila di oggetti.
3. Le posizioni trovate diventano `colFr`/`rowFr`.

Filtri contro i falsi positivi, tarati su immagini senza griglia: almeno 4 celle per lato, celle quasi quadrate (0,8–1,25), ≥ 70% di linee vere, irregolarità massima, presenza ≥ 0,75. Sotto soglia non si applica nulla: meglio nessuna risposta che una sbagliata. Se il passo di un asse è circa metà dell'altro si rifà l'analisi imponendo un passo minimo coerente.

`autoDetectGrid()`: se le dimensioni trovate cambiano quelle correnti e la griglia non è vuota chiede conferma (se l'utente rifiuta applica solo il riquadro); se il riconoscimento fallisce apre direttamente l'allineamento a mano. Due variabili di passaggio servono al PDF: `detectExpect = {n, box}` (numero di persone noto e posizione delle caselle letta dal PDF: il riconoscimento cerca solo passi compatibili con `n`, e se non trova `n×n` usa `box` con righe uguali) e `detectNote` (testo da premettere al toast dell'esito). `detSource` permette all'editor di far girare il riconoscimento su un'altra immagine.

### Foto in qualsiasi formato

Ogni foto passa da `imageFileToUrl(file)` (chiamata da `loadImageFile`), che restituisce sempre un data URL JPEG o PNG, cioè qualcosa che ogni browser sa mostrare anche dopo un export/import:

1. prima il browser (`loadImageEl`: JPEG, PNG, WebP, AVIF, GIF, BMP, SVG, e in Safari anche HEIC e TIFF; l'orientamento EXIF lo applica il browser);
2. **HEIC/HEIF** (`Heif`): il contenitore ISO BMFF si legge a mano (`pitm`, `iinf`, `iloc`, `iref`, `ipco`/`ipma`, `idat`) e le immagini HEVC — sull'iPhone una griglia di tessere 512×512 — si fanno decodificare al decoder video del browser con **WebCodecs** (`VideoDecoder` configurato con la stringa del codec ricavata da `hvcC` e `hvcC` stesso come `description`), incollando i fotogrammi su un canvas e applicando `irot`/`imir`. Chrome ed Edge lo supportano (accelerazione hardware); dove manca (`VideoDecoder` assente o `isConfigSupported` falso) l'errore porta il flag `heic` e compare un messaggio dedicato;
3. **TIFF** (`Tiff`): strisce a 8 bit non compresse, LZW (con predittore 2), Deflate o PackBits, in grigi, RGB/RGBA o a palette;
4. ultima spiaggia (`embeddedJpeg`): la più grande anteprima JPEG contenuta nel file, che copre i RAW delle fotocamere.

Poi la foto viene ridotta a `PHOTO_MAX_SIDE` (2400 px) e ricodificata in JPEG (su fondo bianco, per le trasparenze); un JPEG/PNG/WebP/GIF già piccolo (≤ 2400 px e ≤ `PHOTO_KEEP_BYTES`) resta com'è. Serve anche alla persistenza: una foto da 12 megapixel in base64 riempirebbe da sola il `sessionStorage`. Il drop e il selettore accettano anche file senza tipo MIME riconoscendo l'estensione (`isImageFile`); un file di tipo sconosciuto si prova comunque come immagine, e se non lo è compare `toastImageUnsupported`.

Per provare questa parte: `sips -s format heic|tiff …` su macOS crea file di prova (l'HEIC di una foto grande ha la stessa griglia di tessere dell'iPhone; cambiando il byte dopo `irot` si prova la rotazione), e un Chrome headless pilotato via DevTools Protocol (`--remote-debugging-port`, `WebSocket` di Node) permette di confrontare il risultato con l'originale.

### Editor della foto: ritaglia, ruota, prospettiva

`#editModal` ha due modalità. *Ritaglia e ruota*: cornice con maniglie, rotazione di 90°, raddrizzamento fine (±45°), "Auto" (raddrizza e ritaglia attorno alla griglia usando il riconoscimento). *Prospettiva*: quattro angoli trascinabili, "Trova angoli" (`findBoardCorners`: cerca la linea nera spessa del bordo esterno su ogni lato), "Raddrizza" (omografia + `warpBoard`). `commitPhoto(url, base, edit)` usa il risultato e rilancia il riconoscimento.

I ritocchi ripartono sempre dalla foto come caricata: `imgRaw`, `imgOriginal` e `lastEdit` vivono **solo in memoria** (per non raddoppiare lo spazio salvato); dopo un ricaricamento si riparte dalla foto ritoccata. Il caricamento di una foto **non** applica correzioni automatiche di prospettiva/ritaglio: è stato provato e tolto perché sbagliava troppo spesso; le correzioni restano strumenti manuali.

### Lettura e disegno dei PDF (`Pdf`)

Il modulo `Pdf` (una IIFE dentro la IIFE) non usa librerie. Espone `open(buffer)` → `{doc, pages}`, `probe(doc, page)` → glifi con posizione in punti PDF (origine in basso a sinistra), immagini piazzate e riquadri grandi, e `render(doc, page, rect, scale)` → un canvas con quel rettangolo della pagina.

- `Doc` indicizza gli oggetti **scorrendo il file** alla ricerca di `n g obj` (saltando il contenuto degli stream) invece di fidarsi della tabella xref; espande gli object stream; la radice viene dall'ultimo `/Root`. Filtri: Flate (con `DecompressionStream`, tollerando dati in coda) con predittori PNG, ASCIIHex, ASCII85; i flussi decodificati sono in cache.
- Font: semplici (WinAnsi/MacRoman/Standard + `/Differences` con i nomi dei glifi) e Type0 Identity-H, con `ToUnicode` quando c'è; larghezze da `/Widths` o `/W`.
- `runPage(doc, page, dev)` è **un solo interprete dei content stream per due usi**: il "device" decide cosa farne. `probeDevice` raccoglie glifi, immagini e riquadri; `canvasDevice` disegna tracciati, colori (Gray/RGB/CMYK/ICCBased/Indexed/Separation/DeviceN/Lab, con le funzioni di tipo 0/2/3/4), trasparenza e metodi di fusione dell'ExtGState, sfumature assiali/radiali, immagini (JPEG tramite `createImageBitmap`, raw con SMask), Form XObject (i gruppi di trasparenza con opacità si compongono in un canvas a parte) e livelli spenti (optional content). Il testo si disegna con **font di sistema somiglianti** (`fontCss`, scelti dal nome del font) adattando la larghezza al font originale: i font incorporati non si possono caricare così come sono. Non sono supportati pattern a tassello, soft mask e immagini inline.

### Dal PDF al caso (`readCaseFromGlyphs`, `findBoardRect`, `importCasePdf`)

I fogli di Murdoku hanno sempre la stessa impaginazione (nome sotto la foto segnaletica, indizio nella nuvoletta sotto il nome) e la lettura lavora sulle posizioni:

1. Glifi (senza il testo ruotato, che sui fogli è decorazione: le scritte verticali tipo "VISITORS" accanto alle foto) → pezzi di testo nell'ordine di disegno → righe (pezzi sulla stessa linea di base, con uno stacco < 0,45 del corpo: uno stacco maggiore è un'altra nuvoletta).
2. **Testo coperto**: nei modelli restano testi di prova nascosti sotto riquadri disegnati dopo (un vecchio nome sotto un altro, un "Lorem ipsum"). Due testi non si sovrappongono mai in un foglio vero: se succede vince quello disegnato per ultimo.
3. Vittima dalla frase "*X was murdered!*"; il **font dei nomi** è quello in cui compare da solo il nome della vittima (in mancanza, il font con più righe di una sola parola con l'iniziale maiuscola).
4. Per ogni nome, le righe sotto di lui nella sua colonna, una dopo l'altra finché non c'è uno stacco: sono l'indizio.
5. **Controllo di validità**: almeno il 60% dei nomi deve avere sotto una frase che parla di una persona (he/she/his/her/him/victim). È ciò che fa rifiutare i PDF che non sono Murdoku (nomi degli autori di un articolo, titoli di sezione con il testo sotto…).
6. Indizi generali: blocchi di testo non assegnati nella zona dei sospettati, che siano frasi (almeno tre parole, con minuscole: le scritte sulla mappa sono maiuscole); titolo: la riga sopra "difficulty: …".

`findBoardRect` trova la tavola: la più grande immagine quasi quadrata (sui fogli originali è il fondo della mappa, dentro la cornice; in mancanza il più grande riquadro quadrato disegnato), mai dove ci sono i nomi; la allarga alla cornice e alle etichette che sporgono dalle caselle. Restituisce il ritaglio da disegnare e l'area delle caselle (`inner`).

`readCaseFromGlyphs` restituisce sempre un oggetto: su una pagina senza sospettati `suspects` è vuoto, ma titolo e `victimName` restano, perché un caso può stare su più pagine. `importCasePdf(file, withBoard)` legge tutte le pagine (fino a `PDF_MAX_PAGES`), unisce i sospettati senza doppioni (`mergeCases`), cerca la tavola prima sulle pagine con i sospettati e poi sulle altre, la disegna (`PDF_BOARD_SIDE` = 1400 px, 80 px per persona fino a 2400 nelle griglie grandi, JPEG) e la passa a `setPhoto()` con `detectExpect`/`detectNote`. Se nessuna pagina ha sospettati leggibili ma il PDF parla di un omicidio ("murder"/"Murdoku"), si carica almeno la tavola (`toastPdfBoardOnly`): un foglio impaginato in modo nuovo resta giocabile. `applyCaseInfo()` assegna le lettere (vittima → `v`, gli altri in ordine alfabetico su `validLetters`), sostituisce nomi, indizi, indizi generali (numerati se più d'uno) e titolo, accende i ritratti e porta la griglia a `n×n` (chiedendo conferma se la griglia non è vuota). Se insieme al PDF arriva una foto, la tavola non viene disegnata (`withBoard = false`): vince la foto dell'utente.

### Undo/redo

Stack di snapshot (`history`/`historyIndex`) aggiornato da `pushHistory()` dopo ogni modifica. Gli snapshot contengono solo `{rows, cols, gridBox, cells}` — non la foto né i metadati — e vivono solo in memoria: lo storico si perde di proposito al ricaricamento.

### Persistenza a sessione unica, legata alla tab

`STORAGE_KEY = 'murdoku_state_v1'` in **sessionStorage**, non `localStorage`. Un solo stato corrente `{rows, cols, gridBox, cells, img, puzzleTitle, suspectNames, suspectClues, generalClues, showPortraits, customColors, timerElapsedMs, timerRunning}`, autosalvato da `persist()` a ogni `pushHistory()`/undo/redo/azione sul cronometro/modifica di un testo. Se il salvataggio fallisce (di solito per spazio esaurito, con foto molto grandi) compare il toast "Storage full". Per lavorare su un altro puzzle si usa **Esporta** (JSON con tutto tranne cronometro e history) / **Importa** (sovrascrive dopo conferma e azzera il cronometro). `normalizeGridDims()` traduce il vecchio formato quadrato (`n`), `normalizeCells()` riempie i campi introdotti dopo (`mark`, `xBy`) e `normalizeGridBox()` i campi del riquadro: sono i punti giusti per future migrazioni di formato.

**`sessionStorage` è la scelta che fa funzionare "F5 continua, apertura nuova parte vuota" senza codice apposta**: `init()` chiama semplicemente `loadStoredData() || fresh`. `sessionStorage` è legato al singolo browsing context, sopravvive a un F5 ma è vuoto su qualunque nuova apertura (nuova tab, doppio clic sul file), anche verso lo stesso file. Risolve anche il caso limite che con `localStorage` non si può risolvere: una tab appena aperta e mai toccata che riceve F5 deve *restare* vuota, non riesumare la sessione di un'altra tab — con un unico slot condiviso per l'origine e un flag "è un reload?" questo non è distinguibile. Non reintrodurre `localStorage` per l'autosave principale senza risolvere anche questo caso. In `localStorage` stanno solo preferenze del dispositivo: lingua (`murdoku_lang_v1`), vista del pannello dei sospettati (`murdoku_view_v1`) e larghezza del pannello per vista (`murdoku_left_w_v1`, `murdoku_left_w_list_v1`).

### Cronometro e chiusura del caso

`timerElapsedMs` è sempre il totale "congelato"; mentre il cronometro è in marcia il tempo trascorso si calcola con `Date.now() - timerStartedAt` (`currentTimerElapsedMs()`) e si somma solo al prossimo stop/persist: niente drift da `setInterval` in background. `toggleTimer()` è l'azione manuale, `ensureTimerRunning()` la controparte idempotente richiamata dalle modifiche alla griglia (e da undo/redo). Al caricamento, se era in marcia riparte da dove si trovava; mentre è in marcia un secondo intervallo chiama `persist()` ogni 30 s.

"Chiudi il caso" (`closeCase()`) si abilita quando tutte le lettere sono decise: ferma il cronometro e mostra timbro, sospettati e tempo, con coriandoli. Non conosce la soluzione e non verifica nulla.

### Interfaccia bilingue (IT/EN)

Tutte le stringhe rivolte all'utente (etichette, toast, `confirm()`/`prompt()`, titoli, finestra di aiuto) passano per `STRINGS.en`/`STRINGS.it` e `t(key, params)` (placeholder `{nome}`): una stringa nuova va aggiunta in entrambe le lingue. Il markup statico usa `data-i18n` (testo), `data-i18n-title` e `data-i18n-placeholder`, applicati da `applyTranslations()`, che sovrascrive il `textContent`: non mettere `data-i18n` su un elemento con figli da preservare (va sullo `<span>` interno). La lingua è una preferenza in `localStorage` (`murdoku_lang_v1`), separata dallo stato del puzzle; il default è **l'inglese** e il markup statico è scritto in inglese per non far lampeggiare etichette sbagliate al primo paint. Il menu `#langSelect` chiama `setLang()`, che rigenera anche i contenuti dinamici (card, simboli, cronometro, aiuto, riga dello strumento).

### Animazioni

Le animazioni "una tantum" delle celle si accodano con `queueAnim()` da chi modifica lo stato e `renderCells()` le applica dopo aver aggiornato il DOM (`flushAnims`): undo/redo/caricamenti non accodano nulla, quindi non animano. Con `prefers-reduced-motion` (`REDUCED_MOTION`) le animazioni decorative si spengono, ma i riempimenti dei gesti "tieni premuto" (gomma, pressione prolungata sulla casella) restano, perché mostrano quanto manca all'azione.

### Ridimensionamento dell'immagine di sfondo

`updateImageSize()` calcola lo spazio disponibile in `#stage` (in pixel, leggendo le padding computate) e imposta `width`/`height` espliciti su `#bgImage` in scala "contain": rimpicciolisce le immagini grandi ma *ingrandisce* anche quelle piccole. Le dimensioni intrinseche arrivano solo dopo il `load`, quindi `applyImage()` la chiama subito (con un fallback a solo `max-width`/`max-height`) e il listener `load` la richiama, seguito da `renderGridBox()`. Due trappole già incontrate: (1) con solo `max-width`/`max-height` un'immagine piccola non viene mai ingrandita; (2) il ramo "dimensioni note" deve azzerare (`'none'`) i `max-*` lasciati dal fallback, altrimenti l'immagine resta inchiodata ai valori minimi calcolati prima del `load` (quando `#stage` non ha ancora layout valgono 120 px).
