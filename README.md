# Documento di specifica dello standard "dCV" (digital CV) - Ver 0.1
## Architettura hardware e protocollo di comunicazione seriale per sistemi modulari analogici (Eurorack e altri).

### PREMESSE

  L'obiettivo generale è di sviluppare, testare e rendere disponibile in modalità open un nuovo standard di comunicazione per l'interfacciamento e il pilotaggio di moduli eurorack e sintetizzatori analogici stand alone.

  Lo standard MIDI, sviluppato nei primi anni '80, è stato progettato per risolvere le esigenze di interoperabilità e sincronizzazione tra gli strumenti musicali elettronici, partendo dal presupposto che la tecnologia digitale stava ormai monopolizzando la produzione di nuovi strumenti, sia nel controllo che nella generazione dei suoni. Quello che gli ideatori del MIDI non poteva prevedere è che più avanti nel tempo la tecnologia analogica avrebbe riconquistato un suo spazio e un suo mercato in una forma più organizzata e funzionale, grazie alla nascita dello standard Eurorack ideato da Dieter Doepfer, concepito per funzionare senza un'infrastruttura di comunicazione digitale come il MIDI.

  Il formato Eurorack ha consentito una vera rivoluzione nel modo di concepire la sintesi musicale e nel modo di progettare e utilizzare l'hardware (ovvero i moduli) per assemblare in modo creativo e personale strumenti per creare suoni. In questo nuovo comparto lo standard MIDI si è però rivelato fortemente limitato se non totalmente incompatibile, nonostante molti siano stati i tentativi di adattarlo e applicarlo ai sistemi modulari, senza mai prendere in considerazione il fatto che il MIDI è nato quasi mezzo secolo fa per risolvere esigente tecniche molto diverse e soprattutto è nato molto prima che i produttori di moduli convergessero inaspettatamente verso uno standard meccanico (eurorack) e un sistema di controllo principalmente analogico (quello basato su CV, Gate e Sync). 
  
  La versione MIDI 2.0 uscita nel 2020, seppure offra molte più funzionalità, per assurdo si allontana ancora di più dalle esigenze del settore dei modulari che ha una sua filosofia progettuale e costruttiva ormai ben delineata. Per un amante dell'elettronica analogica non si potrebbe immaginare nulla di più affascinante di una semplice tensione variabile da 0-5V (o 0-10V) che viene riconosciuta e interpretata in modo pressochè univoco da un intero universo di circuiti (peraltro molti dei quali ormai implementano microcontrollori e tecnologia digitale). 
  
  Lo studio di uno standard di comunicazione tecnologicamente più evoluto ed espressamente pensato per i sistemi modulari e gli strumenti analogici ha quindi uno scopo ben preciso: dare ulteriore impulso progettuale e produttivo al comparto dei moduli Eurorack e in generale al settore della sintesi musicale analogica, senza snaturare la filosofia su cui si è sviluppata e ha conquistato il suo spazio nell'universo della musica elettronica.
  Anche per questa ragione lo standard dCV è pensato sia per nuovi prodotti, nei quali una porta dCV specifica potrebbe essere facilmente implementata in fase di progetto, sia per garantire la retrocompatibilità con i moduli classici (dotati dei classici ingressi CV, Gate e Sync), tramite l'utilizzo di specifici circuiti di interfaccia da inserire a rack o all'interno dei case (eventualmente anche come upgrade hardware di singoli moduli).

### Obiettivi
- coprire il gap tecnologico che limita le applicazioni dei sistemi modulari rispetto alle catene di suono gestite via Midi
- rendere possibile la configurazione di strutture di generazione di suono più articolate e dinamiche.
- rendere semplice e funzionale il controllo di strutture modulari polifoniche/parafoniche con master keyboard, sequencer midi e tools sofware vari.
- velocizzare il remapping delle strutture modulari (richiamo di preset), riducendo al minimo il patching manuale per gli utilizzi "live".
- mantenere la retrocompatibilità con i moduli classici.

### SPECIFICHE GENERALI DEL SISTEMA

Da una prima analisi delle necessità di quantità, precisione e velocità dei dati da trasmettere e del numero di dispositivi potenzialmente collegabili al sistema, nasce questa prima versione (ver. 0.1), da considere come versione prototipale su cui sarà possibile eseguire test di funzionalità e affidabilità della tecnologia proposta. A questa seguirà una prima versione Beta (1.0) ed eventuali successive quando necessarie a risolvere criticità o implementare funzionalità aggiuntive. 
Sulla base delle specifiche di questa prima versione saranno progettati e resi disponibili i primi prototipi hardware (+ eventuali librerie di codice C++) che consentiranno di testare le funzionalità del sistema proposto rendendo possibile la sua implementazione in strutture Eurorack esistenti senza la necessità di sviluppare nuovi moduli (interfaccia MIDI -> dCV, dCV -> CV/Gate/Sync, ecc...).

