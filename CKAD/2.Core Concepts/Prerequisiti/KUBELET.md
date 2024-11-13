- Il **kubelet** è un componente fondamentale di Kubernetes che agisce come un agente (cioè un programma o processo autonomo che viene eseguito su ciascun nodo e agisce per conto del sistema Kubernetes) eseguito su ogni nodo all'interno del cluster. 
  
- La sua funzione principale è garantire che i container definiti nei _Pod_ siano in esecuzione e rispettino le specifiche ricevute dal _control plane_ (piano di controllo).
  
- E' responsabile dell'interazione con un master
  
- Svolge infatti il ruolo di collegamento tra il nodo e il _control plane_ del cluster, eseguendo i comandi e le istruzioni ricevute per garantire che il nodo si comporti come richiesto
  
  Nota: Il kubelet opera ovunque vi siano pod da gestire, assicurandosi che il loro stato sia conforme alle definizioni inviate dall'**API Server**. Anche sui nodi master, è presente per contribuire alla gestione dei componenti del [[Control Plane]] che girano come pod (ad esempio, l'API server stesso, il controller manager e il scheduler).
  


### Funzioni Principali del Kubelet:

1. **Gestione dei Pod**: Il kubelet riceve le definizioni dei Pod dall'**API server** e si assicura che i container all'interno dei Pod siano avviati e in esecuzione correttamente sul nodo.
2. **Monitoraggio**: Monitora continuamente lo stato dei container in esecuzione e riporta le informazioni di stato all'API server. Se un container si arresta in modo anomalo o smette di funzionare, il kubelet può tentare di riavviarlo per mantenere il corretto funzionamento del Pod.
3. **Comunicazione**: Si interfaccia con il runtime dei container (ad esempio, **Docker**, `containerd` o `CRI-O`) per eseguire le operazioni sui container come avvio, arresto e monitoraggio.
4. **Applicazione delle Specifiche**: Il kubelet assicura che lo stato corrente dei Pod sul nodo corrisponda alle specifiche definite nei manifest di Kubernetes. Questo significa che controlla che le risorse richieste, come CPU e memoria, siano rispettate e gestite adeguatamente.

### Architettura e Funzionamento:

- Il kubelet si esegue come un servizio di sistema su ciascun nodo.
- Comunica con l'**API server** per ottenere i dettagli dei Pod e invia aggiornamenti sullo stato dei container.
- Non gestisce i container che non sono stati creati da Kubernetes; si concentra solo sui Pod specificati dal piano di controllo.

### Importanza del Kubelet:

Il kubelet è cruciale per il funzionamento di un nodo poiché è responsabile dell'avvio e del mantenimento dei Pod in esecuzione. Senza il kubelet, i Pod non potrebbero essere gestiti, e il nodo non sarebbe in grado di partecipare correttamente al cluster.

In sintesi, il kubelet garantisce che i container siano eseguiti come previsto e funge da collegamento tra il piano di controllo e il runtime dei container sul nodo.