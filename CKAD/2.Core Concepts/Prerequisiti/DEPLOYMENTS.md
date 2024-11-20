
***
# Table of contents
- [0. Overview](#0. Overview)
- [1. Definizione ](#1. Definizione )
- [2.Creare un Deployment attraverso un file YAML](#2.Creare un Deploument attraverso un file YAML)
- [Comandi](#COmandi)


***
## 0. Overview
Vediamo come distribuire la nostra applicazione in un ambiente di produzione.

Supponiamo di avere un server web che deve essere distribuito.

- Per ovvie ragioni necessiteremo di diverse istanze di questo server in esecuzione.
- Ogni volta che una nuova versione dell'applicazione diventa disponibile nel registro di docker, vogliamo poi poter **aggiornare** queste istanze. 
  Tutavia non andremo ad aggiornale simultaneamente, in quanto questo avrebbe una ricaduta sugli utenti, ma una alla volta.
  Questo tipo di aggiornamento è detto **roling update**
- Supponiamo poi che nell'ultimo aggiornamento ci sia un bug e ne venga richiesto l'annullamento delle modifiche. 
  In questi casi vogliamo poter tornare alle versioni precedenti.
- Infine, se ad esempio di desidera apportare modifiche all'anbiente (come l'aggiornamento delle versioni del web server sottostanti, il ridimensionamento dell'ambiente, la modifica dell'allocazione delle risorse etc), non vogliamo che vengano applicate subito dopo l'esecuzione del comando. Si preferisce "mettere in pausa" l'ambiente, apportare le modifiche e poi ripendere, in modo che tutte le modifiche vengano distribuite insieme

![[Pasted image 20241120155224.png]]

Tutte queste funzionalità sono disponibili nel **deploiment** di Kubernetes.



### 0.1 Gerarchia delle distribuzioni 
- Abbiamo visto che i [[PODS]] si occupano di distribuire singole istanze della nostra applicazione.
  
- Ogni container viene incapsulato all'interno di un pod
  
- I vari pod dello stesso tipo vengono distribuiti dal [[REPLICASET]] ( o dal replication controller)
  
- Ad un livello superiore nella gerarchia troviamo il **deployment**:
  Questo ci fornisce tutte le capacità descritte sopra

![[Pasted image 20241120161122.png]]


***
## 1. Definizione 
In Kubernetes, il **Deployment** è un oggetto di alto livello che permette di gestire in modo dichiarativo il ciclo di vita delle applicazioni containerizzate. In pratica, è uno strumento per assicurarti che la tua applicazione venga distribuita, aggiornata e gestita automaticamente con affidabilità e flessibilità.

È uno dei controller più comuni e utili in Kubernetes, poiché garantisce che il numero desiderato di pod sia sempre in esecuzione e ti permette di effettuare aggiornamenti o rollback in modo controllato.9


### 1.1 Cosa fa un Deployment?
- **Distribuisce i container**: Configura e avvia una o più istanze (repliche) dei container dell'applicazione.
- **Gestisce lo stato desiderato**: Kubernetes controlla che il numero di repliche specificato nel Deployment sia sempre attivo e funzionante.
- **Esegue aggiornamenti**: Supporta aggiornamenti continui senza interruzioni (rolling update) o aggiornamenti drastici (recreate).
- **Fornisce rollback automatico**: Se un aggiornamento fallisce, puoi tornare alla versione precedente.
- **Ridimensiona le applicazioni**: Puoi scalare orizzontalmente (aumentare o diminuire il numero di repliche) con un semplice comando o automaticamente tramite un autoscaler.




***
## 2. Creare un Deployment attraverso un file YAML
Il file di definizione di un Deployment risulta estremamente simile a quello di un [[REPLICASET]] tranne per:

- **kind**: Deployment

![[Pasted image 20241120161716.png]]

### Componenti principali del Deployment
1. **`metadata`**: Contiene informazioni come il nome del Deployment e le label associate.
2. **`spec.replicas`**: Specifica il numero di pod che devono essere in esecuzione.
3. **`spec.selector`**: Definisce i pod da gestire in base alle label.
4. **`spec.template`**: Descrive il template per i pod da creare.
5. **`spec.strategy`** (opzionale): Specifica la strategia di aggiornamento (es. rolling update o recreate).




***
## Comandi

#### Creare
1.
```bash
kubectl create -f <nome-file.yml>
```

- Questo comando **creerà il Deployment**
	- Il deployment andrà automaticamente a creare il **replicaSet**
		- che a sua volta **creerà i pod usando il file di configurazione**


#### Elencare 
2.
```bash
kubectl get deployments
```

- utilizzato per ottenere una lista dei Deployment attivi nel cluster Kubernetes. Mostra informazioni essenziali come il nome del Deployment, lo stato delle repliche e altre metriche utili.
  
- Avrò in output i seguenti campi:
	- **NAME**: Nome del Deployment.
	- **READY**: Mostra il numero di pod pronti rispetto al numero totale richiesto. Esempio: `3/3` indica che tutti e tre i pod sono in esecuzione e pronti.
	- **UP-TO-DATE**: Numero di pod aggiornati secondo la configurazione attuale del Deployment.
	- **AVAILABLE**: Numero di pod disponibili per gestire il traffico (cioè, pronti a ricevere richieste).
	- **AGE**: Tempo trascorso dalla creazione del Deployment.



2.1
```bash
kubectl get all
```
- Considerando che la creazione di un Deployment implica l'instanziamento di **ReplicaSet** e **Pods**, possiamo elencare tutti gli oggetti creati con questo comando


