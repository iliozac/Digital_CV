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





