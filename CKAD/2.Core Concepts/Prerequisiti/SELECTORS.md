I **selectors** sono una funzionalità chiave in Kubernetes che consente di collegare oggetti come i **Service**, i **ReplicaSet**, o i **Jobs** ad altri oggetti, in particolare ai **Pods**. I selectors utilizzano un insieme di **label** per individuare i pod (o altre risorse) con cui devono interagire.


## Cosa sono le label?
Le label sono coppie chiave-valore assegnate agli oggetti Kubernetes (es.: Pods, Nodes, Services). Sono usate per identificare, classificare o raggruppare oggetti in modo flessibile.

Esempio di label:

```yaml
Copy code
metadata:
  labels:
    app: web
    environment: production
```




## Come funzionano i selectors?
I selectors permettono di trovare oggetti con label specifiche. Questo è essenziale per operazioni come:

1. **Collegare un Service ai Pods**
Un Service utilizza i selectors per indirizzare il traffico ai pod corrispondenti.

Esempio:
```yaml
selector:
  app: my-app
```
Questo Service invierà il traffico solo ai pod con app=my-app nei loro label.

2. **Definire un gruppo di replica in un ReplicaSet**
Un ReplicaSet usa i selectors per individuare e gestire un gruppo di pod.
Esempio:

```yaml
selector:
  matchLabels:
    environment: production
```