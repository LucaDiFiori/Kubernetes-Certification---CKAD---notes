In Kubernetes, **svc** si riferisce ai **Service (Servizi)**, una delle risorse fondamentali del sistema. I servizi sono utilizzati per esporre le applicazioni o i pod in modo stabile e per gestire la comunicazione tra diverse parti di un'applicazione. Ecco una spiegazione dettagliata:



***
### **Cos'è un Service in Kubernetes?**

Un **Service** è un'astrazione che definisce un endpoint di rete stabile per un gruppo di pod. Questo è utile perché i pod in Kubernetes sono **effimeri**: possono essere creati, distrutti o spostati dinamicamente. Il Service garantisce un modo costante per accedere ai pod, indipendentemente dai loro indirizzi IP che possono cambiare.




---

### **Come funziona un Service?**

Un Service utilizza delle **label** per selezionare i pod a cui si vuole collegare. Funziona come un bilanciatore di carico interno al cluster, distribuendo il traffico tra i pod corrispondenti.




***
### **Tipi di Services**

1. **ClusterIP** (default):
    - Espone il servizio internamente al cluster Kubernetes.
    - Il **ClusterIP** è il tipo di servizio predefinito. Esso espone il servizio solo all'interno del cluster Kubernetes, assegnandogli un indirizzo IP virtuale (chiamato **ClusterIP**) accessibile da altri pod o componenti interni al cluster.
      
      **Caratteristiche principali**
      - **Accessibile solo internamente** al cluster, tramite il DNS del servizio o il suo ClusterIP.
      - Perfetto per la comunicazione tra i pod (ad esempio, per applicazioni composte da più microservizi).
      - Non richiede configurazioni esterne.
      
      
      **Esempio**
      Immagina di avere un'applicazione con due componenti:
      - Un backend (API) eseguito su pod con l'etichetta `app: backend`.
      - Un frontend che comunica con il backend.
      - Puoi creare un servizio `ClusterIP` per il backend, rendendolo accessibile al frontend tramite DNS.
	
1. **NodePort**:
    - Il tipo **NodePort** espone il servizio su una porta specifica di ogni nodo del cluster. Questo permette di accedere al servizio anche dall'esterno del cluster, utilizzando l'IP di un nodo e la porta assegnata.
    - Permette l'accesso al servizio dall'esterno del cluster usando `<NodeIP>:<NodePort>`.
      
3. **LoadBalancer**:
    - Richiede un bilanciatore di carico esterno (fornito dal cloud provider).
    - Fornisce un IP pubblico per accedere al servizio.
      
4. **ExternalName**:
    - Mappa il servizio a un hostname esterno, restituendo un record DNS.




***
### **Componenti principali di un Service**

- **Selector**: Indica quali pod saranno associati al servizio, in base alle etichette (labels).
- **ClusterIP**: L'indirizzo IP virtuale che rimane stabile per l'accesso al servizio.
- **Port**: Specifica la porta su cui il servizio sarà disponibile.
- **TargetPort**: Indica la porta del container su cui il traffico sarà inoltrato.




***
### **Esempio di Configurazione YAML**

Un Service che collega i pod di un'applicazione a una porta specifica:

```yml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  selector:
    app: my-app
  ports:
    - protocol: TCP
      port: 80         # Porta esposta dal Service
      targetPort: 8080 # Porta del pod
  type: ClusterIP
```




***
### **Risoluzione DNS dei Service**

I Service sono automaticamente registrati nel DNS interno del cluster. Ad esempio:

- Se il Service si chiama `my-service` e si trova nel namespace `default`, il suo nome DNS sarà:
  
```bash
    `my-service.default.svc.cluster.local`
```






***
## Comandi
#### Creare un service
1. **Manuale** (usando un file YAML):
```bash
kubectl apply -f my-service.yaml
```

2. **Automatica** (senza file YAML): Creazione di un `ClusterIP` per un'applicazione esistente:
```bash
kubectl expose pod <pod-name> --type=ClusterIP --port=80 --target-port=8080
```



#### Visualizzare i servizi
```bash
kubectl get services
kubectl get svc
```


#### Visualizzare i dettagli di un servizio
```bash
kubectl describe service <service-name>
```


#### Eliminare un servizio
```bash
kubectl delete service <service-name>
```



### Uso pratico
Immagina di avere un'applicazione web che gira su un deployment chiamato `web-deployment`. Puoi esporla tramite un servizio:

1. **Crea un deployment**:
```bash
kubectl create deployment web-deployment --image=nginx
```

2. **Esporre il servizio**:
```bash
kubectl expose deployment web-deployment --type=NodePort --port=80
```

3. **Trova la porta esposta**:
```bash
kubectl get svc web-deployment
```

4. **Accedi all'applicazione**: Usando l'indirizzo IP del nodo e la porta visualizzata nel comando precedente.