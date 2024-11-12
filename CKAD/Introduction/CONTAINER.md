I container sono una tecnologia di **virtualizzazione leggera** che consente di eseguire applicazioni e i loro ambienti di runtime in modo **isolato** rispetto al sistema operativo sottostante. Essi offrono un modo standard per impacchettare codice e le sue dipendenze, assicurando che l'applicazione venga eseguita in modo coerente in diversi ambienti, dai server locali ai cloud.


***

# Concetti di base dei Container
1. **Isolamento**: Ogni container opera in un proprio spazio isolato, chiamato [[NAMESPACE]], che include un sistema di file, processi, rete e altre risorse. 
   Questo isolamento assicura che un container non interferisca direttamente con altri container o con il sistema operativo host.
   
2. **[[IMMAGINE]] di Container**: Un container viene creato a partire da un'immagine di container, che è una versione **immutabile** dell'applicazione e di tutto ciò che è necessario per eseguirla. 
   Le immagini possono essere create manualmente o derivate da un _Dockerfile_ o da altre fonti. 
   Ogni immagine è composta da più livelli, e quando viene eseguita, questi livelli vengono uniti per formare un singolo file system leggibile.
      
3. **[[RUNTIME]] dei Container**: Un _runtime_ è il software che gestisce i container. 
   Docker è uno dei runtime più conosciuti, ma ci sono altri come _containerd_ e _CRI-O_. 
   Il runtime si occupa di avviare, fermare e gestire i container, oltre a gestire l'interazione con il sistema operativo sottostante.
   
4. **Portabilità**: Una delle caratteristiche principali dei container è la loro portabilità. Poiché includono tutto il necessario per eseguire un'applicazione (come librerie e dipendenze), possono essere spostati senza problemi tra ambienti di sviluppo, test e produzione, garantendo coerenza.

5. **Efficienza rispetto alle macchine virtuali**: I container sono molto più leggeri delle macchine virtuali (VM). Mentre una VM include un intero sistema operativo guest, un container condivide il kernel del sistema operativo dell'host, utilizzando solo una frazione delle risorse necessarie a una VM. Ciò consente un avvio più rapido e un utilizzo più efficiente di CPU e memoria.

# Componenti principali dei Container
1. **Docker e Docker Engine**: Docker è la piattaforma più popolare per creare e gestire container. 
   Include strumenti per 
   - costruire immagini (_Docker build_), 
   - eseguire container (_Docker run_) 
   - gestire i container e le immagini (_Docker ps_, _Docker images_). 
   - _Docker Engine_ è il motore runtime che esegue e orchestra i container.
   
2. **Dockerfile**: Un _Dockerfile_ è un **file di script contenente una serie di istruzioni per creare un'[[IMMAGINE]] di container**. 
   Ogni istruzione rappresenta un passaggio che verrà eseguito per costruire l'immagine, come copiare file, installare pacchetti, eseguire comandi e configurare l'ambiente.
   
   Esempio di un semplice `Dockerfile` in cui:
   l'immagine di base è _ubuntu, viene installato _python3_, viene copiato uno script e impostato il comando di avvio.
  ```BASH
  FROM ubuntu:latest 
  RUN apt-get update && apt-get install -y python3 
  COPY my_script.py /app/my_script.py
  CMD ["python3", "/app/my_script.py"]
  ```

   3. **Registry**: Un _container registry_ è un repository per le immagini di container. Docker Hub è il registro pubblico più famoso, ma ci sono anche soluzioni private come _Harbor_ o i registri gestiti da cloud provider (come Amazon ECR o Azure Container Registry). I registri consentono di archiviare e distribuire immagini di container.
      
   4. **Volume**: I container sono, per natura, _stateless_, il che significa che i dati creati all'interno di un container vanno persi quando il container viene distrutto. I _volumi_ sono una soluzione per la persistenza dei dati, permettendo di montare un percorso di archiviazione persistente all'interno del container, assicurando che i dati sopravvivano anche dopo la terminazione del container.

# Come funzionano i Container
Un container opera su un sistema host e interagisce con il kernel attraverso:

- **Cgroups (Control Groups)**: Regolano le risorse assegnate ai container (CPU, memoria, I/O, ecc.), impedendo che un container consumi più risorse di quanto consentito.
- **[[NAMESPACE]]**: Forniscono isolamento tra i processi e le risorse del container e il sistema host, creando uno spazio indipendente per processi, utenti, rete, ecc.

# Vantaggi dei Container
- **Coerenza tra ambienti**: Un'applicazione containerizzata si comporta allo stesso modo in ambienti diversi, riducendo problemi legati a differenze di configurazione.
- **Scalabilità**: È semplice scalare i container in base al carico di lavoro, aumentando o diminuendo il numero di istanze.
- **Velocità di deployment**: L'avvio di un container richiede pochi secondi, permettendo un ciclo di sviluppo e rilascio più rapido.
- **Compatibilità**: Grazie all'isolamento, i container possono eseguire applicazioni con dipendenze diverse sullo stesso sistema senza conflitti.


***

# Cosa significa "immutabilità" nei container?
Quando si parla di immutabilità dei container, si intende che **l'[[IMMAGINE]] di base di un container non cambia durante la sua esecuzione**. Una volta creata un'[[IMMAGINE]] di container, questa rimane invariata, e qualsiasi container avviato da quell'immagine avrà sempre lo stesso contenuto iniziale. In altre parole, il contenuto e le dipendenze definite nell'immagine del container sono fissi.

Tuttavia, i _container_ stessi, durante l'esecuzione, possono effettivamente subire modifiche temporanee:

1. **Modifiche durante l'esecuzione**: Un container in esecuzione può modificare il proprio file system durante la sua vita. Ad esempio, un'applicazione può creare, modificare o eliminare file. Queste modifiche sono **volatili**, ovvero vengono perse quando il container viene arrestato o distrutto, a meno che non si utilizzi una soluzione di persistenza come i volumi.
    
2. **Persistenza con volumi**: I _volumi_ permettono ai container di mantenere i dati persistenti tra le esecuzioni. Questo implica che, sebbene il container sia considerato immutabile nella sua immagine di base, l'uso di volumi permette di salvare dati che persistono oltre la vita del container stesso.
    
3. **Nuove versioni dell'immagine**: Per aggiornare un'applicazione o cambiare il comportamento di un container, non si modifica l'immagine esistente. Invece, si crea una nuova immagine con le modifiche desiderate e si avvia un nuovo container basato su quella nuova immagine. Questa pratica rafforza il concetto di immutabilità perché garantisce che ogni versione di un'immagine sia tracciabile e riproducibile.


### Vantaggi dell'immutabilità dei container
- **Coerenza e riproducibilità**: Un'immagine immutabile assicura che ogni container avviato da essa sia identico agli altri, eliminando problemi dovuti a configurazioni non coerenti tra ambienti.
- **Sicurezza**: La natura immutabile delle immagini base riduce il rischio di modifiche non autorizzate durante l'esecuzione.
- **Scalabilità**: Permette di scalare orizzontalmente le applicazioni distribuendo più container identici senza rischiare differenze nei contenuti.

### Limiti dell'immutabilità
- **Dati volatili**: Qualsiasi modifica effettuata durante l'esecuzione è persa una volta che il container viene arrestato, a meno di usare volumi o sistemi di storage persistenti.
- **Modifiche durante l'esecuzione**: Sebbene il container possa sembrare immutabile nella sua definizione, durante l'esecuzione possono essere apportate modifiche temporanee al file system interno. Queste modifiche non fanno parte dell'immagine originale e non sono conservate dopo il riavvio.