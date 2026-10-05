# Murdoku Assistant — Manuale d'uso

*Read this in [English](README.md).*

Murdoku Assistant è un foglio di lavoro digitale per risolvere i puzzle **Murdoku** (murdoku.com): sostituisce carta e penna quando si segnano indizi e decisioni sulla scena del crimine. Gli dai il puzzle — il **PDF** o una **foto** — e lui trova la griglia, compila i sospettati con i loro indizi e tiene traccia di tutto quello che scrivi. Non risolve nulla al posto tuo e non dà suggerimenti.

È un'unica pagina HTML autosufficiente: nessuna installazione, nessun account, nessun server, nessuna libreria o font esterno. Basta aprire `index.html` in un browser da PC.

![Murdoku Assistant dopo aver caricato il PDF di "A Walk in the Park": a sinistra i sospettati con indizi e ritratti, al centro la tavola disegnata dal PDF con qualche indizio, alcune X e due decisioni (E e B), a destra gli strumenti](docs/screenshot.png)

*L'app subito dopo aver caricato il PDF di "A Walk in the Park", con qualche indizio e due decisioni piazzate. Puzzle e disegni di Manuel Garand e Valentyna Bezdushna — [murdoku.com](https://murdoku.com).*

## Indice

1. [Cos'è un Murdoku](#1-cosè-un-murdoku)
2. [Avvio](#2-avvio)
3. [Caricare un puzzle](#3-caricare-un-puzzle)
4. [Panoramica dell'interfaccia](#4-panoramica-dellinterfaccia)
5. [Adattare la griglia alla foto](#5-adattare-la-griglia-alla-foto)
6. [Come si gioca](#6-come-si-gioca)
7. [Sospettati, indizi e ritratti](#7-sospettati-indizi-e-ritratti)
8. [Scorciatoie da tastiera](#8-scorciatoie-da-tastiera)
9. [Annulla/Ripeti e la gomma](#9-annullaripeti-e-la-gomma)
10. [Cronometro e chiusura del caso](#10-cronometro-e-chiusura-del-caso)
11. [Esporta/Importa](#11-esportaimporta)
12. [Salvataggio automatico e suoi limiti](#12-salvataggio-automatico-e-suoi-limiti)
13. [Lingua e preferenze](#13-lingua-e-preferenze)
14. [Dimensioni della griglia e lettere](#14-dimensioni-della-griglia-e-lettere)
15. [Domande frequenti](#15-domande-frequenti)

## 1. Cos'è un Murdoku

Un Murdoku è un puzzle logico-deduttivo: qualcuno è stato ucciso, e ogni sospettato si trova su una sola casella della scena del crimine, con **una persona per riga e una per colonna** — esattamente come le torri non attaccanti su una scacchiera. Leggendo l'indizio di ogni sospettato si restringe il campo finché, per ogni persona, resta una sola casella possibile: quella diventa la **decisione**. L'assassino è chi è rimasto solo con la vittima nella stessa area.

## 2. Avvio

Apri `index.html` con un doppio clic, oppure con "Apri con" → il tuo browser preferito. Non serve installare nulla e non serve la connessione: l'app non usa font, script o risorse esterne.

L'app è pensata per l'uso da PC con mouse e tastiera. Usa un browser aggiornato (Chrome, Edge, Firefox o Safari): per leggere i PDF ne serve uno abbastanza recente.

## 3. Caricare un puzzle

Quando la pagina è vuota, una scheda al centro ti invita a caricare il puzzle. Puoi **trascinare un file in qualunque punto della pagina**, oppure usare **CARICA FOTO O PDF** (lo stesso comando è l'icona con l'immagine in fondo al pannello Strumenti).

### Dal PDF (consigliato)

I puzzle stampabili gratuiti di murdoku.com sono PDF. Trascinane uno sulla pagina, oppure premi **LEGGI DAL PDF** sotto il titolo "Sospettati", e in una frazione di secondo l'app:

- **disegna la tavola** direttamente dal PDF, la ritaglia e ne trova la griglia;
- legge **nome e indizio di ogni sospettato**, gli **indizi generali** (numerati come sul foglio) e il **titolo del puzzle**;
- assegna le **lettere**: la vittima (la riga "*X was murdered!*") è sempre **V**, tutti gli altri prendono A, B, C… in ordine alfabetico — sui fogli originali i nomi cominciano già con A, B, C…, quindi lettera e iniziale coincidono;
- accende i **ritratti**, scegliendo un uomo o una donna in base al He/She di ogni indizio;
- porta la griglia a tante righe e colonne quante sono le persone (se ci sono già dei segni, prima te lo chiede).

Da sapere:

- Le scritte sulla tavola (numeri delle buche, nomi delle zone…) sono disegnate con un font di sistema simile, perché i browser non possono caricare i font incorporati nel PDF. Le posizioni sono esatte e il testo resta leggibile.
- Dopo aver disegnato la tavola, l'app cerca solo una griglia della misura giusta (una riga e una colonna per persona). Se non vede bene le linee, mette la griglia dove il PDF dice che stanno le caselle.
- Il lettore si aspetta l'impaginazione dei fogli originali: il nome sotto ogni foto segnaletica e l'indizio nella nuvoletta sotto. Un PDF che non è un foglio di Murdoku dà il messaggio "In questo PDF non ho trovato i sospettati" e non cambia nulla.
- Se trascini **insieme** una foto e il PDF, la tua foto viene usata come tavola e dal PDF arrivano solo i sospettati.

### Da una foto

Trascina una foto (o uno screenshot) della pagina del puzzle. L'app ricava da sola **quante righe e colonne** ha la griglia, **dove si trova**, **quanto è inclinata** (fino a circa ±5°) e perfino righe e colonne di misure un po' diverse dovute alla prospettiva. Se la cava con una foto scattata a mano dal telefono, con luce sbilanciata e con margini di pagina larghi. Se non è sicura non applica nulla e apre la modalità **Allinea griglia** per sistemarla a mano (vedi sezione 5). Nomi e indizi si possono poi scrivere nelle card dei sospettati.

### Griglia vuota

"**oppure inizia su una griglia vuota**" nasconde la scheda e ti fa giocare su una griglia semplice (righe e colonne si impostano in **Impostazioni**).

## 4. Panoramica dell'interfaccia

Il layout ricalca quello del Murdoku originale: sospettati a sinistra, scena del crimine al centro, strumenti a destra.

- **Pannello sinistro — fascicolo e sospettati**
  - il logo e il menu della **lingua**;
  - il **titolo del caso** (modificabile), le dimensioni della griglia e il **cronometro** con il bottone ▶/⏸;
  - **LEGGI DAL PDF**;
  - l'interruttore **INDIZIO / DECISIONE**;
  - una **card per ogni persona**: una grande lettera colorata (o un ritratto, dopo un PDF), il nome e una nuvoletta con l'indizio. La vittima **V** ha un nastro rosso "VITTIMA"; una card riceve il timbro "PIAZZATO" quando quella persona è sulla griglia;
  - una **barra di avanzamento** con quante persone sono piazzate;
  - **INDIZI GENERALI**, un riquadro di testo libero per gli indizi che valgono per tutti;
  - i quattro **simboli nota**.
- **Il divisore** tra il pannello sinistro e la tavola si può trascinare per ingrandire la tavola (doppio clic per tornare com'era). La larghezza viene ricordata.
- **Centro — la scena del crimine**: l'immagine della tavola con la griglia sopra, e le etichette `C1…Cn` / `R1…Rn` lungo i bordi (solo i numeri quando le caselle sono piccole). Riga e colonna della casella selezionata sono evidenziate, e un'etichetta diventa verde quando la sua riga/colonna contiene già una decisione. In basso, una barra mostra sempre **cosa scriverà il prossimo clic** (per esempio "A · Anna — INDIZIO — clic = indizio · tieni premuto = decisione").
- **Pannello destro — Strumenti**: la grande **✕** (esclusione), la **gomma** ("tieni premuto per svuotare tutto"), **ANNULLA / RIPETI**, **SOLO SELEZIONE**, poi **Rileva griglia**, **Allinea griglia**, **Ritaglia e ruota**, **CHIUDI IL CASO**, **COME SI GIOCA**, e quattro icone: carica foto o PDF, esporta, importa, impostazioni.
- **Impostazioni** (icona a ingranaggio): righe e colonne (2–24), rimuovi la foto, azzera il cronometro, e **Inizia un nuovo caso** (cancella tutto).

## 5. Adattare la griglia alla foto

Il riconoscimento è automatico, ma puoi sempre correggerlo.

- **Rileva griglia** rilancia il riconoscimento sulla foto attuale.
- **Allinea griglia** passa alla modalità di allineamento (nel frattempo le caselle non si modificano):
  - trascina il riquadro per spostarlo, un **angolo** per ridimensionarlo, la **manopola rotonda** per ruotarlo;
  - il pannello in alto ha **RIGHE** e **COLONNE** (− / +), **ROTAZIONE** a passi di 0,1° (tenendo premuto si ripete), **UNIFORMA** (rende di nuovo tutte le righe e colonne della stessa misura) e **FATTO**;
  - da tastiera: le frecce spostano il riquadro (Shift per passi più grandi), `[` e `]` lo ruotano, Invio o Esc concludono.
- **Ritaglia e ruota** apre l'editor della foto:
  - *Ritaglia e ruota*: trascina la cornice e le sue maniglie per ritagliare, ruota di 90°, raddrizza con il cursore (±45°, con i bottoni ± per i ritocchi fini), **✨ Auto** raddrizza e ritaglia da solo attorno alla griglia, **Ripristina** riparte da capo;
  - *Prospettiva*: trascina i quattro angoli gialli sugli angoli della tavola (oppure lascia che li metta **✨ Trova angoli**), poi **Raddrizza** — utile per le foto scattate di sbieco;
  - **Foto originale** torna alla foto esattamente com'era stata caricata; **APPLICA** (o Invio) usa il risultato, e la griglia viene ritrovata da capo.

## 6. Come si gioca

**Scegli uno strumento, poi clicca una casella.** Gli strumenti sono: un sospettato (clic sulla sua card, o il tasto della sua lettera), la **✕**, oppure un simbolo nota. L'interruttore **INDIZIO / DECISIONE** decide cosa fa una lettera:

- **Modalità INDIZIO** — clic = **indizio** (una piccola etichetta che vuol dire "questa persona *potrebbe* essere qui"; ce ne possono essere più d'una nella stessa casella). **Tieni premuta una casella per mezzo secondo** per piazzare comunque la persona come **decisione** (mentre tieni premuto si riempie un anello).
- **Modalità DECISIONE** — clic = **decisione**.

Puoi **trascinare sulle caselle** per mettere lo stesso indizio, la stessa ✕ o lo stesso simbolo su tante caselle in un colpo solo (tutto il tratto è un solo passo di Annulla). Il trascinamento non piazza mai decisioni. Cliccando di nuovo una casella togli quell'indizio, quella ✕ o quel simbolo. Con **SOLO SELEZIONE** attivo, il clic sposta soltanto la selezione. Passando sopra una casella vedi un'anteprima trasparente di cosa scriverà il clic.

Le regole che l'app applica per te:

- Una **decisione** mette la lettera della persona, grande, sulla casella. È permessa solo su una casella senza ✕ e senza altre decisioni, e ogni persona si piazza una volta sola. Appena piazzata:
  - tutti gli indizi di quella persona spariscono dal resto della griglia;
  - ogni altra casella della stessa **riga** e della stessa **colonna** riceve una ✕ (la "regola della torre": una persona per riga e per colonna);
  - **una decisione non si toglie con un clic** — solo **Annulla** la riporta indietro.
- La **✕** segna una casella dove non può stare nessuno. Si mette e si toglie liberamente, tranne su una casella con una decisione, e svuota indizi e simbolo della casella. Una casella con la ✕ non accetta altro finché non togli la ✕.
- Non si può scrivere un indizio per una persona già piazzata.
- I **simboli nota** (▲ ● ■ ★, tasti `1`–`4`) sono annotazioni libere — "controllata", "da rivedere"… Non significano nulla per le regole, uno per casella, e non si mettono su una casella con ✕ o con una decisione.

Indizi, decisioni e simboli usano il colore di ogni persona, lo stesso mostrato sulla sua card. **Tenendo premuta la card di un sospettato** si evidenziano tutte le caselle dove compare la sua lettera.

## 7. Sospettati, indizi e ritratti

Ogni card ha un campo per il nome e una nuvoletta per l'indizio in cui puoi scrivere (da un PDF si compilano da soli). Gli indizi lunghi passano a un corpo più piccolo e la nuvoletta si allunga invece di tagliare il testo. Il riquadro **INDIZI GENERALI** sotto le card cresce con il testo.

I ritratti sono **spenti** di default: ogni card mostra la sua lettera grande. Si accendono quando i sospettati arrivano da un PDF, scegliendo un uomo o una donna in base al He/She dell'indizio — se modifichi l'indizio da "She" a "He", cambia anche il ritratto.

Nomi, indizi e titolo fanno parte del caso (vengono salvati ed esportati) ma non influenzano mai le regole, e scriverli non crea mai un passo di Annulla.

## 8. Scorciatoie da tastiera

| Tasto | Effetto |
|---|---|
| Frecce | Sposta la casella selezionata |
| Lettera (`a`…) | Indizio per quella persona nella casella selezionata |
| Shift + lettera | Decisione per quella persona nella casella selezionata |
| `x` | Mette/toglie la ✕ nella casella selezionata |
| `1` – `4` | Mette/toglie un simbolo nota |
| Ctrl/Cmd + Z | Annulla |
| Ctrl/Cmd + Y, oppure Ctrl/Cmd + Shift + Z | Ripeti |
| Esc | Chiude la finestra aperta |
| Durante l'allineamento: frecce / `[` `]` | Sposta il riquadro / lo ruota (Shift = passi più grandi); Invio o Esc concludono |

Le scorciatoie non scattano mentre scrivi in un campo di testo (titolo, nomi, indizi, indizi generali): lì Ctrl+Z è l'annulla del campo stesso.

## 9. Annulla/Ripeti e la gomma

Ogni modifica alla griglia — ✕, indizi, decisioni, simboli, un tratto trascinato, spostamento/ridimensionamento/rotazione del riquadro, cambio di dimensioni, svuotamento — diventa un passo della cronologia. **ANNULLA** e **RIPETI** (o Ctrl+Z / Ctrl+Y) la percorrono. La cronologia vive solo in memoria e **si perde ricaricando la pagina**.

La **gomma** svuota tutte le caselle (✕, indizi, decisioni, simboli) se la **tieni premuta per circa un secondo e mezzo** — un riempimento mostra quanto manca. Foto, posizione della griglia, nomi, indizi e cronometro restano, e **Annulla** riporta indietro le caselle.

## 10. Cronometro e chiusura del caso

Il cronometro parte da solo alla tua prima mossa sulla griglia. Il bottone ▶/⏸ accanto al titolo lo mette in pausa e lo fa ripartire; **Azzera cronometro** è nelle Impostazioni.

Quando tutti sono piazzati, **CHIUDI IL CASO** diventa attivo: ferma il cronometro e mostra il timbro "CASO CHIUSO" con tutti i sospettati e il tuo tempo (e un po' di coriandoli). L'app non conosce la soluzione: chiudere il caso non ti dice se hai indovinato — controlla con la soluzione del puzzle. **CONTINUA A GUARDARE** torna alla griglia.

## 11. Esporta/Importa

- **Esporta** chiede un nome (di default il titolo del caso) e scarica un file `.json` con tutto il caso: dimensioni e posizione della griglia, caselle, foto, titolo, nomi, indizi, indizi generali e ritratti. Il cronometro e la cronologia di Annulla non sono inclusi.
- **Importa** carica un file di questo tipo, **sostituendo** il caso corrente dopo conferma, e azzera il cronometro. Trascinare un file `.json` sulla pagina fa lo stesso.

## 12. Salvataggio automatico e suoi limiti

Il caso in corso viene salvato automaticamente mentre lavori, ma il salvataggio è **legato alla scheda del browser** (tecnicamente `sessionStorage`, non `localStorage`):

- **Ricaricare la pagina (F5) nella stessa scheda**: tutto resta esattamente come lo hai lasciato.
- **Aprire una nuova scheda o finestra**, anche sullo stesso file: parte vuota — anche se un'altra scheda ha ancora del lavoro in corso.
- **Chiudere la scheda o il browser**: il lavoro va perso, a meno che tu non l'abbia esportato.

Il browser concede a questo salvataggio qualche megabyte. Una tavola disegnata da un PDF ci sta tranquillamente; una foto molto grande potrebbe non starci, e l'app te lo dice ("Spazio esaurito"). Per conservare un caso o passare da un caso all'altro, usa **Esporta**.

## 13. Lingua e preferenze

L'interfaccia parte in **inglese**; il menu in cima al pannello sinistro passa all'**italiano**. La lingua e la larghezza del pannello sinistro vengono ricordate in questo browser e sono indipendenti dal salvataggio del caso.

Se il sistema chiede di ridurre le animazioni, quelle decorative vengono spente (resta il riempimento dei gesti "tieni premuto", perché mostra quanto manca).

## 14. Dimensioni della griglia e lettere

Le griglie vanno da 2×2 a **24×24**. Il numero di persone è `min(righe, colonne)`: la vittima è sempre **V** e gli altri prendono le lettere **A, B, C…** in ordine. Si saltano **V** (la vittima) e **X** (il tasto dell'esclusione), quindi dopo la **U** vengono **W** e **Y**: un puzzle da 24 persone usa A…U, W, Y e V.

Righe e colonne possono essere diverse. Su una griglia rettangolare alcune caselle del lato più lungo restano per forza senza decisione anche a puzzle risolto: è normale, non un errore.

## 15. Domande frequenti

**Ho chiuso la scheda e ho perso il lavoro. Si può recuperare?**
No, a meno che tu non l'avessi esportato. Vedi la sezione 12: esporta se un caso richiede più sessioni.

**Ho piazzato una decisione per errore. Come la tolgo?**
Usa Annulla (Ctrl+Z o il bottone) finché non torni a prima di quella decisione. Con un clic non si toglie.

**Perché non riesco a scrivere in una casella?**
Ha una ✕ (toglila prima), contiene già una decisione, oppure quella persona è già stata piazzata altrove. Un messaggio in cima alla tavola dice quale dei casi.

**La griglia non combacia con la foto.**
Usa **Allinea griglia** per spostarla, ridimensionarla e ruotarla a mano, oppure **Ritaglia e ruota** per raddrizzare la foto (la modalità *Prospettiva* sistema le foto scattate di sbieco). Dopo aver applicato, la griglia viene ritrovata da capo.

**Il mio PDF non viene riconosciuto.**
Il lettore si aspetta l'impaginazione dei fogli originali di Murdoku. Puoi comunque usarlo come foto: fai uno screenshot della tavola e trascinalo, poi scrivi nomi e indizi.

**Un sospettato ha un nome che inizia per X.**
La X è riservata al tasto dell'esclusione, quindi quella persona prende la lettera libera successiva (Y). La card mostra comunque il suo nome.

**Perché alcune persone restano "da piazzare" anche a griglia piena?**
Solo sulle griglie rettangolari: vedi la sezione 14.
