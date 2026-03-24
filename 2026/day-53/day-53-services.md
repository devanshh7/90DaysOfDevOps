# Kubernetes Services

## Overview

Today I learned how Kubernetes Services solve the Pod networking problem. Pods have unstable IPs that change on restart — a Service provides a permanent, stable IP and DNS name that load-balances traffic across all matching Pods.

---

## Why Services exist

![k8s_services_architecture](https://github.com/user-attachments/assets/4159e302-e889-4fa3-a825-f6ac0affb8de)

Every Pod gets its own IP address, but two problems make pod IPs useless for direct communication:

1. Pod IPs are not stable — when a Pod restarts or gets replaced by the Deployment controller, it gets a brand new IP address
2. A Deployment runs multiple identical Pods — you don't want to hardcode any single one's IP

A Service solves both. It provides a single stable virtual IP (ClusterIP) backed by all matching Pods, with automatic load balancing.

```
[Client] --> [Service: stable IP + DNS] --> [Pod 1: 10.244.0.4]
                                        --> [Pod 2: 10.244.0.5]
                                        --> [Pod 3: 10.244.0.6]
```

The Service IP never changes, even if every Pod behind it is replaced.

---

## Service types overview

| Type | Accessible from | When to use |
|---|---|---|
| `ClusterIP` | Inside the cluster only | Internal service-to-service communication |
| `NodePort` | Outside via `<NodeIP>:<NodePort>` | Development, testing, on-prem clusters |
| `LoadBalancer` | Outside via a cloud load balancer IP | Production traffic in cloud environments |

Each type builds on the previous. A LoadBalancer also has a NodePort; a NodePort also has a ClusterIP.

---

## Task 1 — Deploy the application

**File: `app-deployment.yaml`**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-app
  labels:
    app: web-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web-app
  template:
    metadata:
      labels:
        app: web-app
    spec:
      containers:
      - name: nginx
        image: nginx:1.25
        ports:
        - containerPort: 80
```

```bash
kubectl apply -f app-deployment.yaml
kubectl get pods -o wide
```
<img width="726" height="297" alt="image" src="https://github.com/user-attachments/assets/c5dd58f5-46b7-4718-9fd9-126836399067" />

The `-o wide` flag shows each Pod's individual IP address. These IPs are the problem — they change every time a Pod restarts. Note them down before proceeding.

**Verify:** All 3 pods are `Running`. Pod IPs noted: `__10.244.0.23_`, `_10.244.0.24__`, `__10.244.0.25_`

---

## Task 2 — ClusterIP service (internal access only)

ClusterIP is the default Service type. It creates a stable internal virtual IP reachable only from within the cluster. Nothing outside can connect to it.

**File: `clusterip-service.yaml`**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: web-app-clusterip
spec:
  type: ClusterIP
  selector:
    app: web-app
  ports:
  - port: 80        # port the Service listens on
    targetPort: 80  # port on the Pod to forward to
```
<img width="398" height="302" alt="image" src="https://github.com/user-attachments/assets/1c114a70-eefd-4d55-a038-77c9afd8654e" />

**Key fields explained:**

| Field | Purpose |
|---|---|
| `selector.app: web-app` | Finds all Pods with this label — these are the backends |
| `port` | What clients connect to on the Service |
| `targetPort` | What the Service forwards to on each Pod (can differ from `port`) |

```bash
kubectl apply -f clusterip-service.yaml
kubectl get services
```
<img width="689" height="118" alt="image" src="https://github.com/user-attachments/assets/0c24036e-9974-432e-a69d-3affc95ebe06" />

**Test from inside the cluster:**

```bash
kubectl run test-client --image=busybox:latest --rm -it --restart=Never -- sh

# Inside the test pod:
wget -qO- http://web-app-clusterip
exit
```
<img width="716" height="337" alt="image" src="https://github.com/user-attachments/assets/3e232463-4f16-453b-8c0c-f3d522bb43ac" />

`--rm` deletes the test pod automatically on exit. The nginx welcome page should appear. Running `wget` multiple times distributes requests across all 3 Pods.

**Verify:** Service responds. CLUSTER-IP is stable and does not change between runs.

---

## Task 3 — Service discovery with DNS

Kubernetes runs a built-in DNS server (`CoreDNS`) that creates a DNS record for every Service automatically. The full DNS format is:

```
<service-name>.<namespace>.svc.cluster.local
```

```bash
kubectl run dns-test --image=busybox:latest --rm -it --restart=Never -- sh

# Inside the pod:
wget -qO- http://web-app-clusterip                               # short name (same namespace)
wget -qO- http://web-app-clusterip.default.svc.cluster.local    # full DNS name
nslookup web-app-clusterip                                       # resolve the DNS entry
exit
```
<img width="679" height="290" alt="image" src="https://github.com/user-attachments/assets/ca523939-086a-4934-ac1f-d50b64257c1a" />

**Rules for DNS names:**

- Short name (e.g. `web-app-clusterip`) — works only within the same namespace
- Full name (e.g. `web-app-clusterip.default.svc.cluster.local`) — works from any namespace

**Verify:** `nslookup` returned IP: `_10.96.131.25__` — this matches the CLUSTER-IP from `kubectl get services`.

---

## Task 4 — NodePort service (external access via node)

NodePort opens a port on every node in the cluster (valid range: `30000–32767`). Traffic arriving at any node on that port is forwarded into the Service, then to the Pods.

**File: `nodeport-service.yaml`**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: web-app-nodeport
spec:
  type: NodePort
  selector:
    app: web-app
  ports:
  - port: 80
    targetPort: 80
    nodePort: 30080   # opened on every node in the cluster
```

**Traffic flow:**
```
External client --> <NodeIP>:30080 --> Service --> Pod:80
```
<img width="717" height="192" alt="image" src="https://github.com/user-attachments/assets/6f62a751-649c-48ff-a0f5-c50e727af1c5" />

```bash
kubectl apply -f nodeport-service.yaml
kubectl get services

# Access depending on your local setup:
minikube service web-app-nodeport --url   # Minikube
curl http://localhost:30080               # Docker Desktop
kubectl get nodes -o wide                 # Kind: get node IP, then curl <node-ip>:30080
```

**Verify:** Nginx welcome page visible from outside the cluster via NodePort.

> **Note:** NodePort is convenient for development but not suitable for production. Clients need to know node IPs, and the port number is high and non-standard. LoadBalancer is the production-grade alternative.

---

## Task 5 — LoadBalancer service (cloud external access)

LoadBalancer is the production Service type for cloud environments. It instructs the cloud provider (AWS, GCP, Azure) to provision a real external load balancer with a public IP.

**File: `loadbalancer-service.yaml`**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: web-app-loadbalancer
spec:
  type: LoadBalancer
  selector:
    app: web-app
  ports:
  - port: 80
    targetPort: 80
```

```bash
kubectl apply -f loadbalancer-service.yaml
kubectl get services
```

On a local cluster (Minikube, Kind, Docker Desktop) the `EXTERNAL-IP` column shows `<pending>`. This is expected — there is no cloud controller to provision a real IP.

**Why `<pending>` on local clusters?**
A LoadBalancer Service requires a cloud controller manager to watch for the Service and provision an external load balancer through the cloud provider's API. Local clusters have no such controller, so Kubernetes waits indefinitely.

**Simulating LoadBalancer on Minikube:**

```bash
minikube tunnel    # run in a separate terminal — provisions a local tunnel
kubectl get services    # EXTERNAL-IP now shows a real IP
```

**Verify:** EXTERNAL-IP shows `<pending>` on a local cluster (or a real IP if using `minikube tunnel`).

---

## Task 6 — Service types side by side

```bash
kubectl get services -o wide
kubectl describe service web-app-loadbalancer
```

### The layering model

Service types are not independent — each wraps the previous:

```
LoadBalancer
  ├── external IP (cloud-provisioned)
  └── NodePort (e.g. :31234)
        └── ClusterIP (e.g. 10.96.45.12)
```

A LoadBalancer Service is simultaneously reachable via:
- Its external IP (from anywhere, cloud only)
- Its NodePort (from any node)
- Its ClusterIP (from inside the cluster)

`kubectl describe service web-app-loadbalancer` shows all three: a `ClusterIP`, a `NodePort`, and the `LoadBalancer Ingress` section.

**Verify:** The LoadBalancer service description showed ClusterIP: `_10.96.238.99__` and NodePort: `_10.96.72.246__`

### Full comparison

| Type | `kubectl get svc` CLUSTER-IP | EXTERNAL-IP | PORT(S) | Accessible from |
|---|---|---|---|---|
| ClusterIP | Yes | None | 80/TCP | Cluster only |
| NodePort | Yes | None | 80:30080/TCP | Cluster + `NodeIP:30080` |
| LoadBalancer | Yes | IP or `<pending>` | 80:3xxxx/TCP | Cluster + NodePort + external IP |

---

## Task 7 — Clean up

```bash
kubectl delete -f app-deployment.yaml
kubectl delete -f clusterip-service.yaml
kubectl delete -f nodeport-service.yaml
kubectl delete -f loadbalancer-service.yaml

kubectl get pods      # should show: No resources found
kubectl get services  # should show only the built-in "kubernetes" service
```
<img width="689" height="228" alt="image" src="https://github.com/user-attachments/assets/aa7261df-c1f4-452f-b10a-adf99b15674a" />

**The built-in `kubernetes` service** in the default namespace is a special service that gives Pods inside the cluster access to the Kubernetes API server. Never delete it.

**Verify:** All custom pods and services are gone. Only the `kubernetes` service remains.

---

## Summary of useful commands

| Command | What it does |
|---|---|
| `kubectl apply -f service.yaml` | Create or update a Service |
| `kubectl get services` | List all services in the current namespace |
| `kubectl get services -o wide` | Show extra columns including selector |
| `kubectl describe service <name>` | Full Service details including endpoints |
| `kubectl run test --image=busybox --rm -it --restart=Never -- sh` | Spawn a temporary pod for testing |
| `kubectl get endpoints <name>` | List the Pod IPs a Service is currently routing to |
| `minikube service <name> --url` | Get the URL to access a NodePort/LB service on Minikube |
| `minikube tunnel` | Simulate LoadBalancer external IP on Minikube |

---

## Key concepts learned

**The selector is everything.** A Service finds its Pods purely through label matching (`selector.app: web-app`). Add a Pod with that label and the Service routes to it automatically. Remove the label from a Pod and it's immediately removed from the Service's backend pool.

**`port` vs `targetPort`.** `port` is what clients connect to on the Service. `targetPort` is what the Service forwards to on the Pod. They can be different — for example, a Service can listen on port `443` and forward to pods on port `8080`.

**DNS is the right way to connect services.** Never hardcode a ClusterIP in your app config. Use the DNS name (`service-name` or `service-name.namespace.svc.cluster.local`). The DNS name is stable across restarts, redeployments, and even cluster migrations.

**ClusterIP is the foundation.** In a microservices architecture, most service-to-service traffic flows over ClusterIP. NodePort and LoadBalancer are only for when you need to let traffic in from outside the cluster.

**LoadBalancer = NodePort + ClusterIP + cloud provisioning.** Understanding the layering means you always know what fallback access methods exist even if the external load balancer isn't available yet.

---

*Day 53 complete — next up: ConfigMaps and Secrets (Day 54)*
