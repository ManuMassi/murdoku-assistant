# Murdoku Assistant — Manuale d'uso

*Read this in [English](MANUAL.md).*

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

L'app è divisa in tre zone:

- **Header (in alto)**: dimensioni della griglia, immagine di sfondo, cancella tutto, blocca/sblocca, undo/redo, esporta/importa, cambio lingua.
- **Area centrale**: l'immagine di sfondo (facoltativa) con la griglia sovrapposta.
- **Barra in basso**: bottoni lettera (uno per ogni lettera valida), modalità indizio/decisione, evidenzia caselle vuote, cronometro, stato delle lettere ancora da piazzare.

## 4. Impostare la griglia

- **Righe/Colonne**: due campi numerici in header, da 2 a 22 ciascuno. Non devono essere uguali: la griglia può essere rettangolare. Cambiare uno dei due valori, dopo conferma, azzera il contenuto della griglia (indizi, decisioni, esclusioni) — non le dimensioni/posizione del riquadro.
- **Immagine di sfondo** (facoltativa): "Carica immagine" per caricare una foto del puzzle su cui disegnare sopra la griglia; "Rimuovi immagine" per toglierla senza toccare la griglia. Senza immagine, l'app mostra semplicemente una griglia vuota su sfondo neutro.
- **Posizionare/ridimensionare il riquadro griglia**: quando la griglia è **sbloccata** (vedi sotto), puoi trascinare il corpo del riquadro azzurro per spostarlo sopra l'immagine, oppure trascinare uno dei quattro angoli per ridimensionarlo, così da farlo coincidere con la griglia reale disegnata nella foto.
- **Blocca/Sblocca** (bottone 🔒 in header): quando è **sbloccata**, puoi spostare/ridimensionare il riquadro ma non modificare le celle. Quando è **bloccata**, il riquadro è fisso e puoi cliccare/navigare tra le celle per compilarle. Serve a evitare di spostare accidentalmente la griglia mentre stai giocando.

## 5. Come si gioca

Tutto l'inserimento avviene **da tastiera**: il click su una casella serve solo a selezionarla (la griglia deve essere bloccata). Usa le **frecce** per muovere la selezione, poi digita una lettera.

- **Lettera minuscola → indizio**: annota che quella lettera è *possibile* in quella casella. Più indizi possono coesistere nella stessa casella (es. "potrebbe essere A o C"). Un indizio non è più possibile per una lettera già decisa altrove nella griglia.
- **Shift+lettera (maiuscola) → decisione**: dichiara che quella lettera è *quella* casella, in modo definitivo. È permesso solo su una casella vuota (senza esclusione X e senza già una decisione) e solo se quella lettera non è già stata decisa altrove. Appena piazzata:
  - tutti gli indizi di quella lettera vengono rimossi da ogni altra casella della griglia;
  - ogni altra casella della stessa riga e della stessa colonna viene automaticamente **esclusa (X)** — il "vincolo torre": una lettera per riga, una per colonna.
  - **Non c'è modo di rimuovere una decisione con un click**: l'unico modo per tornare indietro è l'**Undo**.
- **`X` → esclusione**: dichiara che nessuna lettera può stare in quella casella. Si attiva/disattiva liberamente (tasto `x`, funziona sia minuscolo che maiuscolo) finché la casella non contiene già una decisione. Impostare `X` su una casella ne svuota anche gli indizi.
- Se una casella ha già una `X`, non puoi scriverci né un indizio né una decisione: puoi solo togliere la `X` premendo di nuovo `x`.

**Quali lettere sono disponibili?** Il numero massimo di decisioni piazzabili in tutta la griglia è `min(righe, colonne)` (il vincolo torre esaurisce prima la dimensione più corta). Le lettere valide sono le prime `min(righe,colonne)-1` lettere dell'alfabeto (a, b, c, ...) più una `v` fissa come ultima lettera; `w/x/y/z` non sono mai lettere di gioco — `x` resta libera per lo strumento di esclusione. Su una griglia rettangolare, alcune caselle resteranno necessariamente senza decisione anche a puzzle risolto: è corretto e atteso.

## 6. Scorciatoie da tastiera

| Tasto | Effetto |
|---|---|
| Frecce | Sposta la casella selezionata |
| Lettera minuscola | Indizio nella casella selezionata |
| Shift + lettera | Decisione nella casella selezionata |
| `x` | Attiva/disattiva l'esclusione |
| Ctrl+Z | Undo |
| Ctrl+Y (o Ctrl+Shift+Z) | Redo |

Le scorciatoie lettera/X funzionano solo quando la griglia è **bloccata** e il focus non è su un campo di testo (es. i campi righe/colonne).

## 7. Undo/Redo

