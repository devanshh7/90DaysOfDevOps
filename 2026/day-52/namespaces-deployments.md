# Kubernetes Namespaces and Deployments

## Overview

Today I learned how Kubernetes uses namespaces to isolate resources, and how Deployments replace bare Pods for running workloads — adding self-healing, scaling, rolling updates, and rollbacks.

---

## Core concepts

### Namespaces

A namespace is a virtual cluster inside your physical cluster. Resources in one namespace are isolated from resources in another. Use them to separate environments (dev, staging, production) or teams.

![k8s_namespaces_deployments_architecture](https://github.com/user-attachments/assets/39112610-c77d-4df1-abe4-15ddbb383fbc)

```
kubectl get namespaces
```

| Namespace | Purpose |
|---|---|
| `default` | Where resources go when you don't specify a namespace |
| `kube-system` | Kubernetes internal components — never touch these |
| `kube-public` | Publicly readable resources |
| `kube-node-lease` | Node heartbeat tracking |

### Deployments vs bare Pods

| Feature | Bare Pod | Deployment |
|---|---|---|
| Self-healing | No — deleted = gone | Yes — controller recreates it |
| Scaling | Manual, one at a time | `--replicas=N` |
| Rolling updates | No | Yes — zero downtime |
| Rollback | No | Yes — `kubectl rollout undo` |
| Used in production | Never | Always |

A Deployment creates a `ReplicaSet` behind the scenes. The ReplicaSet creates and manages the actual Pods. You mostly interact with the Deployment, not the ReplicaSet directly.

```
Deployment → ReplicaSet → Pod, Pod, Pod
```

---

## Task 1 — Explore default namespaces

```bash
kubectl get namespaces
kubectl get pods -n kube-system
```
<img width="488" height="133" alt="image" src="https://github.com/user-attachments/assets/48b8ca52-5356-40af-9b1a-0323031ca162" />

**What I saw in kube-system:**

These are the control plane components keeping the cluster alive. Common pods you'll see include `etcd`, `kube-apiserver`, `kube-controller-manager`, `kube-scheduler`, and CNI/DNS components.

**Verify:** `kube-system` had `___8___` pods running.

But the main components of k8s architecture is there: _core dns, control,plane, api server, etcd, controller manager, kube proxy, kube scheduler_

<img width="733" height="186" alt="image" src="https://github.com/user-attachments/assets/a2367fbf-c1d8-46d2-a40b-ae5adfd526b3" />

> Do not delete, edit, or exec into anything in `kube-system`. These are the brains of the cluster.

---

## Task 2 — Create and use custom namespaces

### Imperative creation

```bash
kubectl create namespace dev
kubectl create namespace staging
kubectl get namespaces
```
<img width="598" height="232" alt="image" src="https://github.com/user-attachments/assets/ed73284d-01fc-49d5-9022-f378625bc5c9" />

### Declarative creation (from manifest)

**File: `namespace.yaml`**

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: production
```
<img width="306" height="319" alt="image" src="https://github.com/user-attachments/assets/b85d2a63-7b3c-4a86-ac2d-6d8c529df8c8" />

```bash
kubectl apply -f namespace.yaml
```
<img width="456" height="176" alt="image" src="https://github.com/user-attachments/assets/b2f2d3ae-c451-43bd-b66c-be1cdaf8e396" />

### Deploy pods into specific namespaces

```bash
kubectl run nginx-dev --image=nginx:latest -n dev
kubectl run nginx-staging --image=nginx:latest -n staging
```
<img width="876" height="517" alt="image" src="https://github.com/user-attachments/assets/5188b65c-f941-483e-aee7-60d9d03df788" />

### Listing pods across namespaces

<img width="563" height="116" alt="image" src="https://github.com/user-attachments/assets/56993c0a-7d36-4cc7-a278-82b27947eeef" />

```bash
# Only shows default namespace — these pods won't appear here
kubectl get pods

# Shows pods in a specific namespace
kubectl get pods -n dev

# Shows all pods across ALL namespaces
kubectl get pods -A
```

**Key insight:** `kubectl get pods` without `-n` always defaults to the `default` namespace. Any pod in `dev` or `staging` is invisible without explicitly targeting that namespace or using `-A`.

---

## Task 3 — Create your first Deployment

**File: `nginx-deployment.yaml`**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  namespace: dev
  labels:
    app: nginx
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
        image: nginx:1.24
        ports:
        - containerPort: 80
```
<img width="440" height="397" alt="image" src="https://github.com/user-attachments/assets/4bbe0f5e-e6c6-4cbf-bd9f-254b9b11047e" />

<img width="653" height="185" alt="image" src="https://github.com/user-attachments/assets/9fdaf114-017d-48df-bdd2-818544741a9d" />

### Key differences from a standalone Pod

| Field | Pod | Deployment |
|---|---|---|
| `apiVersion` | `v1` | `apps/v1` |
| `kind` | `Pod` | `Deployment` |
| `replicas` | N/A | Number of pod copies to maintain |
| `selector` | N/A | How the Deployment identifies its pods |
| `template` | N/A | The pod blueprint the Deployment uses |

**Critical rule:** `selector.matchLabels` must exactly match `template.metadata.labels`. If they don't match, the Deployment cannot find or manage its pods.

```bash
kubectl apply -f nginx-deployment.yaml
kubectl get deployments -n dev
kubectl get pods -n dev
kubectl get replicasets -n dev
```
<img width="654" height="125" alt="image" src="https://github.com/user-attachments/assets/f3474c0c-0e8d-48ed-90c7-ed02f700123f" />

### Understanding the Deployment output columns

| Column | Meaning |
|---|---|
| `READY` | `running/desired` — e.g. `3/3` means all 3 desired pods are running |
| `UP-TO-DATE` | Number of pods that have been updated to match the current desired spec |
| `AVAILABLE` | Number of pods ready to serve traffic (passed readiness checks) |

---

## Task 4 — Self-healing

The most important difference between a Deployment and a bare Pod.

```bash
# List current pods (note one of the pod names)
kubectl get pods -n dev

# Delete one pod
kubectl delete pod <pod-name-here> -n dev

# Immediately check again
kubectl get pods -n dev
```
<img width="725" height="236" alt="image" src="https://github.com/user-attachments/assets/9876fae6-2320-4003-89b3-4b9f3cb38ed3" />

**What happened:** The Deployment controller noticed only 2 of 3 desired replicas existed. It immediately created a replacement pod. The new pod has a completely different randomly generated name — it is a brand new pod, not the same one restarted.

**Verify:** The replacement pod had a different name. It reached `Running` status within seconds.

> This is why you use Deployments in production — individual pods are treated as disposable. The Deployment guarantees the desired state is always maintained.

---

## Task 5 — Scale the Deployment

### Imperative scaling

```bash
# Scale up to 5 replicas
kubectl scale deployment nginx-deployment --replicas=5 -n dev
kubectl get pods -n dev

# Scale down to 2 replicas
kubectl scale deployment nginx-deployment --replicas=2 -n dev
kubectl get pods -n dev
```
<img width="760" height="348" alt="image" src="https://github.com/user-attachments/assets/976f8388-ee29-4082-84c6-25cd419e39ba" />

**When scaling down:** Kubernetes terminates pods (newest first) until the count matches. Terminated pods show `Terminating` status briefly, then disappear entirely.

### Declarative scaling

Edit `replicas:` in `nginx-deployment.yaml` and re-apply:

```bash
# Change replicas: 3 to replicas: 4 in the file, then:
kubectl apply -f nginx-deployment.yaml
```
<img width="453" height="406" alt="image" src="https://github.com/user-attachments/assets/a0bed466-15af-415e-8614-80723c0b935a" />

**Verify:** After scaling down from 5 to 2, the 3 extra pods were terminated. The remaining 2 pods continued running without interruption.

<img width="644" height="181" alt="image" src="https://github.com/user-attachments/assets/58f0a1dd-6ad2-4796-87bc-4d3059688bd1" />

---

## Task 6 — Rolling update and rollback

### Trigger a rolling update

```bash
kubectl set image deployment/nginx-deployment nginx=nginx:1.25 -n dev
```

Kubernetes replaces pods one at a time — a new pod is started and confirmed healthy before the old one is terminated. This means zero downtime.

```bash
# Watch the rollout progress in real time
kubectl rollout status deployment/nginx-deployment -n dev

# See all past revisions
kubectl rollout history deployment/nginx-deployment -n dev
```

### Roll back to the previous version

```bash
kubectl rollout undo deployment/nginx-deployment -n dev
kubectl rollout status deployment/nginx-deployment -n dev

# Confirm the image version
kubectl describe deployment nginx-deployment -n dev | grep Image
```

**Verify:** After rollback, the image returned to `nginx:1.24`. Every `apply`, `set image`, and `rollout undo` creates a new entry in rollout history.

### How rolling updates work internally

When you update a Deployment, Kubernetes creates a **new ReplicaSet** for the new version and scales it up while scaling down the old ReplicaSet — one pod at a time. The old ReplicaSet is kept (scaled to 0) so rollbacks are instant.

```
Old ReplicaSet: 3 pods  →  2 pods  →  1 pod  →  0 pods
New ReplicaSet: 0 pods  →  1 pod   →  2 pods  →  3 pods
```

---

## Task 7 — Clean up

```bash
kubectl delete deployment nginx-deployment -n dev
kubectl delete pod nginx-dev -n dev
kubectl delete pod nginx-staging -n staging
kubectl delete namespace dev staging production

# Verify everything is gone
kubectl get namespaces
kubectl get pods -A
```
<img width="881" height="558" alt="image" src="https://github.com/user-attachments/assets/2d27c3ed-af1c-4020-9604-618383d906d2" />

**Verify:** Only the four default Kubernetes namespaces remain (`default`, `kube-system`, `kube-public`, `kube-node-lease`). No custom pods visible anywhere.

> **Warning:** `kubectl delete namespace <name>` deletes everything inside that namespace. In production, treat this command with extreme caution.

---

## Summary of useful commands

| Command | What it does |
|---|---|
| `kubectl get namespaces` | List all namespaces |
| `kubectl create namespace <n>` | Create a namespace imperatively |
| `kubectl get pods -n <ns>` | List pods in a specific namespace |
| `kubectl get pods -A` | List pods across all namespaces |
| `kubectl apply -f deployment.yaml` | Create or update a Deployment |
| `kubectl get deployments -n <ns>` | List deployments in a namespace |
| `kubectl get replicasets -n <ns>` | See ReplicaSets created by Deployments |
| `kubectl scale deployment <n> --replicas=N -n <ns>` | Scale a deployment |
| `kubectl set image deployment/<n> <container>=<image> -n <ns>` | Trigger a rolling update |
| `kubectl rollout status deployment/<n> -n <ns>` | Watch a rollout in real time |
| `kubectl rollout history deployment/<n> -n <ns>` | View revision history |
| `kubectl rollout undo deployment/<n> -n <ns>` | Roll back to previous revision |
| `kubectl describe deployment <n> -n <ns>` | Full deployment details |
| `kubectl delete namespace <n>` | Delete a namespace and everything in it |

---

## Key concepts learned

**Namespaces isolate everything.** Pods, deployments, services — all are namespace-scoped. Two teams can have a deployment named `nginx-deployment` as long as they're in different namespaces and they won't collide.

**Deployments own pods through labels.** The `selector.matchLabels` field is how a Deployment finds and manages its pods. If you manually create a pod with matching labels, the Deployment will adopt it and count it as one of its replicas.

**ReplicaSets are the actual replica manager.** You interact with Deployments, but Deployments delegate replica management to ReplicaSets. Old ReplicaSets are kept at scale 0 to enable instant rollbacks.

**Rolling updates are the default strategy.** Kubernetes never takes all pods down at once. It ensures at least some pods are always running throughout the update. This is configurable via `strategy.rollingUpdate.maxUnavailable` and `maxSurge`.

**Rollback is just another update.** `kubectl rollout undo` doesn't go backwards — it creates a new revision that happens to match an older spec. The history keeps growing forward.

---

*Day 52 complete — next up: Services and networking (Day 53)*
