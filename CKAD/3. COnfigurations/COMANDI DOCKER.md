
***
## --> Gestione delle immagini

- #### Costruire un'immagine da un Dockerfile.
    ```bash
    docker build -t nome-immagine:tag .
    ```
    - Il punto (`.`) alla fine del comando `docker build -t nome-immagine:tag .` specifica la **directory di contesto** per la build Docker. In questo caso `.` per la directory corrente.
      
      Nella maggior parte dei casi, il punto (`.`) è usato per indicare che la **directory corrente** contiene tutto il necessario per la build, rendendo il comando semplice e intuitivo.
      
    -  **Come funziona il contesto di build?**
	1. Docker legge il percorso indicato (in questo caso `.` per la directory corrente) e prepara un **archivio temporaneo** contenente tutti i file della directory specificata.
	2. Durante la build, Docker accede a questi file per eseguire le istruzioni nel **Dockerfile**, come `COPY`, `ADD`, o riferimenti a file locali.

		La directory di contesto contiene i file che Docker utilizzerà durante il processo di build, compreso il **Dockerfile** e tutti i file o directory necessari per creare l'immagine.



- #### Elencare tutte le immagine presenti localmente
```bash
    `docker images`
    ```



- #### Scaricare un'immagine dal Docker Hub o da un altro registro.
```bash
    `docker pull nome-immagine:tag`
```


- #### Caricare un'immagine su un registro remoto (ad esempio Docker Hub). 
```bash
    `docker push nome-immagine:tag`
    ```



- #### Elencare le immagine presenti localmente
    visualizza un elenco di tutte le immagini Docker disponibili localmente sul sistema. Questo elenco mostra informazioni utili come il nome, il tag, l'ID dell'immagine e altre dettagli utili per la gestione delle immagini.
    
```bash
    docker images
    ```


- #### Rimuovere una o più immagine
```bash
    docker rmi nome-immagine:tag
    ```



***
## --> Gestione dei container

- #### 1 Creare ed eseguire un container da un'immagine
```bash
docker run -d --name mio-container -p 8080:80 nome-immagine:tag

o anche in generale

docker run -d --name nome-container -p porta-host:porta-container nome-immagine:tag
    ```
    
 -  -p 8080:80:  Effettua il "port mapping" tra il sistema host e il container:

	- **8080**: È la porta sul sistema **host** (il tuo computer o server). Sarà quella accessibile dall'esterno.
	- **80**: È la porta all'interno del container. Questa porta è usata dall'applicazione in esecuzione nel container.
	- Il mapping consente al traffico indirizzato alla porta 8282 dell'host di essere inoltrato alla porta 8080 del container.


- #### 1.1 Sovrascrivere il comando base dell'immagine
  ```bash
docker run nome-immagine [COMMAND]
```

Il comando `docker run nome-immagine [COMMAND]` avvia un container basato sull'immagine nome-immagine e, opzionalmente, esegue un comando specificato al posto di quello predefinito.
	-  **`[COMMAND]`**: È un comando opzionale che verrà eseguito all'interno del container al momento dell'avvio. Se non specificato, Docker utilizza il comando predefinito dell'immagine, che per Ubuntu è `/bin/bash` (una shell interattiva).

Per rendere definitivo questo CMD dovrò scriverlo nel dockerfile


- #### 1.2 Sovrascrivere l'entrypoint
  Il flag `--entrypoint` nel comando `docker run` consente di sovrascrivere l'**entrypoint** predefinito di un'immagine Docker. L'entrypoint è il comando o lo script che viene eseguito automaticamente quando un container viene avviato. Usando `--entrypoint`, puoi specificare un comando alternativo al posto di quello predefinito.
  
  ```bash
docker run --entrypoint [COMANDO] [IMMAGINE] [ARGOMENTI]
```






- #### 2. Elencare i container attivi
```bash
docker ps
```

- #### 3. Elencare i container attivi e non attivi
```bash
docker ps -a
```

- #### 4. Fermare un container in esecuzione 
```bash
docker stop nome-container
```

- #### 5. Avviare un container fermo
```bash
docker start nome-container
```

- #### 6. Riavviare un container
```bash
docker restart nome-container
```

- #### 7. Rimuovere un container
```bash
docker rm nome-container
```

- #### 
```bash
```