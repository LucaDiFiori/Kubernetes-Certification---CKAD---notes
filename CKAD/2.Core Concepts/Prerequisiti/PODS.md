Come abbiamo visto, con Kubernetes, il nostro scopo ultimo è quello di distribuirela nostra applicazione sotto forma di containers su una serie di macchine, configurate come workers node, in un cluster. Tuttavia Kubernetes non andrà a distribuirla direttamente in questi nodi. I containers sono infatti incapsulati in un *oggetto* chiamato **pod** 

In Kubernetes, un **Pod** è l'unità base di esecuzione e la più piccola entità di distribuzione. 
Rappresenta una singola istanza dell'applicazione ed è composto da un gruppo di uno o più container che condividono le stesse risorse di rete e di storage

![[Pasted image 20241118153039.png]]

Un Pod rappresenta un "host logico" specifico per l'applicazione: contiene uno o più container applicativi che sono strettamente collegati tra loro.

- **Unità di esecuzione**: Un Pod rappresenta un'istanza di un'applicazione o di una parte di essa. È l'unità base per l'esecuzione delle applicazioni in Kubernetes.
- **Scopo**: Un Pod serve a raggruppare container che devono lavorare insieme strettamente. Un esempio comune è un container principale che serve un'applicazione e un container "sidecar" che si occupa di fare log o di sincronizzare dati.

#### Esempio pratico:
Supponiamo che tu abbia un'applicazione web. Puoi avere un Pod con due container:

- **Container 1**: un server web (es. Nginx o Apache).
- **Container 2**: un container per il logging che raccoglie i log del server web e li invia a un server remoto.

Questi due container nel Pod condividono la stessa rete e possono accedere allo stesso volume di dati per salvare e leggere file di log.

In sintesi, un **Pod** è una struttura che permette ai container di lavorare insieme e condividere risorse, facilitando la gestione e l'esecuzione coordinata delle applicazioni in Kubernetes.

#### Esempio pratico: Scalabilità
![[Pasted image 20241118153635.png]]
Consideriamo l'esempio più semplice in cui abbiamo:
	- un solo nodo nel nostro cluster 
	- con una singola istanza dell'applicazione in esecuzione in un singolo container 
	- incapsulato in un pod


