I Kubernetes controllers sono componenti fondamentali nel sistema Kubernetes, responsabili di mantenere lo stato desiderato delle risorse nel cluster. 

Sono i processi che monitorano gli oggetti di Kubernetes e rispondono di conseguenza.

Ogni controller si occupa di monitorare lo stato attuale di una risorsa e di apportare modifiche per allinearlo con lo stato desiderato, definito dagli oggetti del cluster. Questa attività è un'applicazione pratica del pattern **Control Loop**, che opera continuamente per garantire il funzionamento corretto del sistema.

Ecco un dettaglio sui controller più comuni:

---

### **1. [[REPLICASET]] Controller**

- **Scopo**: Garantisce che un determinato numero di repliche di un'applicazione siano sempre in esecuzione.
- **Funzionamento**:
    - Controlla quante istanze del pod specificato esistono.
    - Se il numero corrente è inferiore al desiderato, crea nuovi pod.
    - Se è superiore, termina i pod in eccesso.
- **Esempio di utilizzo**: Mantenere alta disponibilità per un'applicazione.

---

### **2. Deployment Controller**

- **Scopo**: Gestire aggiornamenti e rollback delle applicazioni in modo sicuro.
- **Funzionamento**:
    - Crea o aggiorna un oggetto ReplicaSet sottostante.
    - Coordina aggiornamenti graduali dei pod.
    - Permette di ritornare facilmente a una versione precedente in caso di problemi.
- **Esempio di utilizzo**: Deploy di una nuova versione dell'app con zero downtime.

---

### **3. StatefulSet Controller**

- **Scopo**: Gestire applicazioni stateful, cioè che richiedono un’identità persistente per i pod.
- **Funzionamento**:
    - Garantisce un ordinamento specifico nella creazione, aggiornamento e terminazione dei pod.
    - Assegna volumi persistenti che restano associati ai pod anche dopo il riavvio.
- **Esempio di utilizzo**: Database come MongoDB o applicazioni con configurazioni legate al nome del pod.

---

### **4. DaemonSet Controller**

- **Scopo**: Garantire che una copia di un pod sia in esecuzione su tutti (o su un sottoinsieme) dei nodi.
- **Funzionamento**:
    - Distribuisce automaticamente i pod a ogni nodo man mano che questi vengono aggiunti al cluster.
    - Rimuove i pod dai nodi eliminati.
- **Esempio di utilizzo**: Raccolta di log, monitoraggio con agenti come Prometheus Node Exporter.

---

### **5. Job Controller**

- **Scopo**: Esegue compiti una tantum (batch jobs) e ne garantisce il completamento.
- **Funzionamento**:
    - Lancia uno o più pod per svolgere il lavoro.
    - Considera il job completato solo quando i pod hanno terminato correttamente.
- **Esempio di utilizzo**: Elaborazioni dati o backup.

---

### **6. CronJob Controller**

- **Scopo**: Pianificare ed eseguire job a intervalli di tempo regolari.
- **Funzionamento**:
    - Si basa su Job per lanciare i pod, ma lo fa seguendo un orario specificato con sintassi cron.
- **Esempio di utilizzo**: Attività schedulate come l'invio di report giornalieri.

---

### **7. Horizontal Pod Autoscaler (HPA)**

- **Scopo**: Adatta dinamicamente il numero di repliche di un’applicazione in base al carico.
- **Funzionamento**:
    - Monitora metriche come utilizzo CPU/memoria.
    - Aumenta o riduce le repliche di un’applicazione per garantire prestazioni e ottimizzare i costi.
- **Esempio di utilizzo**: Gestione del carico durante picchi improvvisi di traffico.

---

### **8. Node Controller**

- **Scopo**: Gestire lo stato dei nodi.
- **Funzionamento**:
    - Identifica nodi non funzionanti.
    - Marca i nodi problematici e, se necessario, rimuove i pod che vi risiedono per consentire il rischeduling su altri nodi.
- **Esempio di utilizzo**: Tolleranza ai guasti nei nodi.

---

### **Come lavorano i controllers?**

1. **Monitoraggio**:
    - Usano l’API Server per osservare lo stato corrente delle risorse.
2. **Confronto**:
    - Confrontano lo stato corrente con lo stato desiderato specificato negli oggetti YAML/JSON del cluster.
3. **Correzione**:
    - Apportano modifiche (es. creare, eliminare o aggiornare risorse) per allineare il cluster allo stato desiderato.