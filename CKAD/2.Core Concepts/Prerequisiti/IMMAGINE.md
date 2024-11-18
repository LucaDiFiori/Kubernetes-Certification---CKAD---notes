***
# Fonti
- https://docs.docker.com/build/concepts/dockerfile/#docker-images
- https://www.techtarget.com/searchitoperations/definition/Docker-image
  
***
# Table of content
- [Struttura di un'immagine Docker](#struttura-di-un'immagine-docker)
- [Differenza fra Dockerfile e Docker image](#Differenza-fra-Dockerfile-e-Docker-image)
- [Dettagli delle Immagini di Container](#dettagli-delle-immagini-di-container)
- [Costruzione di un'immagine tramite Dockerfile](#Costruzione-di-un'immagine-tramite-Dockerfile)
- [Registri delle Immagini (Container Registries)](Registri-delle-Immagini-(Container-Registries))
- [Utilizzo delle Immagini per Creare Container](#Utilizzo-delle-Immagini-per-Creare-Container)
- [Differenze tra Immagini e Container](Differenze-tra-Immagini-e-Container)
- [Differenze tra Immagini e Container]()

***

Un'immagine di container è un **file statico e immutabile** che contiene **tutto il necessario per eseguire un'applicazione, inclusi il codice, le librerie, le dipendenze, le variabili di ambiente, e i comandi di avvio**. 
(es file.iso)

L'immagine è il modello da cui vengono creati i container, e la sua natura immutabile garantisce coerenza e portabilità tra ambienti diversi.
Possiamo immaginarla come un'"istantanea" di un ambiente di esecuzione


***
# Differenza fra Dockerfile e Docker image

## 1. Dockerfile
Un **Dockerfile** è un file di testo che contiene una serie di istruzioni che definiscono come creare un'immagine Docker. È essenzialmente un _template_ o una "ricetta" che Docker utilizza per costruire un'immagine. Ogni istruzione nel Dockerfile specifica un passo che deve essere eseguito durante la creazione dell'immagine, come ad esempio l'installazione di pacchetti, la copia di file o la configurazione di variabili di ambiente.

**Esempio di un Dockerfile**:
```dockerfile
# Usa un'immagine di base di Python
FROM python:3.8

# Imposta la directory di lavoro
WORKDIR /app

# Copia il file requirements.txt nel container
COPY requirements.txt .

# Installa le dipendenze
RUN pip install -r requirements.txt

# Copia l'applicazione nel container
COPY . .

# Definisce il comando da eseguire quando il container si avvia
CMD ["python", "app.py"]
```
**In sintesi**: Il Dockerfile è il **file di configurazione** che definisce come costruire l'immagine.

## 2. Docker image
Un'**immagine Docker** è il **risultato finale** della costruzione di un Dockerfile. È un'istantanea (snapshot) di un sistema di file e delle configurazioni che sono necessari per eseguire un'applicazione. Le immagini sono leggere, immutabili e possono essere riutilizzate per creare container identici su qualsiasi sistema che esegua Docker.

**L'immagine Docker contiene**:
- Il sistema operativo di base.
- I file dell'applicazione.
- Le dipendenze necessarie.
- La configurazione e le impostazioni definite nel Dockerfile.

Una volta che hai creato un'immagine, puoi usarla per eseguire uno o più container. Gli **immagini Docker** vengono distribuite tramite Docker Hub o altri registry, e possono essere condivise e riutilizzate da altri utenti.

**In sintesi**: L'immagine Docker è un **file eseguibile** che puoi usare per creare container.

### Relazione tra Dockerfile e Docker Image:
1. **Dockerfile** è un _file di configurazione_.
2. **Docker Image** è il _prodotto_ finale costruito da quel file di configurazione.

Quando esegui il comando `docker build` utilizzando un Dockerfile, Docker crea un'immagine che può essere poi utilizzata per creare uno o più container tramite il comando `docker run`.

### Flusso di lavoro:
1. Scrivi un **Dockerfile** con le istruzioni necessarie.
2. Costruisci l'**immagine Docker** eseguendo `docker build`.
3. Esegui un **container** utilizzando l'immagine con `docker run`.

***
# Struttura di un'immagine Docker:
- **[[LAYER]] (Livelli)**: Ogni immagine Docker è composta da più livelli. Ogni livello rappresenta una modifica o un'istruzione eseguita durante il processo di build (ad esempio, l'installazione di una libreria o la copia di file). I layer sono impilati in sequenza per creare l'immagine finale.
    
- **File di manifesto**: L'immagine include un file di _manifesto_, che elenca i layer e i metadati dell'immagine, come il comando di avvio (entrypoint) e le variabili d'ambiente.
    
- **Storage dei layer**: I layer non sono file singoli ma sono rappresentati come _tarball_ (archivio tar) compressi che vengono gestiti separatamente. Questo permette a Docker di riutilizzare i layer comuni tra immagini diverse, migliorando l'efficienza dello spazio di archiviazione.
  
## È un singolo file?
No, un'immagine Docker non è un singolo file. È una collezione di file compressi e metadati che vengono trattati come un'unità. Questa struttura modulare permette a Docker di ottimizzare il download e la memorizzazione delle immagini.


***

# Dettagli delle Immagini di Container
1.  **Composizione a strati (Layered Architecture)**: Le immagini di container sono costruite con un'architettura a strati (_layers_). Ogni istruzione nel file di costruzione dell'immagine (tipicamente un `Dockerfile`) genera un nuovo strato dell'immagine. Ad esempio:
   
    - `FROM ubuntu:latest`: Crea uno strato di base con l'immagine _Ubuntu_.
    -  `RUN apt-get install -y python3`: Aggiunge un nuovo strato che installa Python3.
    - `COPY my_app.py /app/my_app.py`: Aggiunge un altro strato copiando un file nell'immagine.
      
    Questi strati sono immutabili e vengono "impilati" per formare l'immagine completa. L'uso degli strati rende il processo di costruzione più efficiente, poiché i livelli che non cambiano possono essere riutilizzati nelle successive costruzioni, riducendo i tempi di build e l'uso della banda.
    
    Per spiegare questo in dettaglio, costruiamo alcuni container. Si noti che, per correttezza, l'ordinamento dei livelli dovrebbe essere dal basso verso l'alto, ma per facilità di comprensione, consideriamo l'approccio opposto:
    
    ```YAML
    └── container A: solo un sistema operativo di base, come Debian
      └── container B: costruito sopra #A, aggiungendo Ruby v2.1.10
      └── container C: costruito sopra #A, aggiungendo Golang v1.6
    ```
	
	A questo punto abbiamo tre container: A, B e C. 
	B e C sono derivati da A e condividono solo i file del container di base. Andando avanti,  possiamo costruire sopra B aggiungendo Ruby on Rails (versione 4.2.6). Potremmo anche voler supportare una vecchia applicazione che richiede una versione precedente di Ruby on Rails (ad esempio, versione 3.2.x). Possiamo quindi costruire un'immagine di container per supportare quell'applicazione basata su B, con l'intenzione di migrare l'app in futuro alla versione 4:
	
    ```YAML
    #(CONTINUA DA SOPRA)
	└── container B: costruito sopra #A, aggiungendo Ruby v2.1.10         
		└── container D: costruito sopra #B, aggiungendo Rails v4.2.6       
		└── container E: costruito sopra #B, aggiungendo Rails v3.2.x
    ```
    Concettualmente, ogni immagine del container è un livello che si costruisce su uno precedente. Ogni riferimento al genitore è un puntatore.



2.  **Immutabilità**: Un'immagine è immutabile, il che significa che una volta creata non può essere modificata. Questo assicura che tutti i container avviati da un'immagine specifica siano identici. Se sono necessarie modifiche, viene creata una nuova immagine basata su quella esistente con le modifiche applicate.


***

# Costruzione di un'immagine tramite Dockerfile
Un'immagine è tipicamente costruita utilizzando un `Dockerfile`, un file di testo con istruzioni che Docker (o un altro sistema di build compatibile) utilizza per creare l'immagine.

### Esempio: 
Ecco come appare un tipico flusso di lavoro per la creazione di applicazioni con Docker.
Il seguente codice di esempio mostra una piccola applicazione "Hello World" scritta in Python, utilizzando il framework Flask.
```python
from flask import Flask
app = Flask(__name__)

@app.route("/")
def hello():
    return "Hello World!"
```

Per distribuire e implementare questa applicazione senza Docker Build, dovresti assicurarti che:

- Le dipendenze di runtime necessarie siano installate sul server
- Il codice Python venga caricato nel filesystem del server
- Il server avvii la tua applicazione, utilizzando i parametri necessari


Il seguente Dockerfile crea un'immagine del container, che ha tutte le dipendenze installate e avvia automaticamente la tua applicazione.
```dockerfile
# syntax=docker/dockerfile:1
FROM ubuntu:22.04

# install app dependencies
RUN apt-get update && apt-get install -y python3 python3-pip
RUN pip install flask==3.0.*

# install app
COPY hello.py /

# final configuration
ENV FLASK_APP=hello
EXPOSE 8000
CMD ["flask", "run", "--host", "0.0.0.0", "--port", "8000"]
```

***

# Registri delle Immagini (Container Registries)
Le immagini sono memorizzate e distribuite attraverso _registri_ (o _registry_). I registri possono essere pubblici o privati e consentono di gestire le immagini centralmente. Alcuni esempi includono:

- **Docker Hub**: Il registro pubblico più conosciuto e ampiamente utilizzato.
- **Amazon Elastic Container Registry (ECR)**: Un servizio gestito da AWS per le immagini private.
- **Google Container Registry (GCR)**: Simile a ECR ma gestito da Google Cloud.
- **Azure Container Registry (ACR)**: Offerto da Microsoft Azure.


***

# Utilizzo delle Immagini per Creare Container
Quando un container viene avviato da un'immagine, il runtime del container prende l'immagine, la decomprime e la esegue in un ambiente isolato (il container stesso). Il container utilizza il file system dell'immagine, che rappresenta un _layer_ di sola lettura, e crea uno strato di scrittura sopra di esso. Le modifiche effettuate durante l'esecuzione del container sono registrate solo in questo strato di scrittura e non influenzano l'immagine di base.

***

 # Differenze tra Immagini e Container
- **Immagine**: 
	- Un file immutabile che funge da blueprint per creare un container.
- **Container**: 
	- E' un runtime envirorment virtualizzato per le immagini
	- Fornisce il file sistem virtualizzato, envirorment configs etc per poter
	  runnar l'applicazione
	- Un'istanza runtime di un'immagine. 
	- Può essere avviato, arrestato e distrutto. 
	- Qualsiasi modifica fatta al file system durante l'esecuzione del container è temporanea e va persa quando il container viene eliminato (a meno che non si usino volumi).


In sintesi, le immagini di container rappresentano la base su cui si costruiscono i container, offrendo coerenza e portabilità per applicazioni in diversi ambienti di esecuzione.
