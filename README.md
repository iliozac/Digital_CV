# Primo documento di specifica dello standard "dCV" (digital CV), inteso come architettura Hardware e Software di comunicazione seriale dedicata ai sistemi modulari (Eurorack e altri analogici).

### PREMESSE

  L'obiettivo è di sviluppare, testare e rendere disponibile in modalità open un nuovo standard di comunicazione per l'interfacciamento e il pilotaggio di moduli eurorack e sintetizzatori analogici stand alone.

  Lo standard MIDI, sviluppato nei primi anni '80, è stato progettato per risolvere le esigenze di interoperabilità e sincronizzazione tra gli strumenti musicali elettronici, partendo dal presupposto che la tecnologia digitale stava ormai monopolizzando la produzione di nuovi strumenti, sia nel controllo che nella generazione dei suoni. Quello che gli ideatori del MIDI non poteva prevedere è che più avanti nel tempo la tecnologia analogica avrebbe riconquistato un suo spazio e un suo mercato in una forma più organizzata e funzionale, grazie alla nascita dello standard Eurorack ideato da Dieter Doepfer, concepito per funzionare senza un'infrastruttura di comunicazione digitale come il MIDI.

  Questa forma di standard ha consentito una vera rivoluzione creativa nel modo di concepire la sintesi musicale e nel modo di progettare e utilizzare l'hardware (ovvero i moduli) per assemblare in modo creativo e personale strumenti per creare suoni. In questo nuovo comparto lo standard MIDI si è quindi rivelato fortemente limitato se non totalmente incompatibile, nonostante molti siano stati i tentativi di adattarlo e applicarlo nel mondo Eurorack, con una sorta di "accanimento terapeutico", senza mai prendere in considerazione il fatto che il MIDI è nato quasi mezzo secolo fa per risolvere esigente tecniche molto diverse e soprattutto è nato molto prima che i produttori di moduli convergessero inaspettatamente verso uno standard meccanico (eurorack) e un sistema di controllo principalmente analogico (quello basato su CV, Gate e Sync). 
  
  La versione MIDI 2.0 uscita nel 2020, seppure offra funzionalità molto più complesse, per assurdo si allontana ancora di più dalle esigenze del settore Eurorack, che ha una sua filosofia progettuale/costruttiva ormai ben delineata che non avrebbe senso stravolgere.
  
  Lo studio di uno standard di comunicazione tecnologicamente più evoluto ed espressamente pensato per i sistemi modulari e gli strumenti analogici ha quindi uno scopo ben preciso: dare un ulteriore impulso progettuale e produttivo al comparto dei moduli Eurorack e in generale al settore della sintesi musicale analogica, senza snaturare la filosofia su cui si è sviluppata e ha conquistato il suo spazio nell'universo della musica elettronica.
  Anche per questa ragione lo standard dCV è pensato sia per nuovi prodotti, nei quali una porta dCV specifica potrebbe essere implementata in fase di progetto, sia per garantire la retrocompatibilità con i moduli classici (con connessioni CV, Gate e Sync), tramite l'utilizzo di specifici circuiti di interfaccia da inserire a rack o all'interno dei case (eventualmente anche come upgrade hardware di singoli moduli).

### Obiettivi
- coprire il gap tecnologico che limita le applicazioni dei sistemi modulari rispetto alle catene di suono gestite via Midi
- rendere possibile la configurazione di strutture di generazione di suono più articolate e dinamiche.
- rendere semplice e funzionale il controllo di strutture modulari polifoniche/parafoniche.
- semplificare l'interfacciamento con master keyboard, sequencer midi e tools sofware vari.
- velocizzare il remapping delle strutture modulari, riducendo al minimo il patching manuale.
- rendere possibile il richiamo immediato (on stage) di configurazioni pre memorizzate (preset), fondamentali per gli utilizzi "live".
- mantenere la retrocompatibilità con i moduli classici.

### SPECIFICHE GENERALI DEL SISTEMA

### Interfaccia fisica (Hardware)
- tipologia: Bus seriale singola linea riferita a massa.
- range di tensione del segnale: 0-5 Volt.
- connessione: jack 3.5 mm stereo (con solo Sleeve e Ring usati?).
- logica di rilevamento collisione: mista Hw/Sw (circuiteria logica + interrupt). 

