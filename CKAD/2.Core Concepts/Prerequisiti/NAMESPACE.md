
# Table of contents
- [Cosa sono i Namespace](#Cosa sono i Namespace)
- [Scenari di utilizzo](#**Scenari di utilizzo**)
- [Namespace predefiniti](#Namespace predefiniti)
- [Namespace Custom](# Namespace Custom)
- [Namespace Policies](#Namespace policies)
- [Chiamare le risorse](#Chiamare le risorse)
- [Comandi](#comandi)

***
## Cosa sono i Namespace

In Kubernetes, un **Namespace** è un meccanismo logico per isolare e organizzare le risorse all'interno di un cluster. È particolarmente utile quando si gestiscono ambienti complessi, con più team o applicazioni, per separare e amministrare le risorse in modo indipendente.

- Un Namespace è come un "contenitore virtuale" all'interno del cluster Kubernetes.
- Ogni Namespace ha un proprio spazio per oggetti come pod, servizi, deployment, ecc.
- Serve per evitare conflitti di nome tra risorse e per fornire un controllo granulare su risorse condivise.



***
## **Scenari di utilizzo**

1. **Isolamento per Team o Progetti**
    - Se diversi team lavorano sullo stesso cluster, ciascuno può avere un Namespace dedicato per evitare interferenze.
    - Esempio: `team1-namespace`, `team2-namespace`.
      
2. **Separazione degli Ambienti**
    - Si possono definire Namespace diversi per ambienti di sviluppo, staging e produzione.
    - Esempio: `dev`, `staging`, `prod`.
      
3. **Gestione delle Risorse**
    - I Namespace aiutano a gestire quote di risorse come CPU e memoria per evitare che un team utilizzi tutto.




***
## **Namespace predefiniti**
Kubernetes crea alcuni Namespace di default:

- `default`: il Namespace di default per risorse che non specificano un Namespace.
  Ogni volta che creiamo un cluster senza un namespace specifico a questo verrà assegnato il namespace *default*.
  
- `kube-system`: utilizzato per i componenti interni di Kubernetes, come il controller-manager o l'API server.
  Viene creato automaticamente e risulta separato dal *default* per isolarlo dallo user ed evitare, ad esempio, cancellazioni errate etc
  
- `kube-public`: un Namespace leggibile da tutti, utile per informazioni pubbliche come configurazioni.
  Anche questo namespace viene creato automaticamente da Kubernetes per contenere le risorse che devono essere a disposizione dell'utente 
  
- `kube-node-lease`: usato per la gestione dell'heartbeat dei nodi.


![[Pasted image 20241122111612.png]]




***
## Namespace Custom
Fino a quando il mio cluster rimane piccolo non è necessario preoccuparsi del namespace. Al crescere del mio ambiente avrò bisogno di differenziare ed isolare le diverse sezioni di lavoro.

Ad esempio se voglio usare lo stesso cluster per la parte di sviluppo e produzione mantenendo separate le risorse, posso creare due diversi namespaces. In questo modo, lavorando nell'ambiente di sviluppo, non potrò accidentalmente modificarle nella parte di produzione

![[Pasted image 20241122112759.png]]




***
## Namespace Policies & Quota
Ogniuno di questi namespaces può avere un proprio insieme di *policies* che definiscono che può fare cosa.

E' inoltre possibile assegnare una quota di risorse a ciascun namespace, in modo che risultino garantite e non ne venga superato il limite

![[Pasted image 20241122113742.png]]




***
## Chiamare le risorse
#### 1. All'interno dello stesso namespace
Per *comunicare* fra loro, le risorse all'interno dello stesso namespace possono chiamarsi utilizzando semplicemente il nome.

Supponiamo ad esempio di avere una web-app pod ed un db-service.
Il primo potrà chiamare il secondo utilizzando il suo host name

![[Pasted image 20241122115319.png]]



#### 2. Tra diversi namespaces - DNS
Per *comunicare* fra loro, le risorse appartenenti a diversi namespace dovranno essere seguite dal nome del namespace a cui appartengono

![[Pasted image 20241122115607.png]]

In questo esempio, per collegare la web app appartenente a *default* con il db service appartenente a *dev* dovrò scrivere:

```bash
mysql.connect("db-service.dev.svc.cluster.local")
```

E' possibile farlo perche quando il servizio viene creato, una voce [[2.Core Concepts/Prerequisiti/DNS]] è aggiunta automaticamente.

- CLUSTER.LOCAL: Osservando il nome del DNS sel servizio, l'ultima parte rappresenta il nome dominio predefinito del cluster Kubernetes.

- SVC: rappresenta il sottodominio del servizio

- DEV: rappresenta il namespace

- DB-SERVICE: è il nome del servizio


![[Pasted image 20241122120521.png]]





***
***
## Comandi
#### 1.**Visualizzare i Namespace**
```bash
kubectl get namespaces
```


#### 2. **Creare un Namespace**
```bash
kubectl create namespace mio-namespace
```


#### 2. 1 **Creare un Namespace con un file YAML**
![[Pasted image 20241122131752.png]]

- **KInd** = Namespace

Dopo aver scritto il file:
```bash
kubectl create -f <file.yml>
```


#### 3. **get**
1. Elenco i pod nel namespace *default*
```bash
kubectl get pods
```

2. Elencare i pod in un dato namespace
```bash
kubectl get pods --namespace=<nome-namespace>
```



#### 4. **Creare risorse da un file YAML in un namespace specifico**

1. Creare un pod da YAML in un dato namespace
```bash
kubectl create -f <file.yml> --namespace=<nome-namespace>
```

Nota: Se vogliamo essere sicuri che questo pod venga creato nel namespace *nome-namespace* ogni volta senza specificarlo da linea di comando, posso assegnarlo nel file.yml di definizione del pod, **aggiungendo il campo namespace sotto la sezione metadata**

![[Pasted image 20241122131430.png]]



#### 5. **Switch**
Come detto, alla creazione di un cluster ci troveremo nel namespace *default* e per vedere risorse appartenenti ad altri namespaces dovremmo usare la flag **--namespace=nome**

Ma come possiamo spostarci in un altro namespace in modo permanente?


```bash
kubectl config set-context $(kubectl config current-context) --namespace=<nome-namespace>
```

Questo comando viene utilizzato per impostare il **namespace di default** per il contesto Kubernetes attualmente attivo.

- **`kubectl config current-context`**
    - Restituisce il nome del contesto Kubernetes attualmente in uso.
    - È il contesto che stai attualmente utilizzando per interagire con il cluster Kubernetes.
      
- **`kubectl config set-context`**
    - Modifica o aggiorna un contesto esistente nella configurazione di `kubectl`. In questo caso, stai aggiornando il contesto attualmente in uso (ottenuto con il comando precedente).
      
- **`--namespace=<nome-namespace>`**
    - Specifica il namespace predefinito per il contesto. Questo significa che tutti i comandi `kubectl` eseguiti dopo questo comando utilizzeranno automaticamente il namespace indicato, senza dover specificare esplicitamente l'opzione `--namespace`.
      
- **`$(...)`**
    
    - È una sostituzione di comando in bash. Il risultato del comando `kubectl config current-context` viene passato come input a `kubectl config set-context` 




#### 6. **--all**
```bash
kubectl get pods --all-namespaces
```

- Questo comando elenca i pod attivi in tutti i namespaces presenti 




#### 7. **Creare una Quota** attraverso un file YAML
Per assegnare/limitare le risorse in un namespace possiamo creare una *Quota di risorse*. Con il tipo

- **Kind** = ResorceQuota

E sotto spec aggiungerò le specifiche

![[Pasted image 20241122133623.png]]





#### 8. **Usare un Namespace specifico**
1. Aggiungere il flag `-n` per operare in un Namespace specifico:
    
   ```bash
    `kubectl get pods -n mio-namespace`
    ```
    
2. Cambiare un pod in una namespace
    
```bash
    `kubectl run <nome-pod> --image=<nome-img> -n <nome-nampespace>
```



#### 9.**Eliminare un Namespace**
```bash
`kubectl delete namespace mio-namespace`
```