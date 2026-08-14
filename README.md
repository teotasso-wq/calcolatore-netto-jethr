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

| Fonte | Netto mensile | Nota |
|---|---|---|
| Questo calcolatore | 2.002€ | — |
| aPensione.it | 2.007€ | applica la maggiorazione +65€ (comma 1-bis), come questo calcolatore |
| stipendionettocalcolatore.it | ~1.997€ | mostra il procedimento completo; non applica la maggiorazione +65€ |
| Coverflex (coverflex.com) | 1.990€ | INPS identico; IRPEF e addizionali unite in una sola voce; non applica la maggiorazione +65€ |

Le fonti si dividono in due gruppi coerenti: chi applica la maggiorazione +65€ dell'art. 13 TUIR comma 1-bis (~2.002-2.007€) e chi non la applica (~1.990-1.997€).

Il confronto ha prodotto tre risultati concreti:

1. **Un bug reale trovato e corretto.** La detrazione lavoro dipendente e il cuneo fiscale devono essere calcolati sull'imponibile fiscale (RAL − INPS), non sulla RAL lorda: è quella la base del "reddito complessivo" previsto dalla norma. In una versione precedente usavo la RAL, sovrastimando l'IRPEF.
2. **Un dato prima escluso, poi reintrodotto.** La maggiorazione +65€ (fascia imponibile 25.000-35.000€) era stata omessa per fonti secondarie discordanti; è stata reinserita dopo che due fonti indipendenti hanno confermato lo stesso importo esatto.
3. **Un disaccordo verificato e risolto a favore di questo calcolatore.** L'addizionale regionale Lombardia di aPensione.it corrisponde a un'aliquota piatta dell'1,23% sull'intero imponibile. Questo calcolatore usa invece i quattro scaglioni progressivi (1,23% / 1,58% / 1,72% / 1,73%): su un imponibile di 35.000€ il risultato è 510,30€, cifra confermata al centesimo da una fonte terza indipendente.

## Stack

HTML/CSS/JS vanilla, nessuna dipendenza esterna eccetto due font da Google Fonts. Un solo file, apribile ovunque senza build.

## Limiti dichiarati

Questo è un prototipo dimostrativo, non un sostituto di un cedolino reale o di una consulenza di un consulente del lavoro. Lo scopo non è la copertura di tutti i casi, ma dimostrare comprensione e controllo della logica di calcolo su un caso standard.