### Interfaccia fisica (Hardware)
- tipologia: Bus seriale singola linea riferita a massa.
- range di tensione del segnale: 0-5 Volt.
- connessione: jack 3.5 mm stereo (con solo Sleeve e Ring usati?).
- logica di rilevamento collisione: mista Hw/Sw (circuiteria logica + interrupt). 

### Protocollo di trasmissione
- velocità di trasmissione: 128Kbit/sec
- tipologia: self-clocking 1-wire (PWM based)
- struttura rete: multi Master-Slave. Più Master possono trasmettere in una rete a cui sono collegati vari Slave, rispettando priorità e regole per l'occupazione del bus (prima differenza dal MIDI)
- struttura dati: i dati sono organizzati in frame (pacchetti) e sono trasmessi in modo continuo e "sincronizzato" (seconda differenza dal MIDI).
- la frequenza di trasmissione dei frame può essere modulata entro un range determinato (50-100 frame/sec) in modo da generare un clock di sistema per il controllo della velocità (BPM) in una modalità simile a quella utilizzata nello standard MIDI tramite i messaggi SysEx Clock (questa modalità rende più immediata la compatibilità con il protocollo MIDI). 

### ARCHITETTURA E SPECIFICHE DI FUNZIONAMENTO

Per determinare le specifiche di funzionamento del sistema analizziamo un primo elenco di problematiche specifiche dei sistemi modulari per le quali il nuovo standard potrebbe offrire una soluzione o un miglioramento prestazionale.

  ### Valutazione problematiche da risolvere
  