Supponiamo ora che il numero degli utenti aumenti e devo scalare la mia applicazione. Posso aggiungere un nuovo pod (contenente un istanza dell'applicazione) all'interno del nodo, o aggiungere direttamente un nuovo nodo con il suo pod (contenente un container con l'istanza dell'applicazione).
Quello che non voglio fare è aggiungere una nuova istanza (container) al pod esistente.

![[Pasted image 20241118154521.png]]

**Importante**: Da questo esempio vediamo che i pod ed i container hanno una relazione uno a uno 
  
  

***



## Caratteristiche principali dei Pod
Nota: È necessario installare un runtime per container su ciascun nodo del cluster affinché i Pod possano essere eseguiti su di essi.

- **Gruppo di container**: Un Pod può contenere uno o più container, ma la pratica più comune è avere un singolo container per Pod. Tuttavia, quando più container sono inclusi nello stesso Pod, questi condividono lo stesso [[NAMESPACE]]di rete, indirizzo IP e possono comunicare tra loro tramite `localhost`. 

-  **Condivisione di risorse**: I container all'interno di un Pod   condividono risorse come:
	- **Network**: Tutti i container condividono l'indirizzo IP del Pod, il che significa che possono comunicare tra loro internamente senza bisogno di configurazioni di rete complesse.
	- **Storage**: Un Pod può essere configurato per utilizzare [[VOLUMI]] condivisi tra i container per garantire la persistenza dei dati o la condivisione degli stessi.

***
## Componenti di un Pod

1. **Container**: Ogni Pod può contenere uno o più container (ad esempio container Docker), che eseguono applicazioni e condividono lo stesso ciclo di vita.
2. **Namespace di rete**: I container all'interno del Pod possono comunicare tra loro usando `localhost`, poiché condividono lo stesso namespace di rete.
3. **Volume**: I Pod possono montare volumi persistenti per la condivisione di dati tra container o per la persistenza dei dati.



***


## Tipi di pod

- **Single-container Pod**: Contiene un solo container ed è il caso tipico per la maggior parte dei deployment Kubernetes.
  In questo contesto, puoi pensare a un Pod come un involucro attorno a un singolo container. Kubernetes gestisce i Pod invece di gestire direttamente i container.

- **Multi-container Pod**: Contiene più container che lavorano insieme come un'unica unità e condividono lo stesso contesto di rete e storage. Questi container di solito si integrano per offrire funzionalità complementari, come un server web con un proxy di logging., così che ad ogni creazione di una nuova istanza dell'applicazione questa sià già accompagnata dal suo helper container.
  Note: UNo scenario multi-container pod è raro
  

***


### Funzionamento dei Pod

I Pod sono progettati per essere effimeri, cioè creati e distrutti in base alle esigenze. Se un Pod va in crash o viene eliminato, Kubernetes non lo ripara direttamente, ma crea un nuovo Pod con le stesse specifiche attraverso il controller responsabile, come un **Deployment** o un **ReplicaSet**.



***


### Come i Pod interagiscono con l'infrastruttura

I Pod comunicano con l'infrastruttura sottostante attraverso i seguenti componenti:

- **Kubelet**: Un agente che gira su ogni nodo del cluster e si assicura che i container specificati nei Pod siano in esecuzione.
- **Scheduler**: Responsabile di decidere su quale nodo eseguire il Pod basandosi sulle risorse disponibili e sulle specifiche del Pod.
- **Controller**: Garantisce che un numero specifico di Pod sia sempre in esecuzione. Esempi di controller sono **ReplicaSet**, **Deployment**, **StatefulSet**, e **DaemonSet**.



***


## Creare un pod: Come eseguire il Deploy dei Pod
### kubectl
Abbiamo visto il comando
```BASH
kubectl run nome_pod
```

Quello che fa davvero è esegurie il deploy di un container docker creando un pod:
	- Crea automaticamente un pod
	- esegue il deploy di un'istanza dell'immagine docker desiderata



#### Ma da dove prende questa immagine?
Dovrò specificare il nome dell'immagine usando il parametro
```BASH
--image
```
L'immagine desiderata verrà a questo punto scaricata dalla repository di Docker Hub


#### esempio completo
```BASH
kubectl run nginx --image nginx
```

In questo esempio creo un pod chiamato "nginx" che esegue l'immagine del container ufficiale di nginx.
Questo comando è un modo rapido per avviare un container all'interno di un Pod nel cluster Kubernetes.


#### Dettagli del comando:
1. **`kubectl`**: È lo strumento da riga di comando (CLI) di Kubernetes che permette di interagire con un cluster Kubernetes. Viene utilizzato per eseguire varie operazioni come la creazione, l'eliminazione e la gestione delle risorse nel cluster.
    
2. **`run`**: È un comando di `kubectl` che crea una risorsa nel cluster, in questo caso, un Pod. Tradizionalmente, `kubectl run` veniva usato anche per eseguire una singola istanza di un container, ma con le versioni recenti di Kubernetes è diventato principalmente un modo per creare Pods con determinati container.
    
3. **`nome_pod`**: Questo è il nome del Pod che verrà creato. In questo caso, il Pod sarà chiamato `nome_pod`. Il nome del Pod è importante perché lo utilizzerai per interagire con il Pod (ad esempio per ottenere informazioni o eliminarlo).
    
4. **`--image nome_immagine`**: Questo specifica l'immagine del container da eseguire nel Pod. In questo caso, stai dicendo a Kubernetes di usare l'immagine `nome_immagine`
   Il flag `--image` è obbligatorio e indica il container da utilizzare all'interno del Pod.




***



### Esempio di YAML per un Pod

Un esempio semplice di configurazione YAML per un Pod con un singolo container potrebbe essere:

```YAML
apiVersion: v1 
kind: Pod 
metadata: 
	name: example-pod 
spec: 
	containers: 
	- name: example-container 
	image: nginx:latest 
	ports: 
	- containerPort: 80
  ```

### Conclusione

I Pod sono l'elemento fondamentale di Kubernetes per l'esecuzione delle applicazioni containerizzate. Consentono ai container di condividere le risorse di rete e di storage e sono facilmente scalabili e gestibili grazie ai controller e agli strumenti integrati di Kubernetes.




***



## Perché Kubernetes usa i Pod e non i container direttamente?

- **Abstractive Layer**: Kubernetes considera i **Pod** come un'astrazione di livello superiore rispetto ai container. Questo permette a Kubernetes di gestire i container con un insieme standard di regole e comportamenti. I Pod sono l'unità più piccola che Kubernetes può programmare e gestire, quindi tutta la logica di orchestrazione (come il controllo dello stato, il bilanciamento del carico e la replica) è costruita intorno ai Pod, non ai container.
    
- **Flessibilità per aggiungere container**: Anche se hai un'applicazione in un solo container oggi, un Pod ti permette di aggiungere in futuro container supplementari (ad esempio, per la gestione dei log o per servizi di monitoraggio) senza cambiare la struttura del Pod stesso. In questo modo, il Pod funge da contenitore logico che può crescere con le esigenze dell'applicazione.
    
- **Condivisione delle risorse**: I container all'interno di un Pod condividono le risorse di rete e storage. Questo significa che puoi avere container che collaborano strettamente tra loro, come un'applicazione principale e un container di supporto (sidecar), che condividono lo stesso indirizzo IP e volume di dati. Anche se la tua applicazione ha un solo container, Kubernetes deve mantenere questa struttura per uniformità e flessibilità.
    
- **Gestione del ciclo di vita**: Kubernetes gestisce il ciclo di vita dei Pod. Se un Pod fallisce, Kubernetes può terminare e ricreare il Pod per garantire l'alta disponibilità. Gestire container singoli senza l'astrazione dei Pod renderebbe questo processo molto più complesso.


## Perchè non usare un ketwork di container
#### 1. **Coerenza nel ciclo di vita**
- **Pod**: Quando hai un Pod, Kubernetes gestisce l'intero ciclo di vita di tutti i container al suo interno come una singola unità. Se un Pod fallisce o deve essere scalato, Kubernetes può facilmente replicarlo, riavviarlo o distribuirlo su un altro nodo. Questo approccio centralizza il controllo del ciclo di vita, mantenendo tutto dentro un'unità di gestione.
- **Network di container**: Se avessi un network di container senza un'astrazione come i Pod, dovresti gestire individualmente il ciclo di vita di ogni container all'interno di una rete. Se uno di questi container dovesse fallire o essere sostituito, dovresti gestire manualmente la sua sostituzione o il riavvio, il che complicherebbe la gestione dei container nel cluster.

#### 2. **Comunicazione e Networking**
- **Pod**: I container all'interno di un Pod condividono lo stesso indirizzo IP e lo stesso spazio dei nomi di rete (network namespace). Questo significa che possono comunicare tra loro utilizzando `localhost`, senza dover gestire un complesso sistema di networking tra container diversi. La comunicazione tra i container in un Pod è veloce e semplificata.
- **Network di container**: Usare solo un network di container separati richiederebbe che ogni container avesse un indirizzo IP separato e complessi meccanismi di networking per farli comunicare tra loro. In Kubernetes, ogni container in un Pod avrebbe bisogno di un proprio indirizzo IP, e dovresti gestire manualmente il routing e la comunicazione tra questi indirizzi IP, aumentando la complessità.

#### 3. **Storage condiviso**
- **Pod**: Tutti i container all'interno di un Pod possono montare gli stessi volumi condivisi. Questo significa che i container possono accedere ai dati nello stesso spazio di storage, facilitando la persistenza dei dati e la cooperazione tra container. Ad esempio, un container potrebbe scrivere i log su un volume condiviso che un altro container potrebbe leggere.
- **Network di container**: Se usassi un network di container separati, ogni container dovrebbe avere il proprio spazio di storage o una gestione separata dei volumi, il che complicherebbe la condivisione dei dati tra container.

#### 4. **Semplicità di gestione**
- **Pod**: Kubernetes è progettato per gestire i Pod come unità atomiche. Questo significa che l'intera configurazione e la gestione della distribuzione, del bilanciamento del carico e del monitoraggio sono centralizzate per ogni Pod. Se un Pod ha più container, Kubernetes gestisce tutto come se fosse un'unica entità, semplificando l'orchestrazione.
- **Network di container**: Gestire un network di container separati senza l'astrazione del Pod renderebbe più difficile la gestione centralizzata e l'orchestrazione. Avresti bisogno di sistemi separati per gestire i container in rete, aumentare la complessità operativa e ridurre la coerenza tra i vari container nel sistema.

#### 5. **Scalabilità**
- **Pod**: Kubernetes è costruito per scalare facilmente i Pod in base alle esigenze di carico. Se un Pod ha un solo container, puoi replicarlo facilmente per gestire il carico, o se un Pod ha più container, Kubernetes gestirà il numero di repliche per ciascuno di essi in modo coerente. Questo approccio è molto più facile da gestire e automatizzare.
- **Network di container**: Scalare un network di container senza un Pod richiederebbe che ogni container sia gestito separatamente, complicando la gestione automatica delle repliche, il bilanciamento del carico e la distribuzione dei container.

#### 6. **Gestione dei fallimenti e resilienza**
- **Pod**: Quando un container all'interno di un Pod fallisce, Kubernetes può terminare e riavviare l'intero Pod, mantenendo il sistema in uno stato coerente. Se il Pod è parte di un ReplicaSet o Deployment, Kubernetes garantirà che il numero desiderato di Pod sia sempre attivo.
- **Network di container**: Se i container non sono legati insieme da un Pod, la gestione dei fallimenti e il riavvio di container diventano più complessi. Ogni container potrebbe fallire separatamente, e dovresti gestire manualmente la sostituzione dei container senza un sistema centralizzato di gestione del ciclo di vita.


***



# Comandi utili:
1.
```bash
kubectl run <nome-pod> --image <nome-image>
```
-  Usato per **creare un pod** con il nome *nome_pod* che esegue l'immagine ufficiale del container *nome_container* scaricato da docker hub


2.
```bash
kubectl get pods
```
-  resituisce l'elenco di pods attualmente presenti nel cluster, mostrandone lo stato e altre informazioni quali:
	-  NAME: Nome del pod
	- READY: Indica il numero di container "pronti" nel Pod rispetto al numero totale di container.
	- STATUS: Indica lo stato attuale del Pod.
	- RESTARTS: Mostra il numero di riavvii che un container nel Pod ha avuto.
	- AGE: Indica da quanto tempo il Pod è stato creato


3.
```bash
kubectl describe pod <nome-pod>
```
-  Fornisce una descrizione **dettagliata** del pod `<nome-pod>`
  
-  `kubectl describe pod`: Senza specifiche fornisce info dettagliate su tutti i pod di un namespace


4.
```bash
kubectl get pod <nome-pod> -o wide
```
- Fornisce **informazioni dettagliate ma sintetiche** sullo stato di un pod.
  
- Info fornite:
	  - **NAME**: Nome del Pod.
	  - **READY**: Stato dei container nel Pod.
	  - **STATUS**: Stato attuale del Pod.
	  - **RESTARTS**: Numero di volte che uno o più container del Pod sono stati riavviati.
	  - **AGE**: Tempo trascorso dalla creazione del Pod.
	  - **IP**: L'indirizzo IP assegnato al Pod all'interno del cluster.
	  - **NODE**: Nome del nodo su cui il Pod è schedulato.
	  - **NOMINATED NODE**: (Facoltativo) Nodo candidato per lo spostamento del Pod in caso di rischedulazione.
	  - **READINESS GATES**: (Facoltativo) Informazioni su eventuali condizioni extra che devono essere soddisfatte prima che il Pod venga considerato pronto.



5.
```bash
kubectl delete <nome-pod>
```
- utilizzato per eliminare un Pod specifico in Kubernetes. Questo comando rimuove il Pod dal cluster e libera le risorse ad esso associate.



6.
```bash
kubectl create -f <nome_file.yml>
```
- crea risorse in un cluster Kubernetes a partire da un file di configurazione YAML o JSON.
  Nel caso di un pod lo crea e tenta di eseguirlo 

7.
```bash
kubectl run <nome-pod> --image=<nome-img> --dry-run -o yaml
```
-  Il comando `kubectl run` con le opzioni `--dry-run` e `-o yaml` viene utilizzato per **generare un file YAML** di configurazione basato sui parametri specificati, senza effettivamente creare una risorsa nel cluster. È molto utile per ottenere un file YAML che puoi modificare e utilizzare successivamente con `kubectl apply`.


8.
```bash
kubectl apply -f <file-configurazione>
```
- utilizzato per **applicare una configurazione** a uno o più oggetti del cluster Kubernetes. Questo comando legge un file di configurazione (di solito in formato YAML o JSON) e crea, aggiorna o lascia invariati gli oggetti del cluster
  Es. `kubectl apply -f <file-configurazione>`


9.
```bash
kubectl edit pod <nome-redis>
```
-  utilizzato per **modificare direttamente** la configurazione di un pod esistente in Kubernetes, aprendo l'editor predefinito




- Note **edit pods**: In any of the practical quizzes, if you are asked to **edit an existing POD**, please note the following:

	- If you are given a pod definition file, edit that file and use it to create a new pod.
    
	- **If you are not given a pod definition file**, you may extract the definition to a file using the below command:
    
	    `kubectl get pod <pod-name> -o yaml > pod-definition.yaml`
    
	    Then edit the file to make the necessary changes, delete, and re-create the pod.
    
	- To modify the properties of the pod, you can utilize the `kubectl edit pod <pod-name>` command. Please note that only the properties listed below are editable.