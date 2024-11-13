- Un **nodo** è una macchina (reale o virtuale) su cui è istallato Kubernetes
- Il nodo è il luogo in cui i container vengono lanciati da Kubernetes
- Ogni nodo esegue un insieme di processi per far funzionare il [[CLUSTER]].
- In passato, erano conosciuti come "Minion" (per i workes)
- Ogni nodo è quindi una macchina che hosta un container e può runnarlo grazie all'ambiente runtime come Docker o containerd.
- Ma cosa succede se il nodo su cui è in esecuzione la nostra applicazione si guasta? Ovviamente, la nostra applicazione smette di funzionare. Pertanto, è necessario avere più di un nodo per garantire l'alta disponibilità e la scalabilità, ottenendo così un [[CLUSTER]]


***

# Master Node ([[Control Plane]])
Ora abbiamo un cluster, ma chi è responsabile della gestione del cluster? Dove sono memorizzate le informazioni sui membri del cluster? Come vengono monitorati i nodi? Quando un nodo si guasta, come si sposta il carico di lavoro del nodo guasto su un altro nodo lavoratore? Qui che entra in gioco il Master. 

- Il master è un nodo, in cui è installato Kubernetes, configurato come *master*
- Controlla gli altri nodi del cluster ed è responsabile dell'effettiva orchestrazione dei containers
  sui nodi *worker*
- Il **master node** è il cuore del cluster Kubernetes e contiene i [[COMPONENTI]] principali del **control plane**. Questi componenti gestiscono lo stato globale del cluster, prendono decisioni sulla pianificazione delle applicazioni e rilevano/rimodulano lo stato delle applicazioni. 

## Componenti  
I componenti principali sono:

1. **API Server (`kube-apiserver`)**: L'interfaccia di comunicazione tra gli utenti (tramite `kubectl`) e il cluster. Gestisce le richieste REST e aggiorna lo stato degli oggetti nel cluster.
   La presenza di questa api è quello che rende tale un master in quanto comunica con l'agente [[KUBELET]] presente nel worker. Le info che quest'ultimo invia vengono conservate nell **etcd** presente nel master
2. **Controller Manager (`kube-controller-manager`)**: Esegue vari controller che reagiscono ai cambiamenti di stato, come la gestione dei pod, la replica dei pod e il controllo degli endpoint.
3. **Scheduler (`kube-scheduler`)**: Si occupa di assegnare i pod ai nodi in base a criteri come disponibilità delle risorse, affinità e priorità.
4. **Etcd**: Un database chiave-valore distribuito che conserva tutte le informazioni sullo stato del cluster, comprese le configurazioni e i metadati.
-  Le info che quest'ultimo invia vengono conservate nell **etcd** presente nel master


Il master node coordina e gestisce l'intero cluster, garantendo che le applicazioni funzionino come previsto.

#### Master node vs Control plane:
- **Control Plane**: Rappresenta i servizi e i processi software che controllano e gestiscono il cluster.
- **Master Node**: È la macchina (fisica o virtuale) che ospita ed esegue i componenti del Control Plane.

***

# Worker Node
- Un **worker node** è una macchina che ospita i containers (ad esempio docker containers) ed esegue le applicazioni containerizzate. 
- Per ospitare containers serve che sia presente il runtime del container (in questo caso usiamo Docker)

## Componenti
- **Kubelet**: Se il master node ha il kube-api server, i workers hanno l'agente [[KUBELET]] che è responsabile dell'interazione con un master, per fornire informazione sullo stato di salute nodo stesso ed eseguire le azioni richieste dal master
-  **Kube-proxy**: Un componente di rete che gestisce il traffico di rete in entrata e in uscita per i servizi nel cluster, consentendo la comunicazione tra i pod e l'esterno.
- **Container Runtime**: L'ambiente di esecuzione che gestisce i container (ad esempio, Docker o containerd).


Il **worker node** è responsabile dell'esecuzione dei [[POD]], che sono l'unità minima di esecuzione in Kubernetes e rappresentano uno o più container che condividono risorse come rete e storage.


***

# Come Funzionano Insieme
- Il **control plane** (master node) prende decisioni su dove posizionare i [[POD]], gestisce lo stato desiderato del cluster e monitora le risorse.
- I **worker node** eseguono i [[POD]] e comunicano con il master node per ricevere istruzioni e riportare lo stato corrente.
- L'**etcd** nel master node conserva la verità sullo stato attuale del cluster e sulle configurazioni necessarie.


***

# Relazione tra Nodi e Cluster

- Un **cluster** è composto da almeno un **master node** e uno o più **worker node**. In un ambiente di produzione, spesso si configurano più master node per garantire l'alta disponibilità e la ridondanza.
- I **worker node** forniscono la potenza di calcolo per eseguire i container delle applicazioni, mentre il **master node** garantisce che tutto funzioni come previsto orchestrando il comportamento del cluster.

Questa struttura garantisce che Kubernetes possa gestire e scalare applicazioni containerizzate in modo efficiente e resiliente. Se desideri più dettagli su come i nodi interagiscono tra loro o su un particolare componente, chiedi pure!


***
# Interazione nodo master e worker
- Il nodo master presenta il **kube-api server**
- il nodo worker presenta il [[KUBELET]]
	- Il [[KUBELET]] del worker fornisce informazioni al master
- Le informazioni ottenuto attraverso il kubelet dal worker, vengono salvate in un archivio chiave valore sul master (basato sul popolare framework **etcd**)  

![[WhatsApp Image 2024-11-13 at 15.12.26.jpeg]]
In questa immagine possiamo vedere i [[COMPONENTI]] presenti nei due tipi di nodi e la loro interazione