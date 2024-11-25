***
# TABLE OF CONTENT
- [ 1. Cosa è una replica e perchè necessitiamo di un replication controller](#1. Cosa è una replica e perchè necessitiamo di un replication controller)
- [2.  **Funzionamento di un ReplicaSet**](#2.  **Funzionamento di un ReplicaSet**)
- [3. **Componenti principali**](#3. **Componenti principali**)
- [4. Bilanciamento del carico e scalabilità](<à4. Bilanciamento del carico e scalabilità)
- [5. Replication Controller vs Replica Set](#5. Replication Controller vs Replica Set)
- [6. Creare un Replication Controller](#6. Creare un Replication Controller)
- [7. Creare un Replica Set tramite YAML](#7. Creare un Replica Set tramite YAML)
- [8. Labels and Selectors](#8. Labels and Selectors)
- [9. Scale](#9. Scale)
- [Comandi](#Comandi)
  
  

***
## 1. Cosa è una replica e perchè necessitiamo di un replication controller

Il **ReplicaSet** è un Kubernetes controller responsabile di **garantire che un numero specifico di repliche (istanze) di un pod sia sempre in esecuzione nel cluster**. È il meccanismo che Kubernetes usa per assicurarsi che le applicazioni siano sempre disponibili e scalabili.

- Anche nei casi in cui si dispone di un singolo pod il **replication controller**
  può aiutarci aprendeo automaticamente un nuovo pod quando quello esistente va in crush.
  
  

***
## 2.  **Funzionamento di un ReplicaSet**

1. **Definizione dello stato desiderato**:
    - Un ReplicaSet è configurato tramite un file YAML/JSON, in cui specifichi:
        - Il numero desiderato di repliche (`replicas`).
        - I pod da gestire, identificati da **label**.
     
1. **Monitoraggio dello stato corrente**:
    - Il ReplicaSet osserva quanti pod sono effettivamente in esecuzione nel cluster e li confronta con il numero richiesto.
      
3. **Correzione dello stato**:
    - **Se i pod mancano** (ad esempio, a causa di un errore o della terminazione di un nodo), il ReplicaSet crea nuovi pod.
    - **Se ci sono pod in eccesso**, il ReplicaSet li elimina.




***
## 3. **Componenti principali**

Un oggetto ReplicaSet è composto da:

1. **`replicas`**: Il numero desiderato di repliche.
2. **`selector`**: Le etichette (labels) che identificano i pod gestiti.
3. **`template`**: La definizione del pod che il ReplicaSet deve creare se le repliche mancano.




***
### Esempio
```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: example-replicaset
spec:
  replicas: 3  # Numero di repliche desiderato
  selector:
    matchLabels:
      app: my-app  # Seleziona i pod con questa label
  template:       # Specifica come creare nuovi pod
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: nginx
        image: nginx:1.21
```

- **`replicas`**: Assicura che ci siano 3 pod in esecuzione.
- **`selector`**: Associa il ReplicaSet ai pod con la label `app: my-app`.
- **`template`**: Contiene la configurazione del pod da creare.


***
### Esempio pratico

![[Pasted image 20241120102518.png]]
Consideriamo il nostro esempio in cui avevamo un singolo pod che eseguiva la nostra applicazione. Cosa succede se per qualche motivo la nostra applicazione dovesse bloccarsi ed il pod smettesse di funzionare?

Gli utenti non potrebbero più accedervi. 
Per impedire che questo accada vogliamo avere più di istanze della nostra applicazione in esecuzione contemporanamente


![[Pasted image 20241120102842.png]]
Il *replication controller* ci aiuta a mantenere attive più istanze di un singolo pod nel nostro cluster fornendo così *high availability*




***
## 4. Bilanciamento del carico e scalabilità
Uno dei motivi per cui abbiamo bisogno del controller di replica è creare diverse parti per bilanciare e ripartire il carico su di esse

![[Pasted image 20241120110120.png]]
Ad esempio all'aumentare del carico di utenti su un pod potremmo creare un nuovo nodo, e se il carico su questo nodo risulta elevato potremmo creare nuovi pod su un altro nodo.

Questo controller si estende infatti su diversi nodi e bilancia il carico sia per i pod che per i nodi stessi




***
## 5. Replication Controller vs Replica Set
Il **Replication Controller** e il **ReplicaSet** sono entrambi controller di Kubernetes che garantiscono che un certo numero di pod siano sempre in esecuzione. Sebbene svolgano funzioni simili, il ReplicaSet è una versione migliorata e più flessibile del Replication Controller. Vediamo le differenze principali.

**IMPORTANTE**: Una delle principali differenze è la presenza del **selector** all'interno di un **ReplicaSet** che ci consente di gestire tutti i pod che corrispondono alle etichette specificate, anche se questi non sono stati creati direttamente dal replicaset

#### **Che cosa fanno?**
- **Replication Controller (RC)**:
    
    - È il controller originale di Kubernetes.
    - Monitora lo stato dei pod e ne garantisce un numero fisso, gestendo creazione e terminazione per mantenere lo stato desiderato.
- **ReplicaSet (RS)**:
    
    - È la versione più moderna del Replication Controller.
    - Ha la stessa funzione base, ma supporta funzionalità avanzate come i **selector più flessibili** e viene utilizzato come parte integrante di altri controller, come i Deployment.


#### **Differenza principale: Selector**
La differenza più significativa tra Replication Controller e ReplicaSet è nei **label selectors**, che indicano quali pod gestire:

- **Replication Controller**:
    
    - Usa un **selector rigido**, basato su una corrispondenza esatta delle etichette (match exact labels).
    - Non consente condizioni più complesse.
    - Esempio:
        
        ```yaml
        `selector:   app: my-app`
        ```
        
- **ReplicaSet**:
    
    - Supporta **selector avanzati**, inclusi operatori logici come `in`, `notin`, `exists`.
    - È più flessibile, permettendo di gestire pod con criteri più complessi.
    - Attraverso il selector il **replicaset** può gestire pod che non sono stati creati da lui
    - Esempio:
        
```yaml
selector:
  matchLabels:
    app: my-app
  matchExpressions:
  - key: tier
    operator: In
    values:
    - frontend
    - backend
        ```


#### **Utilizzo con Deployment**
- **Replication Controller**:
    
    - Era usato autonomamente prima dell'introduzione del Deployment.
    - Ad oggi è considerato deprecato e non viene più utilizzato nei nuovi progetti.
- **ReplicaSet**:
    
    - È usato principalmente **all'interno dei Deployment**, che lo gestiscono automaticamente.
    - Non viene quasi mai usato autonomamente, a meno che non si abbia una necessità specifica.


#### **Quando usare RC o RS?**
1. **Replication Controller**:
    - **Non raccomandato per nuovi progetti**. È meglio usare ReplicaSet o Deployment.
2. **ReplicaSet**:
    - Usalo **indirettamente** attraverso i Deployment per una gestione più avanzata e funzionalità come aggiornamenti graduali, rollback, e scalabilità automatica.




***
## 6. Creare un Replication Controller tramite YAML
Vediamo come creare un Replication Controller utilizzando un file YAML.
Anche qui abbiamo i 4 campi principlai:

- **apliVersion**: Come visto è specifico per l'oggetto che vogliamo creare. Anche in questo caso il Replica COntroller è supportato nell'API versione v1
- **kind**: In questo caso creeremo un *ReplicationCOnt*
- **metadata**: Avrà il nome e volendo delle *labels*
- **spec**: Questo campo specifica cosa conterrà l'oggetto che stiamo creando. Nel caso si un Replication Controller, questo conterrà una o più istande di un pod.
  Lo faremo creando un **template** del **pod** da creare.
  
  Come creo questo **pod**?
  Posso copiare in questa sezione il **file.yml di configurazione del pod** escluse
  le linee **apiVersion** e **kind**
  
  ![[Pasted image 20241120113752.png]]
  
  - **spec
	    replicas:** Quello che manca è il numero di repliche che vogliamo attive in ogni istante. A tale scopo aggiungiamo il campo **replicas**, figlio di **spec**
	
	![[Pasted image 20241120114331.png]]



- Quando il file è pronto posso usare il comando:
  
```yaml
kubectl create -f <nome-file.yml>
```

	
- Questo comando **creerà il Replica Controller**
- che a sua volta **creerà i pod usando il file di configurazione**



***
## 7. Creare un Replica Set tramite YAML
Simile al Replica Controller ma con alcune differenze:

1) **apliVersion**: apps/v1
2) [[SELECTOR]]: Un file di definizione per un replica set richiede un **selector** (figlio di **spec**). Questa sezione aiuta il replicaSet a identificare **quali pods ricadono sotto di essa**
   
   Ma perchè dovrei specificare quali pod rientrano se abbiamo 
   fornito la loro definizione nel template?
   Perchè, come detto, i **replicaSett** possono anche **gestire pods che non sono stati creati come parti del ReplicaSet stesso**.
   
   **selector** ci consentirà di gestire tutti i pods che corrispondono alle etichette specificate
 

![[Pasted image 20241120122816.png]]

- **matchLabels**: abbina le etichette specificate alle etichette dei pods



***
## 8. Labels and Selectors
Perchè etichettiamo i pods e gli oggetti in Kubernetes?

Supponiamo di avere distribuito 3 istanze della nostra applicazione Web front-end in 3 pods.
Vogliamo a questo punto creare un Replica controller o un ReplicaSet per assicurarci di avere questi tre pods sempre attivi.

Ora, come fa il ReplicaSet a sapere quali pods deve monitorare?
Potrebbero infatti essercene centinaia nel cluster che runnano diverse applicazioni.

E' in situazioni come queste che le etichette dei nostri pods risultano utili. Possiamo infatti usarle come *filtro* per il nostro ReplicaSet attraverso la sua sezione *selector/matchLabels*

![[Pasted image 20241120124251.png]]




***
## 9. Scale
Questi strumenti offrono un modo veloce per scalare la nostra applicazione. Possiamo farlo in diversi modi:


1) Modificare il campo **replicas** nel file di configurazione yaml
   **replicas**: 3 --> **replicas**: 6
   
   una volta modificato uso il comando **k replace**

2) Usare il comando **k scale --replicas=nuovo_numero**


***
## Comandi

#### Creare
1.
```bash
kubectl create -f <nome-file.yml>
```

- Questo comando **creerà il Replica Controller** o un **replicaset**
- che a sua volta **creerà i pod usando il file di configurazione**


#### Elencare 
2.
```bash
kubectl get replicationcontroller
```

- elenca tutti i **Replication Controller** (RC) presenti nel cluster Kubernetes.
- Avrò in output i seguenti campi:
	- **`NAME`**: Nome del Replication Controller.
	- **`DESIRED`**: Numero desiderato di repliche (specificato nel file YAML).
	- **`CURRENT`**: Numero attuale di pod creati dal Replication Controller.
	- **`READY`**: Numero di pod pronti (stato "Running" e pronti a servire il traffico).
	- **`AGE`**: Da quanto tempo esiste il Replication Controller.

2.1
```bash
kubectl get replicaset
```
- elenca tutti i **replicaSet** presenti nel cluster Kubernetes.



#### Aggiornare / Scalare
3.
```bash
kubectl replace -f <file.yml>
```
- viene utilizzato per **aggiornare una risorsa esistente** in Kubernetes con una nuova configurazione definita in un file YAML. È particolarmente utile quando vuoi apportare modifiche a una risorsa senza cancellarla e ricrearla manualmente.


#### Scalare
4.
```bash
kubectl scale --replicas=<nuovo-numero> -f <file.yml>
```
- utilizzato per **scalare orizzontalmente una risorsa** in Kubernetes, come un **Deployment**, un **ReplicaSet**, o un **ReplicationController**, modificando il numero di repliche desiderate. Questo comando aggiorna solo il campo `replicas` senza influire su altre configurazioni.
  
-  Nota: Questo comando non modifica il valore replicas nel file YAML. CIoè **la modifica viene applicata solo nel cluster Kubernetes**, ma **non viene scritta nel file YAML locale**.
	-  Il file YAML contiene la configurazione iniziale della risorsa. Se **modifichi il numero di repliche nel file YAML** e poi applichi la configurazione con `kubectl apply`, la modifica sarà **persistente** nel file.
	- Se **scali una risorsa** con `kubectl scale`, ma **non aggiorni manualmente il file YAML**, la configurazione di quel file non rifletterà le modifiche apportate nel cluster.
	- Perchè?:
	  Il comando `kubectl scale` è pensato per essere un'operazione temporanea o rapida nel cluster, che modifica solo lo stato della risorsa, senza cambiare il file YAML originale.
  
4.1
```bash
kubectl scale --replicas=<nuovo-numero> replicaset <nome-replicaset>
```
- Scala **direttamente un ReplicaSet esistente** specificandone il nome. È un metodo rapido per modificare il numero di pod desiderati senza dover aggiornare un file YAML.
	- **replicaset** : tipo di oggetto da scalare 

- Nota: Anche in questo caso il comando **scale** non aggiorna il valore di **replicas** nel file YAML



#### Cancellare
5.
```bash
kubectl delete replicaset <nome-replicaset>
```
- viene utilizzato per **eliminare un ReplicaSet specificato** nel cluster Kubernetes. Quando esegui questo comando, **tutti i pod** gestiti dal `ReplicaSet` verranno **terminati** e il `ReplicaSet` stesso verrà **rimosso**.