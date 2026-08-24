# Dal Lordo al Netto — Simulatore RAL 2026

Prototipo per l'esercizio Product Builder @ Jet HR.

## Cosa fa

Prende in input una RAL (retribuzione annua lorda) e restituisce netto mensile, netto annuo, e il dettaglio di ogni voce trattenuta lungo il percorso: contributi INPS, IRPEF (dopo detrazioni), addizionale regionale Lombardia, addizionale comunale Milano, più le misure che aumentano il netto (trattamento integrativo, somma esente cuneo fiscale).

Supporta anche figli a carico: si inserisce l'età di ciascun figlio e il calcolatore li classifica automaticamente in una di quattro fasce, ciascuna con regole diverse:

- **under 18** → Assegno Unico Universale (AUU), automatico
- **18-20 anni** → AUU, ma solo se il figlio soddisfa un requisito specifico (studio/formazione, tirocinio o lavoro <8.000€/anno, disoccupazione registrata, servizio civile) — il calcolatore segnala la condizione, non può verificarla
- **21-29 anni** → detrazione IRPEF (art. 12 TUIR), inclusa nel calcolo del netto in busta paga
- **30+ anni** → nessuna delle due misure (salvo disabilità, non modellata)

Punto importante: l'AUU è erogato **separatamente da INPS**, non attraverso il datore di lavoro, quindi è mostrato come nota informativa distinta e non entra nel netto calcolato. Solo la detrazione per i figli 21-29 modifica davvero il netto in busta paga.

Ogni riga del risultato è espandibile nella sezione "Mostra le formule usate": non solo il numero finale, ma la formula con i valori reali del calcolo appena eseguito.

## Assunzioni fisse (come da consegna)

- Dipendente a tempo indeterminato
- Residente a Milano
- Nessuna agevolazione particolare oltre a quelle per figli a carico modellate
- Anno intero lavorato (365/365)
- Detrazione figli 21-29 anni attribuita al 100% a questo genitore (nessuna ripartizione 50/50)
- Reddito proprio di ogni figlio assunto sotto soglia (non richiesto in input)
- Nessun figlio con disabilità modellato

## Semplificazioni deliberate

Elencate per intero nella pagina stessa, sezione "Semplificazioni applicate". Le principali:

- Il "reddito complessivo" usato per le soglie di legge è approssimato con la sola RAL
- Le addizionali regionale/comunale sono calcolate sull'anno corrente (nella realtà sono trattenute con uno sfasamento di un anno)
- Non modellati: welfare aziendale, straordinari, part-time, altre detrazioni personali, massimale contributivo oltre 122.295€
- Un dato reperito in fonti secondarie discordanti (detrazione "comma 1-bis") è stato omesso di proposito invece di essere incluso con un valore incerto
- Gli importi Assegno Unico per la fascia 18-20 anni sono taggati come NON VERIFICATI su fonte primaria diretta: fonti secondarie riportano cifre leggermente discordanti per questa fascia specifica

## Metodo di verifica dei dati

Tutti i parametri fiscali (scaglioni IRPEF, aliquote INPS, addizionali) sono stati verificati via ricerca web per l'anno fiscale 2026, non presi a memoria. Ogni voce nel calcolatore è taggata:

- **verificato** → fonte primaria consultata direttamente (regione.lombardia.it, comune.milano.it, inps.it)
- **fonti concordanti** → fonti secondarie multiple e coerenti tra loro, nessuna fonte primaria unica letta per intero in sessione
- **NON VERIFICATO** → fonti secondarie discordanti tra loro, nessuna fonte primaria consultata direttamente (usato per gli importi AUU fascia 18-20 anni)

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

**Nota**: questa validazione incrociata copre il caso base (nessun figlio a carico). La funzionalità figli a carico, aggiunta successivamente, è stata verificata via ricerca web e testata su casi manuali (es. figli di 12/20/24 anni), ma non ancora confrontata con calcolatori esterni con figli a carico — prossimo passo se servisse rafforzare la validazione.

## Stack

HTML/CSS/JS vanilla, nessuna dipendenza esterna eccetto due font da Google Fonts. Un solo file, apribile ovunque senza build.

## Limiti dichiarati

Questo è un prototipo dimostrativo, non un sostituto di un cedolino reale o di una consulenza di un consulente del lavoro. Lo scopo non è la copertura di tutti i casi, ma dimostrare comprensione e controllo della logica di calcolo su un caso standard.
