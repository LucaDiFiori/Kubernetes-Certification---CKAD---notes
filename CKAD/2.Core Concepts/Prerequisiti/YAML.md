YAML è un formato leggibile da esseri umani per rappresentare dati strutturati. È spesso usato per file di configurazione o per scambiare dati tra applicazioni, grazie alla sua semplicità e chiarezza.

***

# Fonti:
- https://www.redhat.com/en/blog/yaml-beginners
- https://www.redhat.com/it/topics/automation/what-is-yaml


***
### 0. SIntassi
- I file YAML presentano un'estensione **.yml** o **.yaml** e rispettano regole di sintassi specifiche.
- YAML unisce funzionalità tratte da Perl, C, XML, HTML e altri linguaggi di programmazione. Inoltre, è un sovrainsieme di JSON, pertanto i file JSON sono validi in YAML.
- Non si usano simboli di formattazione classici come parentesi graffe o quadre, tag di chiusura o virgolette
- Per salvaguardare la portabilità tra i sistemi, i caratteri di tabulazione non sono consentiti per definizione e al loro posto si utilizzano gli **spazi bianchi**
- I commenti sono indicati con il simbolo del cancelletto (#)
- I tre trattini (---) stanno a indicare l'inizio di un documento, mentre la fine viene contrassegnata con tre punti (...).

```yaml
#Comment: This is a supermarket list using YAML
#Note that - character represents the list
---
food: 
  - vegetables: tomatoes #first list item
  - fruits: #second list item
      citrics: oranges 
      tropical: bananas
      nuts: peanuts
      sweets: raisins
```

- La struttura di un file YAML è costituita da una **mappatura** o da una **lista**
- Le **mappature** consentono di associare coppie chiave-valore
- È necessario risolvere una **mappatura** in YAML prima di poterla chiudere e di poterne creare una nuova. È possibile creare una nuova mappatura aumentando il livello di rientro o risolvendo la mappatura precedente
- Una **lista** invece prevede che i valori seguano un determinato ordine e non ci sono vincoli al numero di elementi che si possono aggiungere. Le sequenze cominciano con un trattino (-) e uno spazio, mentre il rientro serve a separare un livello da quello superiore
  
  Nell'esempio riportato in precedenza "vegetables" e "fruits" sono elementi appartenenti alla lista denominata "food".

***

### **1. Caratteristiche principali di YAML**

- **Semplicità**: il formato è facile da leggere e scrivere.
- **Indentazione**: usa spazi (non tabulazioni) per strutturare i dati.
- **Gerarchico**: rappresenta dati complessi come alberi.
- **Supporta diversi tipi di dati**: stringhe, numeri, liste, oggetti, valori booleani, null.
- **Compatibile con JSON**: qualsiasi file JSON valido è anche un file YAML valido.


***

### **2. Struttura di base**

YAML usa l'**indentazione** per rappresentare la struttura gerarchica dei dati.

#### Esempio: Dati di configurazione

```yaml
server:
  port: 8080 
  host: localhost 
  ssl: true
```
- `server` è una mappa (o dizionario).
- `port`, `host` e `ssl` sono chiavi con i loro rispettivi valori.


***

### 3. Tipi di dati supportati

#### 3.1. Stringhe
```yaml
nome: "Luca" 
descrizione: 'File YAML di esempio'
```

#### 3.2. Numeri
```yaml
età: 30 
altezza: 1.85
```

#### 3.3. Liste

Le liste sono rappresentate con un trattino `-`.

```YAML
lingue:
   - italiano   
   - inglese   
   - spagnolo
```

#### 3.4. Booleani e Null

```yaml
attivo: true 
scaduto: false 
non_definito: null
```

#### 3.5. Annidamento

YAML supporta strutture complesse:

```yaml

utente:   
   nome: Luca   
   indirizzo:     
     città: Roma     
     CAP: 00100
```


### **4. Commenti**
I commenti si aggiungono con il simbolo `#`.
```yaml
# Questo è un commento port: 8080  # Commento in linea
```


***

### **5. Errori comuni**

1. **Indentazione errata**: YAML richiede che l'indentazione sia uniforme.
2. **Tabulazioni vietate**: usa sempre spazi, non tab.
3. **Caratteri speciali**: evita caratteri non validi nelle stringhe (usa virgolette se necessario).

***

### **6. Usi comuni**

- **File di configurazione**:
    - Kubernetes (`deployment.yaml`)
    - Docker Compose (`docker-compose.yml`)
- **Serializzazione dei dati**: per trasferire dati tra sistemi.
- **Gestione di template**: ad esempio in Ansible.

```YAML
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx:1.14.2
          ports:
            - containerPort: 80

```