1. ***Sincronizzazione con strumenti esterni (synth, master keyboard, sequencer, software DAW, ecc...)***.
   
   Il protocollo **dCV** si basa sulla trasmissione continua di **frame di dati** aggiornati ad una frequenza massima di 100 frame/sec. In questo modo qualsiasi device collegato al bus (sia master che slave) può inviare e/o ricevere informazioni in modo ordinato e sincronizzato. Uno dei dati che possono essere trasmessi è il comando "dClock" (posto all'inizio del frame) che, in modo analogo al Real-Time Clock del protocollo MIDI, consente di "sincronizzare" i moduli ad una determinata velocità generale. Una delle caratteristiche del sistema dCV è infatti quella di poter collegare più device master in contemporanea (fino a un massimo di 16), a condizione che uno dei device master sia configurato come "***Master Sound Manager***", ovvero un dispositivo master con una priorità superiore a tutti gli altri, che stabilisce la velocità generale del sistema (BPM) e "regola il traffico" dei dati nel bus. Ad ogni dispositivo Master (che può essere esterno o interno al sistema modulare) dev'essere quindi assegnato un indirizzo da 0 a 15 e il disposivo a cui assegneremo l'indirizzo 0 sarà il Master Sound Manager (MSM).

   La sincronizzazione con strumenti esterni può quindi avvenire sostanzialmente in due modi:

    - Via connessione MIDI (o USB midi) e conversione MIDI->dCV (tramite interfaccia installata nel modulare).
    - Direttamente tramite connessione dCV, con device esterni che implementano un uscita dCV. 
      
3. ***Gestione di strutture polifoniche/parafoniche***

   Data la complessità e la concentrazione dei collegamenti necessari, l'implementazione di strutture di generazione polifoniche o parafoniche in un sistema modulare classico, attraverso la tecnica del patching (collegamenti via cavi jack), risulta particolarmente complessa già in caso di un utilizzo "in studio" e diventa praticamente impossibile da gestire in contesti di performance "Live".
  Questo è dovuto a quello che possiamo definire il "peccato originale" della categoria dei modulari, ovvero il suo sistema di controllo basato su tensioni analogiche (i fatidici segnali "CV") e impulsi asincroni (Gate e Sync).

   Se proviamo a immaginare una struttura anche solo parafonica minimale a 4 voci, incontriamo subito un primo enorme ostacolo, ovvero il fatto che per ogni singola voce di polifonia avremmo bisogno di un controllo CV dedicato (quindi 4 segnali CV separati) e da dove dovrebbero arrivare questi 4 segnali? Qualsiasi sequencer o sintetizzatore dotato di uscita CV fornirebbe una singola tensione sull'uscita CV, e sarebbe un'informazione monofonica. Ci si potrebbe sbizzarrire in soluzioni macchiavelliche con l'aiuto di DAW e software di programmazione (tipo Pure Data) per elaborare le informazioni midi inviate dalla tastiera, implementare buffer circolari e rimappare l'invio di stringhe su 4 diversi canali midi per inviare a 4 diversi convertitori Midi/CV le informazioni di frequenza dei 4 VCO. E mancherebbe ancora la gestione dei VCA/VCF e relativi generatori di inviluppo... diciamo non esattamente una cosa semplice?

   Il dCV, a differenza del MIDI che trasmette informazioni di tipo "asincrono" (la pressione di un tasto o la modifica di un parametro), è pensato per mantenere costantemente disponibili e aggiornate le informazioni sul bus, anche quando non ci sono eventi specifici, esattamente come in un sistema modulare in cui tutte le tensioni CV sono sempre "disponibili", anche se sono a 0. Inoltre sul bus dCV sarebbero costantemente disponibili anche le informazioni necessarie alla sincronizzazione degli eventi (inviluppi, step di progressione di sequencer, clock di sistema, ecc...). Un modulo a 4 VCO in cui fosse previsto in ingresso dCV (input digitale) e la relativa logica/sw di gestione del protocollo seriale dCV (molto semplice) potrebbe recuperare le informazioni di frequenza e durata per tutti i 4 VCO dal bus dCV, lo stesso sarebbe per qualsiasi altro modulo con sezioni circuitali multiple (VCA, VCF). 

4. ***Patching ridotto all'essenziale e alta configurabilità***

   Un sistema di controllo basato su bus dCV consentirebbe di sfruttare molti dei vantaggi di un controllo digitale classico: stabilità, affidabilità, flessibilità, gestione intelligente delle configurazioni (es. memorizzazione dei setup), ecc... senza però stravolgere la filosofia costruttiva originaria del modulari. Tramite il patching potrebbe essere definita la struttura generale del sistema (tipologia e numero di oscillatori, voci di polifonia, ordine dei devices che intervengono nella catena di generazione/modifica suoni, ecc...) mentre tramite il bus dCV si potrebbe controllare il flusso dei parametri (CV, Gate, Sync, Clock) da inviare ai vari moduli della catena, modificare in tempo reale configurazioni e setup senza spostare nemmeno una patch.

   Al bus dCV possono essere connessi decine di moduli e su un unico filo possono transitare una quantità enorme di parametri e comandi che in un cablaggio classico necessiterebbero di un numero impressionante di patch.

   Il dCV è pensato anche come tool per facilitare la configurazione di qualsiasi tipo di architettura modulare, dalle più semplici alle più complesse. Da quelle controllate da un singolo Master device (es. una tastiera Midi o un sequencer) che pilota una singola linea monofonica, fino ad architetture polifoniche o con più master devices (arpeggiatori, drum machine, sequencer, tastiere) che intervengono nella generazione e nel controllo dei suoni. 


### Definizione del protocollo

Per ora fermiamoci alla valutazione di queste 3 esigenze, che già da sole potrebbero giustificare l'adozione del sistema dCV, ed entriamo nello specifico del protocollo e delle caratteristiche che deve avere per soddisfare le esigenze di cui sopra.

In fase di prima stesura delle specifiche abbiamo definito 3 valori ovvero:

- velocità di trasmissione seriale: 128 Kbit/sec
- frequenza dei frame dati: 100 frame/sec
- numero massimo di Master devices: 16

manca ancora un valore fondamentale per la definizione del protocollo: la quantità di dati da trasmettere per ogni frame.

  In un intervallo di tempo di 10 mS (se consideriamo 100 frame/sec) ad una frequenza di 128Kbit/sec possono essere trasmettessi frame da massimo 1280 bit. Per non "strozzare" il bus e prevedere un po' di tolleranza stabiliamo un limite massimo di 1024 bit/frame. In questo modo anche un Master device che dovesse gestire da solo l'intero flusso di dati da trasmettere, avrebbe ancora un 20% abbondante di tempo a disposizione per occuparsi di altro.
Abbiamo quindi 1024 bit a disposizione per trasmettere dati su 16 canali contemporaneamente (che corrispondono anche al numero massimo di Master device che possono condividere lo stesso bus dCV). 
Resta da definire il numero di Slave device che possono essere collegati al bus e per stabilire questo numero è necessario definire tipologia, quantità e dimensione dei dati da trasmettere.
In linea teorica non esiste un reale limite al numero di slave che possiamo collegare al bus dCV, salvo il limite elettronico connesso alla potenza di trasmissione dei master e alle capacitò che si creano e possono limitare la frequenza di trasmissione (cosa rimediabiel con dei rigeneratori di segnale). Vediamo ora come viene gestito il "patching" (collegamenti virtuali) tra i vari master device e i moduli.
Sfruttando le caratteristiche del bus dCV e il protocollo di comunicazione possiamo creare una matrice virtuale di connessioni che può essere modificata "on the fly", memorizzata e richiamata in qualsiasi momento (come un preset).
Ovviamente vi sarà un solo device abilitato a svolgere questa funzione: il Master Sound Manager, l'unico che comanda l'accesso al bus.



### HARDWARE DI TEST (prototipi)
Per testare il sistema dCV saranno sviluppati alcuni circuiti specifici:
  - Master keyboard **dCV** : circuito di scansione matrice di tastiera (con dinamica/aftertouch) e uscita **dCV**
  - Interfaccia **MIDI->dCV** : basata su Atmega328 per la conversione dallo standard MIDI a dCV
  - Interfaccia **dCV->CV/Gate/Sync** : basata su Atmega 328 per la generazione di 8 uscite CV, 4 Gate e 2 Sync (relizzabile in forma di modulo Eurorack)
  - DCO a 2 Oscillatori x 4 voci di polifonia con ingresso di controllo **dCV** : Circuito basato su RP2350 con DCO ibrido analogico/wave-table (relizzabile in forma di modulo Eurorack)

    

