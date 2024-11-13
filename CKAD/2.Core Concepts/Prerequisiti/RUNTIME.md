In Kubernetes e nel contesto dei container, un **runtime** (come Docker) è il software responsabile dell'esecuzione e della gestione dei container.

### Cos'è un Container Runtime?

Un **container runtime** è l'ambiente di esecuzione in cui i container vengono avviati e gestiti. Fornisce i meccanismi necessari per isolare le applicazioni e le loro dipendenze in modo che possano essere eseguite indipendentemente l'una dall'altra e dal sistema operativo sottostante.

### Funzioni Principali di un Container Runtime:
- **Esecuzione dei Container**: Crea, avvia e gestisce il ciclo di vita dei container.
- **Isolamento**: Assicura che ogni container abbia accesso limitato e controllato alle risorse del sistema (CPU, memoria, storage).
- **Interfaccia con il Kernel**: Interagisce con il kernel del sistema operativo per gestire i processi e le risorse dei container.
- **Networking e Storage**: Gestisce la connettività di rete tra i container e le opzioni di storage persistente o temporaneo.