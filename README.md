[00:38, 21/5/2022] Matteo: Parte hardware del robot.
Abbiamo deciso di utilizzare come cervello del robot una Raspberry py 4, aiutata da un Arduino Uno. La prima gestisce tutta la logica di movimento, decidendo dove e quando girare e comunicando con Arduino tramite delle uscite GPIO. Arduino, invece, gestisce i segnali riguardanti il movimento fisico. I linguaggi di programmazione da loro utilizzati sono, rispettivamente, Python e C++.
All'inizio del programma, appena sia la Raspberry che l’Arduino si accendono, il robot rimane fermo (quindi si trova nello stato 0). Non appena viene premuto il pulsante di ripristino, la Raspberry lo rileva utilizzando un interrupt ed il segnale all’Arduino, che farà muovere il motore posteriore prestando attenzione ai segnali di movimento. Nel frattempo il codice sulla Raspberry inizia a cercare le linee colorate con la sua fotocamera (dunque va nello stato 1).
Quando lo stato è impostato su 1, significa che il robot si sta muovendo. Inizialmente, però, non sa se deve girare in senso orario o antiorario: per questo motivo cerca le linee arancioni o blu. Non appena viene trovata la prima linea, viene rilevato ill suo colore e, in base a questo, il senso verso cui girarare. Dunque viene inviato un segnale alla Raspberry, che cambierà lo stato in 2 (in senso orario) o 3 (in senso antiorario) in base al colore che visto.
A quel punto il codice cercherà soltanto quel colore, ignorando l’altro per allegerire il codice, e poi invierà l'indicatore di direzione all’Arduino. Questo si occuperà di rendere il codice più veloce ed evitare possibili errori. Il programma conterà inoltre tutte le linee che incontra durante il tragitto, e quando il conteggio arriva a 12 significherà che il robot ha compiuto 3 giri completi del campo. Infine la Raspberry invierà un segnale di stop all’Arduino e questo procederà a spegnere il motore posteriore.
Quando l’Arduino riceve un segnale di svolta a 90 gradi, invia un segnale al servo-motore, che girerà ad angolo retto, aspetterà per un brevissimo tempo e poi irrigidirà lo sterzo.
Il programma si occupa inoltre di cercare anche delle linee nere sul bordo del telaio, stando a indicare quando il robot si sta dirigendo verso un muro. Quando ciò accade, la Raspberry invia un segnale all’Arduino, che si occucperà di correggere lo sterzo facendolo girare dall'altra parte.
Come facciamo a rilevare i colori nell'immagine? Abbiamo sperimentato diverse metodologie affrontate durante il corso di questo ultimo anno nella materia tecnologie e progettazione di sistemi informatici, materia in cui abbiamo analizzato la computer vision in python attraverso l'utilizzo della libreria open-source "Open-cv". Abbiamo preferito utilizzare la definizione dei colori attraverso la modalità H.S.V. che sta per Hue Saturation Value, dove Hue è il colore identificato in un range di numeri, la Saturation è la quantità di colore mentre il Value corrisponde alla luminosità del colore, attraverso un range di colori identifichiamo un'area colorata e ne calcoliamo le dimensioni e attraverso la posizione decidiamo l'azione da intraprendere.
A livello elettronico abbiamo una LIPO da 7,4 V gestita da due regolatori di tensione, uno a 7 volt per tutti i motori che sono due in tutto, mentre l'altra è impostata a 5V per l'alimentazione di "Raspberry PI 4", poi abbiamo un "Arduino uno".
Perchè abbiamo utilizzato un "Arduino Uno"? Per impostare le curve o meglio per tornare con lo sterzo in posizione iniziale volevamo utilizzare un servomotore 180° ma "RaspBerry PI 4" nei nostri tentativi non riusciva a usare tale motore in modo stabile, crediamo che il pwm di Raspberry abbia una frequenza differente da quella corretta. Nei nostri tentativi abbiamo provato a cambiare tale frequenza con scarso risultato, a questo punto abbiamo preferito usare Arduino che a nostra esperienza sapevamo avere la giusta frequenza e quindi non creava problemi.
[00:38, 21/5/2022] Matteo: Per inserire il codice su Arduino noi abbiamo utilizzato la classica Ide Ufficiale di Arduino in cui attraverso la porta usb viene caricato il programma sulla scheda. Invece per lavorare con Raspberry abbiamo scritto in python il codice e salvato sulla scheda SD dove abbiamo anche caricato un file eseguibile per l'esecuzione automatica del programma nel momento in cui viene avviata raspberry.
[00:38, 21/5/2022] Matteo: Il più grosso difetto del nostro progetto è dovuto all'errata scelta della fotocamera , abbiamo sperimentato differenti fotocamere e differenti posizioni arrivando alla scelta di una posizione elevata rispetto al terreno quello che per ragioni di tempo non abbiamo fatto in tempo a sistemare è il tipo di fotocamera. Avremmo voluto usare una wide camera dato che l'utilizzo dell'obbiettivo grandangolare ci permetterebbe di ampliare il campo visivo raggiungendo anche ostacoli posti nelle aree più a destra o a sinistra del tracciato. Tale acquisto non è avvenuto perchè ci siamo resi conto troppo tardi di questa necessità. La fotocamera montata attualmente comunque permette di individuare gli ostacoli in molte situazioni differenti.