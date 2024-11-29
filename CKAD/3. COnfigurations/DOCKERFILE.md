Un **Dockerfile** è un file di testo che contiene una serie di istruzioni per creare un'immagine Docker. È la "ricetta" che Docker utilizza per costruire un'immagine personalizzata, che può essere successivamente eseguita come un container.

Ogni Immagine docker **deve essere basata su un'altra immagine** (Solitamente un sistema operativo oppure un'altra immagine create in precedenza basata su un OS)



---
### **Perché usare un Dockerfile?**

Il Dockerfile ti permette di:

1. Automatizzare la creazione di immagini Docker.
2. Definire in modo dichiarativo tutti gli aspetti di un'immagine (sistema operativo di base, dipendenze, configurazioni, comandi da eseguire).
3. Assicurarti che la tua applicazione sia eseguita in ambienti identici, indipendentemente dalla macchina host.



***
### **Struttura di base di un Dockerfile**
![[Pasted image 20241126094630.png]]

- Un Dockerfile è composto da **istruzioni - argomento** che Docker esegue in sequenza. Le **istruzioni** più comuni sono:

1. **`FROM`**: Specifica l'immagine di base da cui partire. Ad esempio:
```DOCKERFILE
    `FROM ubuntu:20.04`
    ```
    
2. **`RUN`**: **Esegue comandi** durante la costruzione dell'immagine, come l'installazione di software:
```DOCKERFILE
    `RUN apt-get update && apt-get install -y nginx`
```
    
3. **`COPY`** o **`ADD`**: Copia file o directory dal tuo sistema host all'immagine:
```DOCKERFILE
    `COPY app/ /var/www/html/`
```
    
4. **`WORKDIR`**: Imposta la directory di lavoro per i comandi successivi:
```DOCKERFILE
    `WORKDIR /app`
    ```
    
5. **`CMD`** e **`ENTRYPOINT`**: Specificano il comando da eseguire quando il container parte:
```DOCKERFILE
    `CMD ["nginx", "-g", "daemon off;"]`
```
    
6. **`EXPOSE`**: Documenta le porte su cui il container ascolta:
```DOCKERFILE
    `EXPOSE 80`
 ```
    
7. **`ENV`**: Imposta variabili d'ambiente:
    
```DOCKERFILE
    `ENV APP_ENV=production`
```





***
### **Passaggi per usare un Dockerfile**

1. **Creare il Dockerfile**: Salva il contenuto del Dockerfile in un file chiamato `Dockerfile`.

2. **Costruire l'immagine**: Usa il comando `docker build` per creare un'immagine:
   ```DOCKERFILE
    docker build -t nome-immagine .
```
	Il punto (`.`) indica la directory corrente, dove si trova il Dockerfile.

3. **Eseguire il container**: Lancia un container basato sull'immagine:
   ```DOCKERFILE
   docker run -d -p 8080:8080 nome-immagine
```
   Questo comando esegue il container in modalità detached (`-d`) e mappa la porta 8080 del container a quella dell'host.




***
### **Differenze tra le istruzioni principali**

- **`RUN`**: Esegue comandi durante la **costruzione** dell'immagine.
- **`CMD`**: Definisce il comando predefinito da eseguire quando il container parte (ma può essere sovrascritto).
- **`ENTRYPOINT`**: Simile a `CMD`, ma meno flessibile, viene sempre eseguito come comando principale del container.




***
### Architettura a strati
Quando Docker *builda* le immagini, lo fa con un'architettura stratificata:
Ogni linea del file crea uno nuovo livello nell'immagine con solo le  modifiche che l'istruzione prevede.

![[Pasted image 20241126095636.png]]

Poichè ogni livello memorizza solo le modifiche dal livello precedente, questo si riflette anche nelle dimensioni: AL crescere degli strati crescerà il peso dell'immagine.

Attraverso il comando **history** posso vedere i vari strati con le info correlate:

  ```DOCKERFILE
   docker history nome
   ```




***
### Fail
Grazie all'architettura a stratificata, se uno degli strati fallisce durante la fase di build, rilanciando il comando **docker build ...**, la costruzione ricomincerà dallo strato mancante 

![[Pasted image 20241126100336.png]]


La stessa cosa vale nel caso in cui andassimo ad **aggiungere altri strati**: Gli altri non verrebbero sicostruiti da capo









