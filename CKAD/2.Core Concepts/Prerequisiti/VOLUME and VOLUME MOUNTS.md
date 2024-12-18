I volumi in Kubernetes (e i **volume mount**) servono a fornire storage persistente ai container all'interno dei pod, superando il limite del filesystem effimero dei container Docker. Qui di seguito trovi una spiegazione dettagliata:

***
# VOLUMI

Un **volume** in Kubernetes è un'astrazione che rappresenta uno spazio di storage che può essere montato e utilizzato dai container di un pod. A differenza del filesystem di un container, un volume non viene cancellato quando il container termina o si riavvia.


***
### **Tipi di Volumi**

Kubernetes supporta vari tipi di volumi, ognuno progettato per specifici scenari di utilizzo:

1. **emptyDir**:
    - Viene creato quando un pod viene schedulato su un nodo.
    - È una directory vuota che persiste per la durata del pod.
    - Utilizzato per la condivisione temporanea di file tra container di un pod.
    
1. **hostPath**:
    - Monta una directory del nodo (host) nel pod.
    - Utile per accedere a file specifici del nodo, ma può essere rischioso per la sicurezza.
    
1. **persistentVolumeClaim (PVC)**:
    - Consente a un pod di richiedere uno storage persistente tramite un PersistentVolume (PV).
    - Utilizzato per storage di lunga durata, ad esempio per database.
    
1. **configMap e secret**:
    - Montano configurazioni o credenziali sensibili come file o variabili d'ambiente.
    
1. **nfs, cephfs, glusterfs**:
    - Permettono di montare file system di rete.
    
1. **awsElasticBlockStore, gcePersistentDisk, azureDisk, etc.**:
    - Volumi per specifici provider di cloud storage.
    
1. **csi**:
    - Consente di utilizzare plugin per storage esterni tramite l'interfaccia Container Storage Interface.




***
***
# VOLUME MOUNT
Un **volume mount** è l'operazione con cui un volume viene associato a un percorso specifico all'interno del filesystem di un container. Ogni container in un pod può montare uno o più volumi, anche nello stesso pod.


***
### **Come funzionano i volumi in un pod?**

Un volume viene dichiarato nella sezione `volumes` della specifica del pod, e poi montato nei container tramite la sezione `volumeMounts`.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: example-pod
spec:
  containers:
  - name: app-container
    image: nginx
    volumeMounts:
    - name: example-volume       # Nome del volume
      mountPath: /usr/share/nginx/html # Path nel container
  volumes:
  - name: example-volume         # Nome del volume
    emptyDir: {}                 # Tipo di volume
```

##### Cosa fa questo pod?
- Definisce un volume di tipo `emptyDir`.
- Monta questo volume nella directory `/usr/share/nginx/html` del container.
- I dati scritti in `/usr/share/nginx/html` saranno condivisi tra tutti i container del pod.


***
### **Access Modes**

I volumi possono avere diverse modalità di accesso:

- **ReadWriteOnce (RWO)**: Montato in lettura e scrittura da un singolo nodo.
- **ReadOnlyMany (ROX)**: Montato in sola lettura da più nodi.
- **ReadWriteMany (RWX)**: Montato in lettura e scrittura da più nodi.