Ogni modifica (X, indizio, decisione, spostamento/ridimensionamento del riquadro, cambio dimensioni, cancella tutto) viene salvata come istantanea nella cronologia. I bottoni **↶ Undo** / **↷ Redo** in header (o Ctrl+Z / Ctrl+Y) scorrono avanti e indietro tra queste istantanee.

La cronologia vive solo in memoria: **si perde ricaricando la pagina** (a differenza dello stato della griglia, vedi sezione successiva). "Cancella tutto" azzera anche la cronologia: dopo, non è più possibile tornare a prima della cancellazione.

## 8. Evidenziazioni

Due bottoni funzionano "tieni premuto": mostrano un'evidenziazione temporanea sulla griglia solo mentre il tasto del mouse resta premuto, e la rimuovono al rilascio (anche se il rilascio avviene fuori dal bottone).

- **Bottoni lettera** nella barra in basso: tenerne premuto uno evidenzia tutte le caselle dove quella lettera è presente come indizio o decisione.
- **"👁 Evidenzia vuote"**: evidenzia tutte le caselle ancora senza `X` e senza decisione (gli indizi non contano — una casella con soli indizi è ancora considerata "vuota" a questo scopo).

## 9. Cronometro

Misura il tempo di risoluzione. Parte automaticamente alla prima interazione con la griglia (non serve premere "Avvia"). I controlli nella barra in basso:

- **▶ Avvia / ⏸ Pausa**: avvia o metti in pausa manualmente.
- **⟲ Azzera**: azzera il tempo accumulato (senza fermare il cronometro se era in marcia).

Il tempo del cronometro viene salvato insieme allo stato della griglia (vedi sezione 11), ma **non viene incluso** quando esporti su file.

## 10. Esporta/Importa

- **Esporta**: chiede un nome, poi scarica un file `.json` con lo stato completo della griglia (dimensioni, immagine, contenuto celle) — non il tempo del cronometro né la cronologia undo. Usalo per salvare un puzzle su cui non stai più lavorando, o per condividerlo.
- **Importa**: carica un file `.json` precedentemente esportato, **sovrascrivendo** (dopo conferma) la sessione di lavoro corrente. Azzera anche il cronometro.

## 11. Salvataggio automatico e suoi limiti

L'app salva automaticamente il lavoro in corso (dimensioni, immagine, contenuto celle, cronometro) mentre lavori, senza bisogno di premere nulla. **Attenzione a un comportamento voluto ma non ovvio**: questo salvataggio è legato alla singola scheda del browser (tecnicamente: `sessionStorage`, non `localStorage`).

- **Premere F5 / ricaricare la pagina nella stessa scheda**: il lavoro resta, esattamente come lo hai lasciato.
- **Aprire una nuova scheda o finestra** (anche sullo stesso identico file): parte da zero, con una griglia vuota — anche se un'altra scheda ha ancora del lavoro in corso.
- **Chiudere la scheda/il browser**: il lavoro va perso, a meno che tu non l'abbia esportato su file con "Esporta" prima di chiudere.

Se vuoi conservare un puzzle a lungo termine, o passare da un puzzle all'altro, usa sempre **Esporta**.

## 12. Cambio lingua

Il bottone 🌐 in header alterna l'interfaccia tra italiano e inglese. La scelta viene ricordata (in questo browser, su questo computer) e riproposta ad ogni apertura successiva — è indipendente dal salvataggio del puzzle descritto sopra.

## 13. Griglie rettangolari

Righe e colonne possono differire. In questo caso il numero di lettere/decisioni disponibili resta `min(righe, colonne)`: la dimensione più corta si esaurisce prima per il vincolo torre, quindi alcune caselle della dimensione più lunga restano necessariamente senza decisione anche a puzzle completamente risolto. È il comportamento corretto, non un bug.

## 14. Domande frequenti

**Ho chiuso la scheda e ho perso tutto il lavoro. Come lo recupero?**
Non è recuperabile se non l'avevi esportato su file. Vedi la sezione 11: esporta regolarmente se il puzzle richiede più sessioni.

**Ho piazzato una decisione per errore, come la tolgo?**
Non si può togliere con un click. Usa Undo (Ctrl+Z o il bottone in header) finché non torni allo stato prima di quella decisione.

**Perché non riesco a scrivere un indizio o una decisione in una casella?**
Controlla se quella casella ha già una `X` (in tal caso puoi solo togliere la `X`), se ha già una decisione, o se quella lettera è già stata decisa altrove nella griglia — in ognuno di questi casi un messaggio in basso spiega il motivo.

**Perché alcune lettere restano sempre "da piazzare" anche a griglia completa?**
Solo su griglie rettangolari: è normale, vedi la sezione 13.