### Protocollo di trasmissione
- velocità di trasmissione: 128Kbit/sec
- tipologia: self-clocking 1-wire (PWM based)
- struttura rete: multi Master-Slave. Più Master possono trasmettere in una rete a cui sono collegati vari Slave, rispettando priorità e regole per l'occupazione del bus (prima differenza dal MIDI)
- struttura dati: i dati sono organizzati in frame/pacchetti e sono trasmessi in modo continuo e "sincronizzato" (seconda differenza dal MIDI).
- la frequenza di trasmissione dei frame può essere modulata entro un range determinato (50-100 frame/sec) in modo da generare un clock di sistema per il controllo della velocità (BPM) in una modalità simile a quella utilizzata nello standard MIDI tramite i messaggi SysEx Clock (questa modalità rende più immediata la compatibilità con il protocollo MIDI). 

### Dimensionamento e struttura dei frame dati
Da una prima analisi delle necessità di quantità, precisione e velocità dei dati da trasmettere e del numero di device potenzialmente collegabili al sistema, nasce questa prima versione di standard "dCV" (digital CV), che non preclude la possibilità di sviluppare versioni successive qualora si riscontrassero limiti o si volessero implementare funzionalità aggiuntive. Questa è da considere come una Beta release con cui effetture test e verifiche di affidabilità della tecnologia proposta.
Sulla base delle specifiche di questa prima release saranno infatti progettati e resi disponibili i primi prototipi hardware che consentiranno di testare le funzionalità del sistema proposto implementandolo in strutture Eurorack esistenti (interfaccia MIDI -> dCV, dCV -> CV/Gate/Sync, ecc...)

### ARCHITETTURA E PRINCIPIO DI FUNZIONAMENTO

Per determinare le specifiche di funzionamento del sistema analizziamo un primo elenco di necessità/criticità specifiche dei sistemi modulari per le quali il nuovo standard potrebbe offrire una soluzione o un miglioramento prestazionale.

1. ***Sincronizzazione con strumenti esterni (synth, master keyboard, sequencer, software DAW, ecc...)***.
   
   Il protocollo **dCV** si basa sulla trasmissione continua di **frame di dati** (fino a 1024 bit/frame) che vengono aggiornati ad una frequenza massima di 100 frame/sec. In questo modo qualsiasi device collegato al bus (sia master che slave) può inviare/ricevere informazioni in modo ordinato e sincronizzato. Uno dei dati che possono essere trasmessi è il comando "dClock" (posto all'inizio del frame) che, in modo analogo al SysEx Clock del protocollo MIDI, consente di "sincronizzare" i moduli ad una determinata velocità generale. Una delle caratteristiche del sistema dCV è infatti quella di poter collegare più device master in contemporanea (fino a un massimo di 16), a condizione che uno dei device master sia configurato come "***Master Sound Manager***", ovvero un dispositivo master con una priorità superiore a tutti gli altri, che stabilisce la velocità generale del sistema (BPM) e "regola il traffico" dei dati nel bus. Ad ogni dispositivo Master (che può essere esterno o interno al sistema modulare) dev'essere quindi assegnato un indirizzo da 0 a 15 e il disposivo a cui assegneremo l'indirizzo 0 sarà il Master Sound Manager (MSM).
   La sincronizzazione con strumenti esterni può quindi avvenire sostanzialmente in due modi: 1) Tramite connessione MIDI (o USB midi) e opportuna conversione MIDI->dCV. 2) Direttamente tramite connessione dCV con device esterni che implementano un uscita dCV. 
      
3. ***Gestione di strutture polifoniche***

   Data la complessità e la concentrazione dei collegamenti necessari, l'implementazione di strutture di generazione polifoniche o parafoniche in un sistema modulare classico, attraverso la tecnica del patching (collegamenti via cavi jack), risulta particolarmente complessa già in caso di un utilizzo "in studio" e diventa praticamente impossibile in contesti Live.
  Questo è dovuto a quello che possiamo definire il "peccato originale" della categoria dei modulari, ovvero un sistema di controllo basato su tensioni analogiche: i fatidici segnali "CV". Per un amante dell'elettronica analogica non si potrebbe immaginare nulla di più affascinante di una semplice tensione variabile che viene riconosciuta e interpretata in modo pressochè univoco da un intero universo di circuiti (peraltro molti dei quali ormai basati su tecnologia digitale).
  Un sistema di controllo basato su dCV consentirebbe di sfruttare molti dei vantaggi di un sistema di controllo digitale: stabilità, affidabilità, flessibilità e gestione intelligente delle configurazioni (es. memorizzazione dei setup) senza stravolgere la filosofia originaria del modulari. Tramite il patching verrebbe definita la struttura generale del sistema (numero di voci di polifonia, devices che intervengono nella catena di generazione/modifica suoni, ecc...) mentre il bus dCV potrebbe controllare il flusso dei parametri (CV, Gate, Sync, Clock) da inviare ai vari moduli della catena. 



