# Murdoku Assistant — Manuale d'uso

*Read this in [English](README.md).*

Murdoku Assistant è un foglio elettronico digitale per risolvere i puzzle **Murdoku** (murdoku.com): sostituisce carta e penna quando si segnano indizi e decisioni su una griglia. Non fa nulla "per te": non risolve il puzzle, non dà suggerimenti — tiene traccia in modo ordinato di quello che scrivi.

È un'unica pagina HTML autosufficiente: nessuna installazione, nessun account, nessun server. Basta aprire `index.html` in un browser da PC.

## Indice

1. [Cos'è un Murdoku](#1-cosè-un-murdoku)
2. [Avvio](#2-avvio)
3. [Panoramica dell'interfaccia](#3-panoramica-dellinterfaccia)
4. [Impostare la griglia](#4-impostare-la-griglia)
5. [Come si gioca](#5-come-si-gioca)
6. [Scorciatoie da tastiera](#6-scorciatoie-da-tastiera)
7. [Undo/Redo](#7-undoredo)
8. [Evidenziazioni](#8-evidenziazioni)
9. [Cronometro](#9-cronometro)
10. [Esporta/Importa](#10-esportaimporta)
11. [Salvataggio automatico e suoi limiti](#11-salvataggio-automatico-e-suoi-limiti)
12. [Cambio lingua](#12-cambio-lingua)
13. [Griglie rettangolari](#13-griglie-rettangolari)
14. [Domande frequenti](#14-domande-frequenti)

## 1. Cos'è un Murdoku

Un Murdoku è un puzzle logico-deduttivo: su una griglia N×N (o rettangolare), ogni riga e ogni colonna nasconde un'unica lettera "assassino" — esattamente come le torri non attaccanti su una scacchiera, dove una volta piazzata una torre nessun'altra può condividere la sua riga o la sua colonna. Attraverso indizi e deduzioni si restringe il campo finché, per ogni lettera, resta una sola casella possibile: quella diventa la **decisione**.

## 2. Avvio

Apri il file `index.html` con un doppio click, oppure con "Apri con" → il tuo browser preferito. Non serve installare nulla, non serve connessione a internet dopo il primo caricamento (a meno che tu non usi font/risorse esterne — questa app non ne usa).

L'app è pensata per l'uso da PC con mouse e tastiera: non c'è un percorso di interazione dedicato al touch.

## 3. Panoramica dell'interfaccia

Il layout ricalca quello del Murdoku originale: una barra in alto e tre colonne.

- **Barra in alto**: il nome del puzzle (un campo di testo libero che puoi compilare), un badge con le dimensioni della griglia e il numero di sospettati, il cronometro con avvio/pausa e azzeramento, il cambio lingua e il **?** che apre la finestra "Come si gioca".
- **Pannello sinistro — Sospettati**: una card per ogni lettera valida, con token colorato (la lettera), campo nome facoltativo e spunta verde ✓ quando quella lettera è già stata piazzata come decisione. La vittima (`V`) ha una card rossa dedicata. Sotto: l'interruttore **indizio / decisione**, una barra di avanzamento con le lettere ancora da piazzare e i quattro **simboli nota**.
- **Centro — il tavolo**: l'immagine di sfondo (facoltativa) con la griglia sovrapposta, più le etichette `R1…Rn` / `C1…Cn` lungo i bordi del riquadro. L'etichetta della riga e della colonna della casella selezionata è evidenziata, e l'intera riga/colonna è tinta leggermente: così il vincolo torre si legge a colpo d'occhio.
- **Pannello destro — Strumenti**: il grande bottone **✕** (strumento di esclusione), **↖ Solo selezione**, "tieni premuto per evidenziare le caselle vuote", undo/redo, le impostazioni della griglia (righe, colonne, blocca/sblocca), l'immagine di sfondo (carica, **rileva griglia**, rimuovi) e i comandi di sessione (esporta, importa, cancella tutto, come si gioca).

Nella barra in alto una targhetta mostra sempre **cosa scriverà il prossimo clic** ("D · Donna — DECISIONE: clic per piazzare"), e passando sopra una casella ne vedi l'anteprima al suo posto — vale la pena dargli un'occhiata prima di cliccare, perché una decisione non si toglie se non con l'Undo.

**I nomi dei sospettati** sono facoltativi e servono solo a te: in un Murdoku vero le iniziali dei sospettati sono in ordine alfabetico (August, Barnaby, Clarence…) e il nome della vittima inizia per V, quindi puoi scrivere nelle card i nomi stampati sul tuo puzzle e leggere la griglia in termini di persone invece che di lettere. I nomi vengono salvati ed esportati insieme al puzzle; non influenzano mai le regole.

## 4. Impostare la griglia

- **Righe/Colonne**: due campi numerici nel pannello Strumenti a destra, da 2 a 22 ciascuno. Non devono essere uguali: la griglia può essere rettangolare. Cambiare uno dei due valori, dopo conferma, azzera il contenuto della griglia (indizi, decisioni, esclusioni) — non le dimensioni/posizione del riquadro.
- **Immagine di sfondo** (facoltativa): "Carica immagine" per caricare una foto del puzzle su cui disegnare sopra la griglia; "Rimuovi immagine" per toglierla senza toccare la griglia. Senza immagine, l'app mostra semplicemente una griglia vuota su sfondo neutro.
- **Riconoscimento automatico**: appena carichi una foto, l'app prova a ricavare da sola **le dimensioni della griglia e la posizione del riquadro**, e le applica. Se la cava anche con una foto da telefono un po' storta, sfocata o con luce sbilanciata, e con margini di pagina larghi. Quando non è sicura non applica nulla e te lo dice — meglio nessuna risposta che una sbagliata, visto che applicarla azzererebbe la griglia. Il bottone **🔍 Rileva griglia** rilancia lo stesso riconoscimento a mano (utile dopo aver raddrizzato o ritagliato di nuovo la foto).
- **Posizionare/ridimensionare il riquadro griglia**: quando la griglia è **sbloccata** (vedi sotto), puoi trascinare il corpo del riquadro giallo per spostarlo sopra l'immagine, oppure trascinare uno dei quattro angoli per ridimensionarlo, così da farlo coincidere con la griglia reale disegnata nella foto.
- **Blocca/Sblocca** (bottone 🔒 **Griglia bloccata** / 🔓 **Griglia sbloccata** nel pannello Strumenti): quando è **sbloccata**, puoi spostare/ridimensionare il riquadro ma non modificare le celle, e un banner in cima al tavolo te lo ricorda. Quando è **bloccata**, il riquadro è fisso e puoi cliccare/navigare tra le celle per compilarle. Serve a evitare di spostare accidentalmente la griglia mentre stai giocando.

## 5. Come si gioca

Puoi lavorare in due modi, del tutto equivalenti: **cliccare** una casella con uno strumento selezionato, oppure muovere la selezione con le **frecce** e digitare. In entrambi i casi la griglia deve essere bloccata.

- **Lettera minuscola → indizio**: annota che quella lettera è *possibile* in quella casella. Più indizi possono coesistere nella stessa casella (es. "potrebbe essere A o C"). Un indizio non è più possibile per una lettera già decisa altrove nella griglia.
- **Shift+lettera (maiuscola) → decisione**: dichiara che quella lettera è *quella* casella, in modo definitivo. È permesso solo su una casella vuota (senza esclusione X e senza già una decisione) e solo se quella lettera non è già stata decisa altrove. Appena piazzata:
  - tutti gli indizi di quella lettera vengono rimossi da ogni altra casella della griglia;
  - ogni altra casella della stessa riga e della stessa colonna viene automaticamente **esclusa (X)** — il "vincolo torre": una lettera per riga, una per colonna.
  - **Non c'è modo di rimuovere una decisione con un click**: l'unico modo per tornare indietro è l'**Undo**.
- **`X` → esclusione**: dichiara che nessuna lettera può stare in quella casella. Si attiva/disattiva liberamente (tasto `x`, funziona sia minuscolo che maiuscolo) finché la casella non contiene già una decisione. Impostare `X` su una casella ne svuota anche gli indizi.
- Se una casella ha già una `X`, non puoi scriverci né un indizio né una decisione: puoi solo togliere la `X` premendo di nuovo `x`.
- **Simboli nota (tasti `1`–`4`) → annotazione libera**: ▲ ● ■ ★ puoi usarli come preferisci (per esempio "controllata", "impossibile per due motivi", "da rivedere"). Non significano nulla per le regole, uno solo per casella, e ripremendo lo stesso simbolo lo togli. Non si possono mettere su una casella con `X` o con una decisione.

Decisioni, indizi e simboli nota sono disegnati con il colore del sospettato, lo stesso mostrato sulla sua card: così riconosci una lettera sulla griglia senza doverla leggere.

**Inserire con il mouse**: scegli uno strumento — una card sospettato, il bottone ✕, un simbolo nota — e poi clicca una casella: il clic la compila subito. Cliccando di nuovo la stessa casella togli l'indizio, la `X` o il simbolo. Se invece vuoi solo muoverti nella griglia senza scrivere, attiva **↖ Solo selezione**: con quello attivo il clic si limita a spostare la selezione.

**Quali lettere sono disponibili?** Il numero massimo di decisioni piazzabili in tutta la griglia è `min(righe, colonne)` (il vincolo torre esaurisce prima la dimensione più corta). Le lettere valide sono le prime `min(righe,colonne)-1` lettere dell'alfabeto (a, b, c, ...) più una `v` fissa come ultima lettera; `w/x/y/z` non sono mai lettere di gioco — `x` resta libera per lo strumento di esclusione. Su una griglia rettangolare, alcune caselle resteranno necessariamente senza decisione anche a puzzle risolto: è corretto e atteso.

## 6. Scorciatoie da tastiera

| Tasto | Effetto |
|---|---|
| Frecce | Sposta la casella selezionata |
| Lettera minuscola | Indizio nella casella selezionata |
| Shift + lettera | Decisione nella casella selezionata |
| `x` | Attiva/disattiva l'esclusione |
| `1` – `4` | Applica/togli un simbolo nota |
| Ctrl+Z | Undo |
| Ctrl+Y (o Ctrl+Shift+Z) | Redo |

Le scorciatoie funzionano solo quando la griglia è **bloccata** e il focus non è su un campo di testo (righe/colonne, nome del puzzle, nomi dei sospettati) — dentro quei campi Ctrl+Z resta l'undo del testo del browser.

## 7. Undo/Redo

Ogni modifica (X, indizio, decisione, spostamento/ridimensionamento del riquadro, cambio dimensioni, cancella tutto) viene salvata come istantanea nella cronologia. I bottoni **↶ Undo** / **↷ Redo** nel pannello Strumenti (o Ctrl+Z / Ctrl+Y) scorrono avanti e indietro tra queste istantanee. Il nome del puzzle e i nomi dei sospettati sono volutamente esclusi dalla cronologia: scrivere un nome non crea mai un passo di undo.

La cronologia vive solo in memoria: **si perde ricaricando la pagina** (a differenza dello stato della griglia, vedi sezione successiva). "Cancella tutto" azzera anche la cronologia: dopo, non è più possibile tornare a prima della cancellazione.

## 8. Evidenziazioni

Due bottoni funzionano "tieni premuto": mostrano un'evidenziazione temporanea sulla griglia solo mentre il tasto del mouse resta premuto, e la rimuovono al rilascio (anche se il rilascio avviene fuori dal bottone).

- **Card dei sospettati** nel pannello di sinistra: tenerne premuta una evidenzia tutte le caselle dove quella lettera è presente come indizio o decisione.
- **Il bottone 👁** nel pannello Strumenti: evidenzia tutte le caselle ancora senza `X` e senza decisione (gli indizi non contano — una casella con soli indizi è ancora considerata "vuota" a questo scopo).

## 9. Cronometro

Misura il tempo di risoluzione. Parte automaticamente alla prima interazione con la griglia (non serve premere "Avvia"). I controlli nella barra in alto:

- **▶ Avvia / ⏸ Pausa**: avvia o metti in pausa manualmente.
- **⟲ Azzera**: azzera il tempo accumulato (senza fermare il cronometro se era in marcia).

Il tempo del cronometro viene salvato insieme allo stato della griglia (vedi sezione 11), ma **non viene incluso** quando esporti su file.

## 10. Esporta/Importa

- **Esporta**: chiede un nome (già precompilato con il nome del puzzle, se lo hai impostato), poi scarica un file `.json` con lo stato completo (dimensioni, immagine, contenuto celle, nome del puzzle, nomi dei sospettati) — non il tempo del cronometro né la cronologia undo. Usalo per salvare un puzzle su cui non stai più lavorando, o per condividerlo.
- **Importa**: carica un file `.json` precedentemente esportato, **sovrascrivendo** (dopo conferma) la sessione di lavoro corrente. Azzera anche il cronometro.

## 11. Salvataggio automatico e suoi limiti

L'app salva automaticamente il lavoro in corso (dimensioni, immagine, contenuto celle, nome del puzzle, nomi dei sospettati, cronometro) mentre lavori, senza bisogno di premere nulla. **Attenzione a un comportamento voluto ma non ovvio**: questo salvataggio è legato alla singola scheda del browser (tecnicamente: `sessionStorage`, non `localStorage`).

- **Premere F5 / ricaricare la pagina nella stessa scheda**: il lavoro resta, esattamente come lo hai lasciato.
- **Aprire una nuova scheda o finestra** (anche sullo stesso identico file): parte da zero, con una griglia vuota — anche se un'altra scheda ha ancora del lavoro in corso.
- **Chiudere la scheda/il browser**: il lavoro va perso, a meno che tu non l'abbia esportato su file con "Esporta" prima di chiudere.

Se vuoi conservare un puzzle a lungo termine, o passare da un puzzle all'altro, usa sempre **Esporta**.

## 12. Cambio lingua

L'interfaccia parte in **inglese**. Il bottone **EN**/**IT** nella barra in alto la alterna tra inglese e italiano; la scelta viene ricordata (in questo browser, su questo computer) e riproposta ad ogni apertura successiva — è indipendente dal salvataggio del puzzle descritto sopra.

## 13. Griglie rettangolari

Righe e colonne possono differire. In questo caso il numero di lettere/decisioni disponibili resta `min(righe, colonne)`: la dimensione più corta si esaurisce prima per il vincolo torre, quindi alcune caselle della dimensione più lunga restano necessariamente senza decisione anche a puzzle completamente risolto. È il comportamento corretto, non un bug.

## 14. Domande frequenti

**Ho chiuso la scheda e ho perso tutto il lavoro. Come lo recupero?**
Non è recuperabile se non l'avevi esportato su file. Vedi la sezione 11: esporta regolarmente se il puzzle richiede più sessioni.

**Ho piazzato una decisione per errore, come la tolgo?**
Non si può togliere con un click. Usa Undo (Ctrl+Z o il bottone nel pannello Strumenti) finché non torni allo stato prima di quella decisione.

**Perché non riesco a scrivere un indizio o una decisione in una casella?**
Controlla se quella casella ha già una `X` (in tal caso puoi solo togliere la `X`), se ha già una decisione, o se quella lettera è già stata decisa altrove nella griglia — in ognuno di questi casi un messaggio in basso spiega il motivo.

**Perché alcune lettere restano sempre "da piazzare" anche a griglia completa?**
Solo su griglie rettangolari: è normale, vedi la sezione 13.
