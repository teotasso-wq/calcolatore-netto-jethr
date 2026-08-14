# Dal Lordo al Netto — Simulatore RAL 2026

Prototipo per l'esercizio Product Builder @ Jet HR.

## Cosa fa

Prende in input una RAL (retribuzione annua lorda) e restituisce netto mensile, netto annuo, e il dettaglio di ogni voce trattenuta lungo il percorso: contributi INPS, IRPEF (dopo detrazioni), addizionale regionale Lombardia, addizionale comunale Milano, più le misure che aumentano il netto (trattamento integrativo, somma esente cuneo fiscale).

Ogni riga del risultato è espandibile nella sezione "Mostra le formule usate": non solo il numero finale, ma la formula con i valori reali del calcolo appena eseguito.

## Assunzioni fisse (come da consegna)

- Dipendente a tempo indeterminato
- Residente a Milano
- Nessun carico di famiglia, nessuna agevolazione particolare
- Anno intero lavorato (365/365)

## Semplificazioni deliberate

Elencate per intero nella pagina stessa, sezione "Semplificazioni applicate". Le principali:

- Il "reddito complessivo" usato per le soglie di legge è approssimato con la sola RAL
- Le addizionali regionale/comunale sono calcolate sull'anno corrente (nella realtà sono trattenute con uno sfasamento di un anno)
- Non modellati: welfare aziendale, straordinari, part-time, altre detrazioni personali, massimale contributivo oltre 122.295€
- Un dato reperito in fonti secondarie discordanti (detrazione "comma 1-bis") è stato omesso di proposito invece di essere incluso con un valore incerto

## Metodo di verifica dei dati

Tutti i parametri fiscali (scaglioni IRPEF, aliquote INPS, addizionali) sono stati verificati via ricerca web per l'anno fiscale 2026, non presi a memoria. Ogni voce nel calcolatore è taggata:

- **verificato** → fonte primaria consultata direttamente (regione.lombardia.it, comune.milano.it, inps.it)
- **fonti concordanti** → fonti secondarie multiple e coerenti tra loro, nessuna fonte primaria unica letta per intero in sessione

Le fonti complete sono elencate in fondo alla pagina del calcolatore.

## Validazione incrociata

Prima della consegna, l'output è stato confrontato con tre calcolatori esterni sullo stesso caso (RAL 35.000€, Lombardia/Milano, 13 mensilità):

| Fonte | Netto mensile | Scarto |
|---|---|---|
| Questo calcolatore | 1.997€ | — |
| Coverflex (coverflex.com) | 1.990€ | 0,3% |
| stipendionettocalcolatore.it (mostra il procedimento) | ~1.997€ | ~0% |
| ipendionetto.it | 2.017€ | 1% |

Il confronto con la fonte che mostra il procedimento passo-passo ha fatto emergere un bug reale in una versione precedente: la detrazione lavoro dipendente e il cuneo fiscale devono essere calcolati sull'imponibile fiscale (RAL − INPS), non sulla RAL lorda — è quella la base corretta del "reddito complessivo" previsto dalla norma. Corretto e riverificato dopo la scoperta.

## Stack

HTML/CSS/JS vanilla, nessuna dipendenza esterna eccetto due font da Google Fonts. Un solo file, apribile ovunque senza build.

## Limiti dichiarati

Questo è un prototipo dimostrativo, non un sostituto di un cedolino reale o di una consulenza di un consulente del lavoro. Lo scopo non è la copertura di tutti i casi, ma dimostrare comprensione e controllo della logica di calcolo su un caso standard.
