# Kubernetes
![Kubernetes](https://img.shields.io/badge/kubernetes-v1.22.0-blue)

## Feature

### Rolling Update & Rollback Strategies
Le strategie di deployment avanzate permettono aggiornamenti sicuri:

**Rolling Update**: Aggiornamento graduale
```yaml
apiVersion: apps/v1
kind: Deployment
spec:
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 25%
      maxSurge: 25%
```

**Blue-Green Deployment**: Switching istantaneo tra versioni
**Canary Deployment**: Test su una piccola percentuale di traffico

### Affinity/Anti-affinity: Controllo Granulare
Controllo preciso della disposizione dei Pod:

**Node Affinity**: Preferenze per specifici nodi
```yaml
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: disk-type
            operator: In
            values: ["ssd"]
```

**Pod Anti-Affinity**: Evitare co-locazione di Pod critici

### Taint & Toleration: Nodi Specializzati
Meccanismo per dedicare nodi a specifici workload:
- **GPU Nodes**: Solo per workload AI/ML
- **High-Memory Nodes**: Per database in-memory
- **Edge Nodes**: Per applicazioni edge computing

### Network Policies: Micro-Segmentazione
Controllo granulare del traffico di rete:
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
spec:
  podSelector:
    matchLabels:
      app: database
  policyTypes:
  - Ingress
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: backend
```

### Helm: Package Manager per Kubernetes
Gestione di applicazioni complesse tramite charts:
- **Templating**: Parametrizzazione di manifest YAML
- **Versioning**: Gestione di release e rollback
- **Dependencies**: Gestione di dipendenze tra componenti
- **Marketplace**: Hub pubblico di applicazioni pre-configurate

### RBAC: Security by Design
Role-Based Access Control per sicurezza enterprise:
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: pod-reader
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "watch", "list"]
```

### Custom Resources: Estensibilità Infinita
Estensione dell'API Kubernetes con Custom Resource Definitions (CRD):
- **Operators**: Automazione di operazioni complesse
- **Custom Controllers**: Logica di business specifica
- **Domain-Specific APIs**: Astrazioni per il tuo dominio

### Cluster Federation: Multi-Cluster Management
Gestione coordinata di più cluster:
- **Cross-Cluster Service Discovery**
- **Workload Distribution**
- **Disaster Recovery**
- **Compliance & Governance**

---

## Architettura & Componenti

### Control Plane
- **kube-apiserver**: API Gateway centrale
- **etcd**: Database distribuito per stato cluster
- **kube-scheduler**: Algoritmi di scheduling
- **kube-controller-manager**: Loop di controllo
- **cloud-controller-manager**: Integrazione cloud provider

### Node Components
- **kubelet**: Agent su ogni nodo
- **kube-proxy**: Networking e load balancing
- **Container Runtime**: Docker, containerd, CRI-O

### Add-ons Essenziali
- **DNS**: Service discovery interno
- **Dashboard**: Interface web
- **Monitoring**: Prometheus + Grafana
- **Logging**: ELK Stack o Fluentd

---

## Strumenti & Workflow DevOps

### Gestione Cluster
- **kubectl**: CLI ufficiale
- **k9s**: Terminal UI avanzata
- **Lens**: Desktop IDE per Kubernetes
- **Rancher**: Gestione multi-cluster

### CI/CD Integration
- **ArgoCD**: GitOps continuous delivery
- **Flux**: Git-based deployment
- **Tekton**: Cloud-native CI/CD
- **Jenkins X**: CI/CD Kubernetes-native

### Monitoring & Observability
- **Prometheus**: Metrics collection
- **Grafana**: Visualizzazione dashboard
- **Jaeger**: Distributed tracing
- **Istio**: Service mesh con observability

### Security & Compliance
- **Falco**: Runtime security monitoring
- **OPA Gatekeeper**: Policy enforcement
- **Aqua/Twistlock**: Container security
- **Vault**: Secrets management

---

## Introduzione

Kubernetes (K8s) è una piattaforma open-source di orchestrazione container che automatizza il deployment, la scalabilità e la gestione di applicazioni containerizzate. Nato in Google e ora mantenuto dalla Cloud Native Computing Foundation (CNCF), Kubernetes è diventato lo standard de facto per l'orchestrazione container in ambienti enterprise.

### Perché Kubernetes?

In un mondo dove le applicazioni moderne sono composte da decine o centinaia di microservizi, gestire manualmente container, networking, storage e scalabilità diventa rapidamente ingestibile. Kubernetes risolve questa complessità fornendo:

- **Automazione completa**: Dalla gestione del ciclo di vita dei container al load balancing
- **Resilienza**: Self-healing automatico e fault tolerance integrata
- **Scalabilità**: Gestione elastica delle risorse in base al carico
- **Portabilità**: Deploy su qualsiasi cloud o on-premise

---

## Funzionalità principali

### Pod Scheduling & Resource Management
Il **Kubernetes Scheduler** è il cervello che decide dove eseguire ogni Pod. Utilizza algoritmi sofisticati per:
- Analizzare le richieste di risorse (CPU, memoria, storage)
- Rispettare vincoli di affinity/anti-affinity
- Bilanciare il carico tra i nodi
- Gestire taint e toleration per nodi specializzati

**Esempio pratico:**
```yaml
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: web-app
    image: nginx:1.21
    resources:
      requests:
        memory: "128Mi"
        cpu: "100m"
      limits:
        memory: "256Mi"
        cpu: "200m"
```

### ReplicaSet & High Availability
I **ReplicaSet** garantiscono che un numero specificato di Pod rimanga sempre in esecuzione:
- Monitoraggio continuo dello stato dei Pod
- Creazione automatica di nuovi Pod in caso di fallimento
- Distribuzione intelligente su più nodi per fault tolerance

### Deployment: GitOps Ready
I **Deployment** portano la gestione del software a un livello superiore:
- **Rolling Updates**: Aggiornamenti graduali senza downtime
- **Rollback automatici**: Ritorno alla versione precedente in caso di problemi
- **Controllo del rollout**: Pause, canary deployments, blue-green deployments
- **Versionamento**: Storico completo di tutte le modifiche

### Service Discovery & Load Balancing
Il networking in Kubernetes è completamente astratto e automatizzato:
- **ClusterIP**: Comunicazione interna tra servizi
- **NodePort**: Esposizione su porte specifiche dei nodi
- **LoadBalancer**: Integrazione con load balancer esterni
- **ExternalName**: Mapping a servizi esterni

### ConfigMap & Secret: Configuration as Code
Separazione netta tra applicazione e configurazione:
- **ConfigMap**: Dati di configurazione non sensibili
- **Secret**: Credenziali, chiavi API, certificati TLS
- **Mounting**: File o variabili d'ambiente
- **Hot-reload**: Aggiornamento runtime senza restart

### Autoscaling: Elasticità Intelligente
Kubernetes offre tre tipi di autoscaling:
- **HPA (Horizontal Pod Autoscaler)**: Scala il numero di Pod
- **VPA (Vertical Pod Autoscaler)**: Scala le risorse dei Pod esistenti
- **KEDA**: Autoscaling basato su eventi esterni (code, database, etc.)

### Self-Healing: Zero-Downtime Operations
Il sistema di self-healing include:
- **Liveness Probes**: Restart automatico di container non responsivi
- **Readiness Probes**: Rimozione temporanea dal load balancing
- **Node Replacement**: Migrazione automatica in caso di fallimento nodo

### Persistent Storage: Stateful Applications
Gestione avanzata dello storage per applicazioni stateful:
- **PersistentVolume (PV)**: Astrazione dello storage fisico
- **PersistentVolumeClaim (PVC)**: Richiesta di storage da parte dell'applicazione
- **StorageClass**: Provisioning dinamico e tipi di storage
- **StatefulSet**: Gestione di applicazioni con stato persistente

### Namespaces: Multi-Tenancy & Isolation
Isolamento logico per:
- **Ambienti**: dev, staging, production
- **Team**: separazione per gruppi di sviluppo
- **Applicazioni**: isolamento tra progetti
- **Governance**: policy e quote per namespace

### Ingress: Gateway Intelligente
Gestione centralizzata del traffico HTTP/HTTPS:
- **SSL/TLS Termination**: Gestione certificati
- **Path-based Routing**: Instradamento basato su URL
- **Load Balancing**: Distribuzione del traffico
- **Rate Limiting**: Controllo del traffico

---

## Feature avanzate

- **Rolling Update & Rollback** automatici
- **Affinity/Anti-affinity rules** per controllare la co-locazione dei Pod
- **Taint & Toleration** per personalizzare l’allocazione dei Pod
- **Network Policies** per controllo granulare del traffico tra Pod
- **Helm** per gestione pacchetti applicativi (Kubernetes charts)
- **RBAC (Role-Based Access Control)** per la gestione sicura degli accessi
- **Custom Resource Definition (CRD)** per estendere l’API Kubernetes
- **Cluster Federation** per gestire più cluster da un unico punto

---

## Vantaggi: Perché Scegliere Kubernetes

| Vantaggio                        | Descrizione tecnica                                                | Impatto Business |
|----------------------------------|--------------------------------------------------------------------|--------------------|
| **Scalabilità automatica**       | HPA/VPA/KEDA per scalare Pod o container in base al carico        | Costi ottimizzati, performance garantite |
| **Alta disponibilità**           | ReplicaSet + Service + Load Balancer garantiscono fault tolerance | 99.9%+ uptime, disaster recovery |
| **Gestione centralizzata**       | YAML dichiarativi, versionabili su Git (GitOps)                   | Audit trail, rollback immediati |
| **Portabilità**                  | Indipendente dal cloud/provider (cloud-agnostic)                  | Vendor lock-in evitato, flessibilità |
| **Aggiornamenti sicuri**         | Rolling updates e rollback automatici                             | Zero-downtime deployments |
| **Isolamento e sicurezza**       | RBAC, Namespaces, NetworkPolicy, Secret                           | Compliance, multi-tenancy |
| **Ecosistema esteso**            | Helm, ArgoCD, Prometheus, Istio, etc.                             | Time-to-market ridotto |
| **Resource Efficiency**          | Bin packing intelligente, resource sharing                        | Utilizzo hardware ottimale |
| **Self-Healing**                 | Restart automatico, health checks, circuit breakers               | Riduzione interventi manuali |
| **Declarative Management**       | Stato desiderato vs stato attuale                                 | Infrastructure as Code |

### Casi d'Uso Ideali

**Enterprise Applications**
- Microservizi complessi con centinaia di servizi
- Applicazioni mission-critical con requisiti 24/7
- Multi-tenant SaaS platforms

**Cloud-Native Development**
- API-first applications
- Event-driven architectures
- Serverless/FaaS implementations

**Data & AI Workloads**
- Machine Learning pipelines
- Big Data processing (Spark, Hadoop)
- Real-time analytics

**Edge Computing**
- IoT data processing
- CDN optimization
- Geo-distributed applications

---

## Svantaggi & Considerazioni

| Svantaggio                        | Descrizione tecnica                                                | Mitigazione |
|----------------------------------|--------------------------------------------------------------------|--------------| 
| **Curva di apprendimento ripida** | Richiede conoscenze di networking, sistemi distribuiti, YAML      | Training, certificazioni, graduale adoption |
| **Complessità operativa**        | Setup, aggiornamenti e debugging possono diventare complessi      | Managed services (EKS, GKE, AKS) |
| **Overhead risorse**             | Kubernetes stesso consuma CPU, RAM e storage                      | Non adatto per workload molto piccoli |
| **Manutenzione cluster**         | Richiede monitoraggio continuo e aggiornamenti dei componenti     | Automated operations, SRE practices |
| **Tooling necessario**           | Spesso servono strumenti esterni (Helm, ArgoCD, Prometheus, etc.) | Platform engineering, tool consolidation |
| **Debugging distribuito**        | Tracciare errori tra più Pod/Nodi può essere complicato           | Observability stack, distributed tracing |
| **Network Complexity**           | CNI, Service Mesh, Network Policies richiedono expertise          | Standardizzazione, automation |
| **Storage Challenges**           | Gestione PV/PVC può essere problematica                           | CSI drivers, automated provisioning |

### Quando NON Usare Kubernetes

**Scenari Non Adatti:**
- Applicazioni monolitiche semplici
- Team con < 5 sviluppatori
- Workload batch semplici
- Applicazioni legacy non containerizzabili
- Budget/skills limitati per operations

**Alternative da Considerare:**
- **Docker Compose**: Per development/small deployments
- **AWS Fargate**: Serverless containers
- **Heroku/Render**: Platform as a Service
- **VM-based deployments**: Per applicazioni legacy

---

## Metriche & KPI di Successo

### Performance Metrics
- **Pod Startup Time**: < 30 secondi
- **Resource Utilization**: CPU 70-80%, Memory 80-90%
- **Application Uptime**: 99.9%+
- **Scaling Response Time**: < 2 minuti

### Operational Metrics
- **Mean Time to Recovery (MTTR)**: < 15 minuti
- **Deployment Frequency**: Daily/Weekly
- **Change Failure Rate**: < 15%
- **Security Scan Results**: 0 high/critical vulnerabilities

### Business Metrics
- **Cost per Transaction**: Riduzione 20-40%
- **Time to Market**: Riduzione 50-70%
- **Developer Productivity**: +30% feature delivery
- **Infrastructure Costs**: Ottimizzazione 25-50%

---

## Learning Path & Roadmap

### Fase 1: Fondamentali (1-2 mesi)
- **Container Basics**: Docker, image building, registries
- **Kubernetes Core**: Pod, Service, Deployment, ConfigMap
- **kubectl**: Comandi essenziali, debugging base
- **YAML**: Sintassi, best practices, validation

### Fase 2: Operazioni (2-3 mesi)
- **Networking**: ClusterIP, NodePort, Ingress, DNS
- **Storage**: PV, PVC, StorageClass
- **Security**: RBAC, Secret, NetworkPolicy
- **Monitoring**: Prometheus basics, log aggregation

### Fase 3: Avanzato (3-6 mesi)
- **Helm**: Charts, templating, lifecycle management
- **CI/CD**: GitOps, ArgoCD, automated pipelines
- **Service Mesh**: Istio basics, traffic management
- **Custom Resources**: CRD, Operators

### Fase 4: Expert (6+ mesi)
- **Cluster Administration**: etcd, upgrades, backup/restore
- **Performance Tuning**: Resource optimization, autoscaling
- **Multi-cluster**: Federation, disaster recovery
- **Security Hardening**: Pod Security Standards, admission controllers

### Risorse Consigliate

**Libri:**
- "Kubernetes in Action" - Marko Lukša
- "Kubernetes: Up and Running" - Kelsey Hightower
- "The Kubernetes Book" - Nigel Poulton

**Certificazioni:**
- **CKA** (Certified Kubernetes Administrator)
- **CKAD** (Certified Kubernetes Application Developer)
- **CKS** (Certified Kubernetes Security Specialist)

**Playground:**
- **Katacoda**: Interactive scenarios
- **Play with Kubernetes**: Browser-based clusters
- **Minikube/Kind**: Local development clusters

---

## Getting Started: Quick Setup

### Local Development
```bash
# Installazione minikube (Windows)
choco install minikube

# Start cluster locale
minikube start --driver=docker

# Verifica installazione
kubectl cluster-info
kubectl get nodes
```

### Cloud Managed Services
```bash
# AWS EKS
eksctl create cluster --name my-cluster --region us-west-2

# Google GKE
gcloud container clusters create my-cluster --zone us-central1-a

# Azure AKS
az aks create --resource-group myResourceGroup --name myAKSCluster
```

### First Application
```yaml
# deployment.yaml
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
        image: nginx:1.21
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
  - port: 80
    targetPort: 80
  type: LoadBalancer
```

```bash
# Deploy dell'applicazione
kubectl apply -f deployment.yaml

# Verifica status
kubectl get pods
kubectl get services

# Access all'applicazione
kubectl port-forward service/nginx-service 8080:80
```

---

## Conclusioni & Raccomandazioni

Kubernetes è una **piattaforma di orchestrazione container estremamente potente** che rappresenta il futuro dell'infrastruttura cloud-native. La sua adozione è giustificata quando:

### È la Scelta Giusta Se:
- Hai **applicazioni distribuite complesse** (microservizi)
- Necessiti di **alta disponibilità e scalabilità automatica**
- Il team ha **competenze sufficienti** o budget per training
- Stai costruendo una **piattaforma a lungo termine**
- Hai bisogno di **portabilità multi-cloud**

### Evita Se:
- Le tue applicazioni sono **semplici monoliti**
- Il team è **piccolo** (<5 persone) senza expertise
- I **costi operativi** superano i benefici
- Hai **vincoli di compliance** troppo restrittivi
- Stai cercando una **soluzione rapida** per problemi semplici

### Strategia di Adozione Consigliata:
1. **Start Small**: Inizia con applicazioni non critiche
2. **Invest in Training**: Forma il team prima del rollout
3. **Use Managed Services**: EKS/GKE/AKS per ridurre complessità
4. **Implement Gradually**: Migrazione progressiva, non big-bang
5. **Monitor & Measure**: KPI chiari per misurare il successo

### Future Outlook:
- **WebAssembly**: Runtime alternativo ai container
- **Serverless Integration**: Knative, KEDA evolution
- **Edge Computing**: K3s, MicroK8s per IoT
- **AI/ML Integration**: Kubeflow, MLOps automation
- **Security Evolution**: Zero-trust, policy automation

---

