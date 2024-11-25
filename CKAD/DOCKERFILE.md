Un **Dockerfile** è un file di testo che contiene una serie di istruzioni per creare un'immagine Docker. È la "ricetta" che Docker utilizza per costruire un'immagine personalizzata, che può essere successivamente eseguita come un container.



---
### **Perché usare un Dockerfile?**

Il Dockerfile ti permette di:

1. Automatizzare la creazione di immagini Docker.
2. Definire in modo dichiarativo tutti gli aspetti di un'immagine (sistema operativo di base, dipendenze, configurazioni, comandi da eseguire).
3. Assicurarti che la tua applicazione sia eseguita in ambienti identici, indipendentemente dalla macchina host.



***
### **Struttura di base di un Dockerfile**

Un Dockerfile è composto da **istruzioni** che Docker esegue in sequenza. Ecco le più comuni:

1. **`FROM`**: Specifica l'immagine di base da cui partire. Ad esempio:
```DOCKERFILE
    `FROM ubuntu:20.04`
    ```
    
2. **`RUN`**: Esegue comandi durante la costruzione dell'immagine, come l'installazione di software:
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
   