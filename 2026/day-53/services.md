# 🚀 Day 53 - Kubernetes Services

## 📌 Objective

Understand how Kubernetes Services provide stable networking and expose applications using different service types.

---

## 🧠 Problem Statement

Pods in Kubernetes:

* Have dynamic IPs
* Restart → IP changes
* Multiple replicas → difficult to access

---

## 💡 Solution: Kubernetes Services

Services provide:

* Stable IP
* DNS name
* Load balancing

---

## ⚙️ Tasks Performed

### ✅ 1. Deployment Creation

_First, create a Deployment that you will expose with Services. Create app-deployment.yaml:_

```
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

* Created a deployment with 3 nginx pods
* Verified pod IPs using:

```bash
kubectl get pods -o wide
```
<img width="747" height="385" alt="image" src="https://github.com/user-attachments/assets/63550116-ec88-4e27-b6fb-990fe8d3ad1e" />

<img width="831" height="156" alt="image" src="https://github.com/user-attachments/assets/248fb2a5-bbaa-4868-981f-fb62d6bd082a" />

---

### ✅ 2. ClusterIP Service

_ClusterIP is the default Service type. It gives your Pods a stable internal IP that is only reachable from within the cluster._

* Created internal service
* Verified using busybox pod:

```
apiVersion: v1
kind: Service
metadata:
  name: web-app-clusterip
spec:
  type: ClusterIP
  selector:
    app: web-app
  ports:
  - port: 80
    targetPort: 80
```
<img width="486" height="356" alt="image" src="https://github.com/user-attachments/assets/ef28872c-bcd5-4128-a6cc-dafc407c55c8" />

**Key fields:**

`selector.app`: web-app — this Service routes traffic to all Pods with the label app: web-app
`port: 80` — the port the Service listens on
`targetPort: 80` — the port on the Pod to forward traffic to

```
kubectl apply -f clusterip-service.yaml
kubectl get services
```

Now test it from inside the cluster:

```bash
# Run a temporary pod to test connectivity
kubectl run test-client --image=busybox:latest --rm -it --restart=Never -- sh

# Inside the test pod, run:
wget -qO- http://web-app-clusterip

exit
```
<img width="898" height="512" alt="image" src="https://github.com/user-attachments/assets/d7f9f853-c9e7-4dc1-ba6c-961474742b22" />


✔ Successfully accessed nginx page

---

### ✅ 3. DNS Verification

Kubernetes has a built-in DNS server. Every Service gets a DNS entry automatically:

```
<service-name>.<namespace>.svc.cluster.local
```

* Verified service DNS:

```bash
kubectl run dns-test --image=busybox:latest --rm -it --restart=Never -- sh

# Inside the pod:
# Short name (works within the same namespace)
wget -qO- http://web-app-clusterip

# Full DNS name
wget -qO- http://web-app-clusterip.default.svc.cluster.local
```

<img width="906" height="830" alt="image" src="https://github.com/user-attachments/assets/78ed98e7-c270-40fe-985e-7ad575261975" />

```bash
nslookup web-app-clusterip
```
<img width="915" height="428" alt="image" src="https://github.com/user-attachments/assets/2a667a61-cdcc-4c22-9d57-b63fbd521d33" />

<img width="751" height="274" alt="image" src="https://github.com/user-attachments/assets/557b92d2-bfa9-40ba-9b00-a6562747884f" />

✔ DNS resolved to ClusterIP

**Verify: What IP does nslookup return? Does it match the CLUSTER-IP from kubectl get services?**

_YEs, It matches the both  IP addresses..._

1. `nslookup web-app-clusterip`: 

RETURN: Name:   web-app-clusterip.default.svc.cluster.local
Address: 10.96.134.39

2. `kubectl get svc`:

RETURN: service/web-app-clusterip   ClusterIP   10.96.134.39   

---

### ✅ 4. NodePort Service

_A NodePort Service exposes your application on a port on every node in the cluster. This lets you access the Service from outside the cluster._

**Create nodeport-service.yaml:**
```
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
    nodePort: 30080
```
<img width="516" height="236" alt="image" src="https://github.com/user-attachments/assets/d4f32344-5560-4dd3-b5a3-602873c446cf" />

* nodePort: 30080 — the port opened on every node (must be in range 30000-32767)
* Traffic flow: <NodeIP>:30080 -> Service -> Pod:80
<img width="839" height="377" alt="image" src="https://github.com/user-attachments/assets/b707d084-6734-4a5b-b222-e3d81e2e2623" />

```
kubectl apply -f nodeport-service.yaml
kubectl get services
```
<img width="778" height="501" alt="image" src="https://github.com/user-attachments/assets/93fca23c-4f58-415b-bd6d-ca1ed0ae4aa8" />

<img width="685" height="281" alt="image" src="https://github.com/user-attachments/assets/7f87d256-1d4f-43b0-82da-997c1303377b" />

* Exposed app externally
* Accessed via:

```
http://localhost:30080
```

✔ Successfully accessed nginx page

---

### ✅ 5. LoadBalancer Service

_In a cloud environment (AWS, GCP, Azure), a LoadBalancer Service provisions a real external load balancer that routes traffic to your nodes._

* Created LoadBalancer service
* Observed:

Create loadbalancer-service.yaml:

```
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

```
EXTERNAL-IP = <pending>
```
<img width="760" height="189" alt="image" src="https://github.com/user-attachments/assets/bd11ab2f-f21e-48db-b66c-30dd80b5d581" />

✔ Understood cloud dependency

---

### 📊 Service Comparison

| Type         | Access            | Use Case           |
| ------------ | ----------------- | ------------------ |
| ClusterIP    | Internal          | Service-to-service, Internal communication between services |
| NodePort     | External via Node | Testing, Development, direct node access |
| LoadBalancer | External Public   | Production traffic in cloud environments        |

---

## 🔍 Key Learnings

* Services solve dynamic IP issue
* Label selectors are critical
* DNS is built into Kubernetes
* LoadBalancer builds on NodePort & ClusterIP

---

## 📸 Proof

<img width="774" height="194" alt="image" src="https://github.com/user-attachments/assets/bd3a7630-ed44-4b7a-a4cb-281682ddce03" />

---

## 🧹 Cleanup

```bash
kubectl delete -f .
```

Each type builds on the previous one:

* LoadBalancer creates a NodePort, which creates a ClusterIP
* So a LoadBalancer service also has a ClusterIP and a NodePort

verify :

`kubectl describe service web-app-loadbalancer`

<img width="671" height="187" alt="image" src="https://github.com/user-attachments/assets/eac58da8-3661-4e4e-8623-25c765a8d885" />

---

## 🚀 Conclusion

Kubernetes Services provide a reliable way to expose and communicate with applications running inside a cluster, ensuring scalability and stability.

---
