# Kubernetes Manifests and Your First Pods

## Overview

Today I learned the structure of a Kubernetes manifest and used it to create Pods — the smallest deployable unit in Kubernetes.

---

## The anatomy of a Kubernetes manifest

Every Kubernetes resource is defined using a YAML manifest with four required top-level fields:

```yaml
apiVersion: v1          # Which API version to use
kind: Pod               # What type of resource
metadata:               # Name, labels, namespace
  name: my-pod
  labels:
    app: my-app
spec:                   # The actual specification (what you want)
  containers:
  - name: my-container
    image: nginx:latest
    ports:
    - containerPort: 80
```

| Field | Purpose |
|---|---|
| `apiVersion` | Tells Kubernetes which API group to use. For Pods, it's `v1`. For Deployments, it's `apps/v1`. |
| `kind` | The resource type. Today: `Pod`. Later: `Deployment`, `Service`, etc. |
| `metadata` | The identity of your resource. `name` is required. `labels` are key-value pairs for organisation and selection. |
| `spec` | The desired state. For a Pod: which containers to run, which images, which ports, commands, env vars, etc. |

---

## Task 1 — Nginx Pod

**File: `nginx-pod.yaml`**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  labels:
    app: nginx
spec:
  containers:
  - name: nginx
    image: nginx:latest
    ports:
    - containerPort: 80
```

**Commands run:**

```bash
#Apply it:
kubectl apply -f nginx-pod.yaml

Verify:
kubectl get pods
kubectl get pods -o wide

# Detailed info about the pod
kubectl describe pod nginx-pod

#Read the logs
kubectl logs nginx-pod

# Get a shell inside the container
kubectl exec -it nginx-pod -- /bin/bash

# Inside the container:
curl localhost:80
exit
```
<img width="718" height="416" alt="image" src="https://github.com/user-attachments/assets/f8bc31a1-c2ff-415f-90a2-856d841353d6" />

<img width="722" height="147" alt="image" src="https://github.com/user-attachments/assets/5bd567dd-927e-4d63-8d85-1853ef171c2d" />

<img width="716" height="360" alt="image" src="https://github.com/user-attachments/assets/93b20fa8-8838-46d4-802f-8aefe1805d1b" />

<img width="719" height="340" alt="image" src="https://github.com/user-attachments/assets/828a1f5b-9eac-4312-8731-4c5946d2ca8e" />



**Verification:** Running `curl localhost:80` from inside the container returned the Nginx welcome page HTML, confirming the container was serving traffic correctly.

> **Note:** `containerPort: 80` is documentation only — it does not expose the port externally. External access requires a `Service` resource.

---

## Task 2 — BusyBox Pod

**File: `busybox-pod.yaml`** *(written from scratch)*

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: busybox-pod
  labels:
    app: busybox
    environment: dev
spec:
  containers:
  - name: busybox
    image: busybox:latest
    command: ["sh", "-c", "echo Hello from BusyBox && sleep 3600"]
```

**Commands run:**
<img width="704" height="398" alt="image" src="https://github.com/user-attachments/assets/de723e34-754d-49e1-ab22-f8b0fb600495" />

```bash
kubectl apply -f busybox-pod.yaml
kubectl get pods
kubectl logs busybox-pod
```
<img width="683" height="164" alt="image" src="https://github.com/user-attachments/assets/60c28241-934f-4038-ad62-a24c8b9d300e" />

**Verification:** `kubectl logs busybox-pod` printed:

```
Hello from BusyBox
```

**Why `sleep 3600`?**
BusyBox has no long-running server process. Without a sleep command, the container exits immediately after printing the message. Kubernetes then retries the container and it enters `CrashLoopBackOff`. The `sleep 3600` keeps the container alive for 1 hour.

---

## Task 3 — Imperative vs declarative

### Declarative (what we've been doing)

Write YAML → `kubectl apply -f file.yaml` → Kubernetes reconciles the desired state.

### Imperative (no YAML needed)

```bash
kubectl run redis-pod --image=redis:latest
kubectl get pods
```
<img width="729" height="148" alt="image" src="https://github.com/user-attachments/assets/08d8b644-e4cf-4cff-b1d2-39d3a88a31e2" />

### Inspecting the generated YAML

```bash
kubectl get pod redis-pod -o yaml
```
<img width="1009" height="895" alt="image" src="https://github.com/user-attachments/assets/288ae812-5477-4407-939f-88802b711b82" />

Kubernetes automatically adds extra fields that don't appear in hand-written manifests:

- `uid` — unique identifier for this resource
- `resourceVersion` — internal version tracking
- `creationTimestamp` — when the Pod was created
- `status` block — live state (phase, podIP, containerStatuses, etc.)

### Dry-run scaffold trick

```bash
kubectl run test-pod --image=nginx --dry-run=client -o yaml > test-pod.yaml
```

This generates a valid manifest without creating any resource. Useful for quickly scaffolding a starting template.

**Key difference:** A hand-written manifest has only the 4 required fields. The Kubernetes-generated YAML includes a full `status` block and many auto-populated metadata fields.

<img width="894" height="522" alt="image" src="https://github.com/user-attachments/assets/07377557-0f94-4602-9657-58b3b9df4456" />

