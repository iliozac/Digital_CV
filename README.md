# Standard HW e SW di comunicazione seriale "dCV" (digital CV) per sistemi modulari eurorack 

  L'obiettivo è di sviluppare, testare e rendere disponibile in modalità open un nuovo standard di comunicazione per l'interfacciamento e il pilotaggio di moduli eurorack e sintetizzatori analogici stand alone.

  Lo standard MIDI è stato sviluppatonei primi anni '80 per rendere interoperabili tra loro gli strumenti musicali elettronici, partendo dal presupposto che la tecnologia digitale stava ormai monopolizzando la produzione di nuovi strumenti, sia nel controllo che nella generazione dei suoni. Quello che non potevano prevedere è che più avanti nel tempo la tecnologia analogica avrebbe riconquistato un suo spazio e un suo mercato in una forma più organizzata e funzionale, grazie alla nascita dello standard Eurorack ideato da Dieter Doepfer.

  Questa forma di standard ha consentito una vera rivoluzione creativa nel modo di concepire la sintesi musicale e nel modo di progettare e utilizzare l'hardware (ovvero i moduli) per assemblare in modo molto più creativo e personale strumenti per la creazione di suoni. In questo nuovo comparto lo standard MIDI si è però rivelato fortemente limitato se non totalmente incompatibile, nonostante molti siano stati i tentativi di adattarlo e applicarlo nel mondo Eurorack in una sorta di "accanimento terapeutico" che non ha mai preso in considerazione il fatto che il MIDI è nato per risolvere le esigente tecniche di quasi mezzo secolo fa e da allora non è mai stato evoluto.

  Lo studio di uno standard di comunicazione tecnologicamente più evoluto ed espressamente pensato per i sistemi modulari e gli strumenti analogici, è finalizzato a dare un ulteriore impulso progettuale e produttivo al comparto dei moduli Eurorack e in generale al settore della sintesi musicale analogica.
Inoltre questo nuovo sistema è pensato sia per nuovi prodotti, nei quali una porta di comunicazione specifica può essere implementata in fase di progetto, sia per garantire le retrocompatibilità con i moduli classici, controllati tramite ingressi CV, Gate e Sync (tramite l'utilizzo di specifiche interfacce esterne o posizionate all'interno dei case modulari).

### Concetti di base
- rendere possibile la configurazione di strutture di generazione di suono più articolate e dinamiche.
- rendere possibile il controllo di architetture polifoniche/parafoniche.
- semplificare l'interfacciamento con master keyboard, sequencer midi e tools sofware vari.
- velocizzare il remapping delle strutture modulari, riducendo al minimo il patching manuale.
- rendere possibile il richiamo immediato (on stage) di configurazioni pre memorizzate (preset), fondamentali per gli utilizzi "live".
- mantenere la retrocompatibilità con i moduli classici.

## ARCHITETTURA DEL SISTEMA

### Interfaccia Hardware
- tipologia di interfaccia: Bus seriale singola linea riferita a massa.
- range di tensione del segnale: 0-5 Volt.
- connessione: jack 3.5 mm stereo (con solo Sleeve e Ring usati?).
- logica di rilevamento collisione: mista Hw/Sw (circuiteria logica + interrupt). 

### Protocollo di trasmissione
- velocità di trasmissione: 128Kbit/sec
- tipologia: self-clocking 1-wire (PWM based)
- struttura rete: multi Master-Slave. Più Master possono trasmettere in una rete a cui sono collegati vari Slave (rispettando priorità e regole per l'occupazione del bus)
- struttura dati: i dati sono organizzati in frame/pacchetti e sono trasmessi in modo continuo e "sincronizzato".
- la frequenza di trasmissione dei frame può essere modulata entro un range determinato (50-100 frame/sec) in modo da generare un clock di sistema per il controllo della velocità (BPM) in una modalità simile a quella utilizzata nello standard MIDI tramite i messaggi SysEx Clock (questa modalità rende più immediata la compatibilità con il protocollo MIDI). 

### Dimensionamento e struttura dei frame dati
Da una prima analisi delle necessità di quantità, precisione e velocità dei dati da trasmettere e del numero di device potenzialmente collegabili al sistema, nasce questa prima versione di standard "dCV" (digital CV), che non preclude la possibilità di sviluppare versioni successive qualora si riscontrassero limiti o si volessero implementare funzionalità aggiuntive. Questa è da considere come una Beta release con cui effetture test e verifiche di affidabilità della tecnologia proposta.
Sulle specifiche di questa prima versione saranno infatti progettati e resi disponibili i primi prototipi hardware che renderanno possibile testare le funzionalità del sistema proposto con i sistemi Eurorack esistenti (interfaccia MIDI -> dCV, dCV -> CV/Gate/Sync, ecc...)

segue...





