In un'immagine Docker, un **layer** (o **strato**) rappresenta un insieme di modifiche o istruzioni applicate al file system durante la creazione dell'immagine stessa. Ogni layer corrisponde tipicamente a una riga di comando nel `Dockerfile` (ad esempio, `RUN`, `COPY`, `ADD`). Quando si costruisce un'immagine, Docker esegue queste istruzioni una per una, creando un nuovo layer per ognuna.

I layer sono immutabili e vengono memorizzati in modo incrementale. Ciò significa che se due immagini condividono alcuni comandi o dipendenze, possono condividere gli stessi layer, riducendo l'utilizzo di spazio e migliorando l'efficienza grazie alla **cache**. Quando un layer viene modificato (ad esempio, se si cambia una riga di comando nel `Dockerfile`), tutti i layer successivi devono essere ricreati.

Ogni layer è impilato su quelli sottostanti per formare l'immagine completa. L'ultimo layer è quello di **scrittura**, dove avvengono le modifiche in tempo reale quando il container viene eseguito. Questo approccio rende le immagini Docker leggere, riutilizzabili e facilmente distribuibili.