---

## Task 4 — Validate before applying

```bash
# Client-side syntax check (no cluster needed)
kubectl apply -f nginx-pod.yaml --dry-run=client

# Server-side schema validation (hits the API)
kubectl apply -f nginx-pod.yaml --dry-run=server
```
<img width="732" height="116" alt="image" src="https://github.com/user-attachments/assets/988b2dcd-4072-413b-a929-945b546350da" />

### Breaking the manifest deliberately

Removed the `image:` field from the nginx-pod.yaml and ran:

```bash
kubectl apply -f nginx-pod.yaml --dry-run=server
```

**Error received:**
<img width="758" height="77" alt="image" src="https://github.com/user-attachments/assets/f7d95bbe-8046-42ec-92de-188389de9d62" />
```
The Pod "nginx-pod" is invalid:
spec.containers[0].image: Required value
```

Kubernetes clearly identifies the missing required field, the container index it belongs to, and why it failed.

---

## Task 5 — Pod labels and filtering

### Listing all labels

```bash
kubectl get pods --show-labels
```

### Filtering by label

```bash
kubectl get pods -l app=nginx
kubectl get pods -l environment=dev
```

### Imperatively adding and removing labels

```bash
# Add a label
kubectl label pod nginx-pod environment=production

# Verify
kubectl get pods --show-labels

# Remove a label (note the trailing dash)
kubectl label pod nginx-pod environment-
```
<img width="810" height="481" alt="image" src="https://github.com/user-attachments/assets/f9ca43dc-df6e-46d7-b16d-56cbbda59dfc" />

### Third Pod with 3+ labels

**File: `multi-label-pod.yaml`**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: multi-label-pod
  labels:
    app: myapp
    environment: staging
    team: backend
spec:
  containers:
  - name: alpine
    image: alpine:latest
    command: ["sleep", "3600"]
```
<img width="452" height="385" alt="image" src="https://github.com/user-attachments/assets/328f91e1-c710-4bfe-a061-148441d436d7" />

```bash
kubectl apply -f multi-label-pod.yaml

# Filter by different labels
kubectl get pods -l team=backend
kubectl get pods -l environment=staging
kubectl get pods -l app=myapp
```
<img width="731" height="149" alt="image" src="https://github.com/user-attachments/assets/5c6751da-2856-4927-9805-36379ed6b510" />

**Key insight:** Labels are just key-value pairs. They have no meaning to Kubernetes itself — only to selectors (used by Services, Deployments, etc. to find the right Pods).

---

## Task 6 — Clean up

```bash
# Delete individually
kubectl delete pod nginx-pod
kubectl delete pod busybox-pod
kubectl delete pod redis-pod
kubectl delete pod multi-label-pod

# Or delete using the manifest file
kubectl delete -f nginx-pod.yaml

# Verify everything is gone
kubectl get pods
```

**Output:**
<img width="694" height="167" alt="image" src="https://github.com/user-attachments/assets/bd78dd75-e290-4072-bdc8-35b2ff5c1fac" />

```
No resources found in default namespace.
```

**Critical insight:** When you delete a standalone Pod, it is gone forever. There is no controller to recreate it. This is why in production you use `Deployments` — a controller that automatically recreates Pods if they crash or get deleted.

---

## Summary of useful commands

| Command | What it does |
|---|---|
| `kubectl apply -f file.yaml` | Create or update a resource from a file |
| `kubectl get pods` | List all pods in the current namespace |
| `kubectl get pods -o wide` | List pods with node and IP info |
| `kubectl get pods --show-labels` | List pods with their labels |
| `kubectl get pods -l key=value` | Filter pods by label |
| `kubectl describe pod <name>` | Full details + events (great for debugging) |
| `kubectl logs <name>` | Container stdout/stderr |
| `kubectl exec -it <name> -- /bin/sh` | Shell inside the container |
| `kubectl label pod <name> key=value` | Add a label imperatively |
| `kubectl label pod <name> key-` | Remove a label |
| `kubectl delete pod <name>` | Delete a pod by name |
| `kubectl delete -f file.yaml` | Delete using the manifest file |
| `kubectl apply -f file.yaml --dry-run=client` | Validate YAML without applying |
| `kubectl apply -f file.yaml --dry-run=server` | Validate against the cluster API |
| `kubectl run <name> --image=<img> --dry-run=client -o yaml` | Scaffold a manifest template |

---

## Key concepts learned

**Pods are ephemeral.** A standalone Pod has no controller. If it dies or gets deleted, it stays gone. Deployments solve this.

**Labels are the glue.** Services and Deployments use label selectors to find Pods. Consistent labelling (`app`, `environment`, `team`) is a production best practice.

**Declarative > Imperative.** `kubectl apply -f` is the production way. `kubectl run` is useful for quick tests and scaffolding templates via `--dry-run=client -o yaml`.

**`containerPort` is documentation.** It does not open any firewall rule or route external traffic. You need a `Service` for that (Day 53+).

**`CrashLoopBackOff` means the container keeps exiting.** Always ensure your container has a long-running process. For utility containers, use `sleep 3600` or similar.

---

*Day 51 complete — next up: Deployments (Day 52)*
