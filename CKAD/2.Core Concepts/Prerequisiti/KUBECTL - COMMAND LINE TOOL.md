***

# Table oìf contents
- [Cos'è kubectl](#Cos'è kubectl)
- [Cosa fa kubectl ?](# Cosa fa kubectl ?)
- [Come funziona kubectl?](#Come funziona kubectl?)
§
- [Sintassi](#SIntassi)
- [-o flag](# -o flag)
- [Comandi comuni di kubectl:](#Comandi comuni di kubectl:)
§
- [Imperative Commands](#Imperative Commands)
- [Imperative Commands vs. Declarative Configuration](# Imperative Commands vs. Declarative Configuration)
- [--dry-run](#--dry-run)
  


***
# Cos'è kubectl
- **`kubectl`** è la riga di comando ufficiale di Kubernetes che permette agli utenti di interagire con il cluster. 
- Attraverso `kubectl`, gli amministratori e gli sviluppatori possono gestire, monitorare e configurare i vari componenti del cluster Kubernetes.
***

## Cosa fa kubectl ?
`kubectl` consente di eseguire operazioni come:

- **Creare e gestire risorse**: creare, aggiornare o eliminare risorse di Kubernetes come Pod, Deployments, Services, ConfigMaps, ecc.
- **Controllare lo stato del cluster**: ottenere informazioni sullo stato delle risorse e sui nodi del cluster.
- **Interagire con i Pod**: avviare shell interattive, eseguire comandi, visualizzare log, e fare debug dei Pod.
- **Gestire le configurazioni**: applicare file di configurazione [[YAML]] o [[JSON]] al cluster.


***

## Come funziona kubectl?
`kubectl` si connette all'**API server** del cluster per eseguire operazioni. L'API server è il punto di ingresso principale per la gestione del cluster, quindi ogni comando inviato da `kubectl` viene elaborato attraverso di esso. La connessione al cluster avviene tramite le informazioni di configurazione presenti nel file `kubeconfig` (spesso situato in `~/.kube/config`), che contiene dettagli come:

- Gli endpoint del server API.
- Le credenziali per l'autenticazione.
- Le informazioni di contesto che specificano quale cluster gestire, quale utente usare, ecc







***
***
***
***




# SINTASSI
***
## Fonti
- https://kubernetes.io/docs/reference/kubectl/
- https://kubernetes.io/docs/reference/kubectl/quick-reference/

***

```BASH
kubectl [command] [TYPE] [NAME] [flags]
```

dove:
- `command`: Specifica l'operazione da eseguire su una o più risorse
  (es. `create`, `get`, `describe`, `delete`)
- `TYPE`: Specifica il tipo di risorsa
- `NAME`: Specifica il nome della risorsa. Se il nome viene omesso verranno mostrate le secifiche per tutte le risorse 
- `flags`:  Specifica flags opzionali

***
## -o flag

La flag -o permette di mostrare dettagli della risorsa in diversi formati

```BASH
kubectl [command] [TYPE] [NAME] -o <output_format>
```

Here are some of the commonly used formats:
1. `-o json`Output a JSON formatted API object.
    
2. `-o name`Print only the resource name and nothing else.
    
3. `-o wide`Output in the plain-text format with any additional information.
    
4. `-o yaml`Output a YAML formatted API object.
   
5. `-o wide ` Probably the most common format used to print additional details about the object



***
## Comandi comuni di kubectl:
- **`kubectl run`**: è usato per distribuire (deploy) un'applicazione nel cluster
	Cioè viene utilizzato per creare ed eseguire rapidamente un Pod o un insieme di Pod nel cluster Kubernetes. È una delle modalità per avviare applicazioni containerizzate in modo semplice
	
	Esempio:
	```BASH
	kubectl run <nome_pod> --image=<nome_immagine> [opzioni]
	```
	Questo comando crea un Pod chiamato `nginx-pod` che esegue un container basato 
	sull'immagine `nginx`.
	
	 **Opzioni Comuni**:
	- **`--image`**: specifica l'immagine del container da eseguire.
	- **`--port`**: espone una porta specifica del container.
	- **`--env`**: imposta variabili d'ambiente nel container, ad esempio `--env="MY_VAR=my_value"`.
	- **`--replicas`**: imposta il numero di repliche (anche se questa opzione è più usata per i Deployment).
	- **`--command`**: indica che il comando specificato deve essere eseguito come comando principale del container.




- **`kubectl cluster-info`**: è utilizzato per visualizzare informazioni di base sul cluster Kubernetes a cui sei connesso. Fornisce dettagli sulle componenti principali del cluster, come l'API server e i servizi di base, per aiutarti a confermare che la connessione al cluster sia attiva e che le componenti principali siano operative.
 

 
- **`kubectl get`**: è uno dei comandi più usati in Kubernetes ed è fondamentale per visualizzare informazioni sui vari oggetti presenti nel cluster. Consente di elencare risorse come pod, servizi, deployment, nodi e altre componenti in un formato leggibile.
  
  **Principali Tipi di Risorse**:
  - **`nodes`**: Elenca i nodi del cluster. (es: `kubectl get nodes`)
  - **`pods`**: Mostra tutti i pod nel namespace corrente.
  - **`services`**: Elenca tutti i servizi.
  - **`deployments`**: Visualizza tutti i deployment.









***
***
***
***





# Imperative Commands
In Kubernetes, il termine **"imperative commands"** si riferisce ai comandi che vengono eseguiti **direttamente dalla riga di comando** per interagire con il cluster e creare, modificare o eliminare risorse. Questi comandi agiscono immediatamente e **non richiedono** una definizione preliminare tramite file di configurazione come accade con i comandi **dichiarativi**.

# Fonti
https://kubernetes.io/docs/reference/kubectl/conventions/

***
## **Imperative Commands vs. Declarative Configuration**
1. **Imperative Commands**:  
    Questi comandi sono immediati e operano direttamente sullo stato corrente del cluster. Vengono usati quando desideri fare modifiche rapide e pratiche senza dover creare file YAML complessi. Ad esempio, se vuoi creare un pod o un deployment senza definire un file di configurazione, puoi farlo direttamente usando un comando imperativo.
    
2. **Declarative Configuration**:  
    Si riferisce all'uso di file YAML o JSON che descrivono lo stato desiderato delle risorse nel cluster. Con i comandi dichiarativi, Kubernetes si occupa di applicare il tuo stato desiderato.


***

## 3 --dry-run

```BASH
--dry-run
```

La flag `--dry-run` ti permette di verificare un comando Kubernetes senza modificarne lo stato reale nel cluster. In pratica, è uno strumento di test che simula l'esecuzione del comando e ti dice:

1. **Se il comando è scritto correttamente**.
2. **Se la risorsa che vuoi creare/modificare/eliminare è valida**.

Non può essere usato da solo

## **Due modalità principali di `--dry-run`**
1. **`--dry-run=client`** (modalità client):
    - Tutta la validazione avviene localmente sul tuo computer, senza inviare alcuna richiesta al server Kubernetes.
    - È utile per verificare errori sintattici o di configurazione prima di interagire con il cluster.
    - 
1. **`--dry-run=server`** (modalità server):
    - Il comando viene inviato al server Kubernetes, ma la risorsa non viene creata.
    - Serve a verificare che il server accetti la richiesta e che le configurazioni siano valide secondo lo stato attuale del cluster.




### **Quando utilizzare `--dry-run`**
- **Test di configurazioni YAML**: Puoi verificare che i tuoi file YAML siano validi senza effettivamente creare risorse.
- **Debugging di comandi**: Puoi controllare se un comando è scritto correttamente prima di eseguirlo.
- **Controllo della validità**: È utile per capire se una risorsa può essere creata/modificata con le regole attuali del cluster.




### **Esempi pratici**
#### **1. Creare un pod senza crearlo davvero**
```BASH
kubectl run test-pod --image=nginx --dry-run=client
```

- Questo comando verifica che un pod con nome `test-pod` e immagine `nginx` possa essere creato.
- Non crea effettivamente il pod, ma conferma che il comando è valido.

#### **2. Generare la definizione della risorsa in formato YAML:   *-o yaml*
Puoi combinare `--dry-run` con `-o yaml` per ottenere un file YAML della risorsa:

```BASH
kubectl run test-pod --image=nginx --dry-run=client -o yaml
```

Nota: Questa combinazione, insieme alle redirection di linux, mi consente di creare facilmente file.yml di configurazione per le mie risorse.
es:
```BASH
kubectl run nginx --image=nginx --dry-run=client -o yaml > nginx-pod.yaml
```





## CREAZIONI DI RISORSE / FILE YAML
### 1. POD
**Creare un NGINX Pod**
```bash
kubectl run nginx --image=nginx
```

**Generare un file YAML Manifest per il pod(-o raml) senza crearlo(--dry-run)**
```bash
kubectl run nginx --image=nginx --dry-run=client -o yaml
```




### 2. DEPLOYMENT
**Creare un deployment**
```bash
kubectl create deployment --image=nginx nginx
```

**Generare un file YAML del deployment (-o yaml) senza crearlo(--dry-run)**
```bash
kubectl create deployment --image=nginx nginx --dry-run -o yaml
```





### 3. REPLICAS
**Creare un Deployment con 4 Replicas**
```bash
kubectl create deployment nginx --image=nginx --replicas=4
```

Posso anche scalare il Deployments usando **k scale**
```bash
kubectl scale deployment nginx --replicas=4
```


**Creare la definizione in un file YAML per poi modificarlo**
```bash
kubectl create deployment nginx --image=nginx`--dry-run=client -o yaml > nginx-deployment.yaml`
```

A questo punto posso modificare il campo replicas nel file





### 4. SERVICE
**Creare un Servizio chiamato redis-service di tipo ClusterIP per aprire il pod sulla porta 6379**

```bash
kubectl expose pod redis --port=6379 --name redis-service --dry-run=client -o yaml
```

Quando usi il comando **`kubectl expose`**, Kubernetes **prende automaticamente i label del pod specificato (`redis`) e li usa come selectors per il Service**. Questo significa che il Service sarà configurato per instradare il traffico al pod chiamato `redis`, senza necessità di modifiche manuali ai selectors.


Oppure:

```bash
kubectl create service clusterip redis --tcp=6379:6379 --dry-run=client -o yaml
```
genera un file YAML per un Service, ma **non imposta automaticamente i selettori (selectors) in base ai label dei pod associati**. Per impostazione predefinita, Kubernetes assegnerà ai selettori il valore `app=redis`, indipendentemente dai label reali presenti nei pod. Questo può causare problemi se i tuoi pod hanno label diverse.


Confronto:
1. **`kubectl expose`**
- **Pro**: Usa automaticamente i labels del pod come selectors, semplificando l'associazione.
- **Contro**: Non permette di specificare direttamente la porta NodePort; richiede modifiche manuali al file YAML.

2. **`kubectl create service clusterip`**
- **Pro**: Configurazione dettagliata delle porte e protocolli.
- **Contro**: Non associa automaticamente il Service ai Pod; selectors da aggiungere manualmente.




**Creare un servizio chiamato nginx del tipo NodePort per aprore la porta 80 del pod nginx solla porta 30080 del nodo**
```bash
kubectl expose pod nginx --port=80 --name nginx-service --type=NodePort --dry-run=client -o yaml
```

genera un file YAML per un Service di tipo **NodePort** che espone il pod `nginx` sulla porta 80.
Con il comando `kubectl expose`, **non puoi specificare direttamente la porta NodePort (es. 30080)**. Kubernetes assegnerà una porta casuale nell'intervallo predefinito 30000–32767. 
Dovrò quindi poi modificare il file YAML

Oppure:

```bash
kubectl create service nodeport nginx --tcp=80:80 --node-port=30080 --dry-run=client -o yaml
```
genera un file YAML per creare un **Service** di tipo **NodePort**

**Questo comando non usa i label del pod come selectors**, il che significa che il Service non sarà collegato automaticamente al pod `nginx`. Non potrà quindi indirizzare il traffico al pod senza ulteriori modifiche manuali al file YAML.



Confronto:
1. **`kubectl expose`**
- **Pro**: Utilizza automaticamente i label del pod come selectors, semplificando l'associazione tra Service e Pod.
- **Contro**: Non permette di specificare direttamente la porta NodePort. È necessario generare un file YAML, modificarlo manualmente per aggiungere il campo `nodePort`, e applicarlo successivamente.

2. **`kubectl create service nodeport`**
- **Pro**: Permette di specificare direttamente il NodePort, evitando modifiche manuali.
- **Contro**: Non associa automaticamente il Service ai Pod tramite i loro label; bisogna aggiungere i selectors manualmente nel file YAML generato.