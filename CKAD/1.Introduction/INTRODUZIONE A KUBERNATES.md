Kubernetes è una piattaforma open-source progettata per automatizzare la gestione, il deployment e il ridimensionamento delle applicazioni containerizzate. È stata originariamente sviluppata da Google e ora è mantenuta dalla Cloud Native Computing Foundation (CNCF).

# Concetti chiave di Kubernetes:
1. **[[CONTAINER]] e Docker**: Kubernetes si basa sull'idea di container, che sono unità leggere e portatili per  l'esecuzione delle applicazioni. Sebbene Docker sia uno degli strumenti più noti per la creazione di container, Kubernetes è indipendente da Docker e può orchestrare container creati con altri runtime. 
   
2. **[[CLUSTER]]**: Un cluster Kubernetes è composto da un insieme di **nodi**. 
   Un nodo può essere una macchina virtuale o fisica e ospita i container. Ogni cluster ha un _master node_ (chiamato anche _control plane_) e uno o più _worker nodes_.
   
3. **[[NODE]]**: I nodi sono le macchine (fisiche o virtuali) che eseguono le applicazioni containerizzate. Ogni nodo esegue un **agent** chiamato `kubelet`, responsabile della gestione dei container e della comunicazione con il _control plane_ (master)
   
   Nota: Anche i nodi master possono avere un kubelet, tuttavia, il suo ruolo sui nodi master non è orientato alla gestione dei carichi di lavoro delle applicazioni, ma piuttosto alla gestione di componenti di sistema.
   
4. **[[POD]]**: Un _pod_ è la **più piccola unità eseguibile in Kubernetes**. Rappresenta uno o più container che condividono lo stesso spazio di rete e risorse di archiviazione. I container in un pod lavorano insieme come un'unica applicazione e possono comunicare tramite `localhost`.
   
5.  **[[REPLICASET]] e [[DEPLOYMENT]]**: Un _ReplicaSet_ garantisce che un numero specifico di repliche di un pod siano in esecuzione in ogni momento. Un _Deployment_ utilizza un ReplicaSet per semplificare il deployment e l'aggiornamento dei pod, gestendo lo scaling e le sostituzioni automatiche dei pod in caso di errore.
   
6. **[[SERVICE]]**: Un _service_ espone un set di pod come servizio di rete unico. Fornisce un endpoint stabile per accedere ai pod, anche se l'indirizzo IP del pod cambia. I _service_ facilitano la comunicazione tra i vari componenti di un'applicazione
   
7. **[[CONFIGMAP]] e [[SECRET]]**: _ConfigMap_ viene utilizzato per gestire configurazioni senza doverle modificare nei pod, mentre _Secret_ serve per gestire informazioni sensibili come credenziali o chiavi API in modo più sicuro rispetto ai ConfigMap.
   
8. **[[INGRESS]]**:  Un _ingress_ è un oggetto di Kubernetes che gestisce l'accesso esterno ai servizi del cluster, solitamente tramite HTTP o HTTPS. Permette di configurare regole per il bilanciamento del carico e la gestione di nomi di dominio.
   
9. **[[NAMESPACE]]**: I _namespace_ consentono di dividere un cluster Kubernetes in ambienti virtuali separati, utili per isolare le risorse tra team o progetti.

# Vantaggi di Kubernetes:
- **Scalabilità automatica**: Aumenta o riduce il numero di pod in base al carico di lavoro.
- **Gestione del deployment**: Permette aggiornamenti senza downtime con funzionalità come i rolling update.
- **Tolleranza ai guasti**: Rimpiazza automaticamente i pod danneggiati o non funzionanti.
- **Portabilità**: Kubernetes funziona su cloud pubblici, privati e ambienti ibridi, rendendo le applicazioni più portatili.