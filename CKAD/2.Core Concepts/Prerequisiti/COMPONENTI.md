# KUBERNETES ARCHITECTURE
Quando si installa Kubernetes su un sistema, si stanno effettivamente installando diversi [[COMPONENTI]] chiave. Ecco un'analisi dettagliata di ciascun componente e del suo ruolo all'interno del cluster:

![[Pasted image 20241113141527.png]]
#### 1. **API Server**
L'**API server** è il punto di ingresso principale per tutte le interazioni con Kubernetes. Funziona come l'interfaccia front-end del cluster. Gli utenti, i dispositivi di gestione e le interfacce a riga di comando, come `kubectl`, comunicano con l'API server per interagire con il cluster. Questo componente accetta le richieste e le elabora, aggiornando lo stato degli oggetti in Kubernetes tramite l'interazione con `etcd`.

#### 2. **ETCD** STORE
- **ETCD** è un archivio chiave-valore distribuito, affidabile e utilizzato per memorizzare tutti i dati necessari per la gestione del cluster. 
- Ha la responsabilità di mantenere lo stato del cluster in modo distribuito. Questo è cruciale, soprattutto quando si hanno più nodi e più master, poiché `etcd` garantisce la consistenza dei dati. 
- Quando si hanno diversi nodi o master nel cluster, etcd conserva tutte le informazioni du questi nodi in modo distribuito
- Inoltre, implementa i meccanismi di _lock_ per evitare conflitti tra i master e mantenere la coerenza dell'intero sistema.

#### 3. **Scheduler**
- Lo **scheduler** è responsabile della distribuzione dei carichi di lavoro (i container) tra i nodi del cluster. 
- Questo componente monitora i nuovi [[POD]] creati e li assegna ai nodi in base alla disponibilità delle risorse e ai vincoli definiti. 
- Lo scheduler decide su quale nodo un pod dovrebbe essere eseguito, ottimizzando l'uso delle risorse del cluster.

#### 4. **Controllers**
- I **controller** sono considerati il cervello dell'orchestrazione in Kubernetes. 
- Essi monitorano lo stato del cluster e rispondono ai cambiamenti. Ad esempio, se un nodo, un container o un endpoint smette di funzionare, i controller prendono decisioni per avviare nuovi container e ripristinare lo stato desiderato. In questo modo, garantiscono che il cluster rimanga in uno stato coerente e stabile.

#### 5. **Container Runtime**
- Il **container runtime** è il software che consente l'esecuzione dei container. 
- In Kubernetes, può essere utilizzato **Docker**, anche se sono supportati altri runtime come `containerd` e `CRI-O`. Il container runtime è essenziale per la gestione e l'esecuzione dei container nei nodi del cluster.

#### 6. [[KUBELET]]
- Il **kubelet** è un agente che gira su ogni nodo all'interno del cluster. 
- Ha la responsabilità di garantire che i container definiti nei pod siano in esecuzione e funzionino come previsto. 
- Il kubelet comunica con l'API server per ricevere le specifiche dei pod e riporta lo stato dei pod e dei nodi al control plane.
