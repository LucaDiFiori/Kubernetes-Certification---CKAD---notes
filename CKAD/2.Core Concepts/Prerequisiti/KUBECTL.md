- **`kubectl`** è la riga di comando ufficiale di Kubernetes che permette agli utenti di interagire con il cluster. 
- Attraverso `kubectl`, gli amministratori e gli sviluppatori possono gestire, monitorare e configurare i vari componenti del cluster Kubernetes.
***

### Cosa fa `kubectl`?
`kubectl` consente di eseguire operazioni come:

- **Creare e gestire risorse**: creare, aggiornare o eliminare risorse di Kubernetes come Pod, Deployments, Services, ConfigMaps, ecc.
- **Controllare lo stato del cluster**: ottenere informazioni sullo stato delle risorse e sui nodi del cluster.
- **Interagire con i Pod**: avviare shell interattive, eseguire comandi, visualizzare log, e fare debug dei Pod.
- **Gestire le configurazioni**: applicare file di configurazione [[YAML]] o [[JSON]] al cluster.


***

### Come funziona `kubectl`?
`kubectl` si connette all'**API server** del cluster per eseguire operazioni. L'API server è il punto di ingresso principale per la gestione del cluster, quindi ogni comando inviato da `kubectl` viene elaborato attraverso di esso. La connessione al cluster avviene tramite le informazioni di configurazione presenti nel file `kubeconfig` (spesso situato in `~/.kube/config`), che contiene dettagli come:

- Gli endpoint del server API.
- Le credenziali per l'autenticazione.
- Le informazioni di contesto che specificano quale cluster gestire, quale utente usare, ecc


***

### Comandi comuni di `kubectl`:
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

  

