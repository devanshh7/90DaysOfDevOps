# Kubernetes Architecture and Cluster Setup

## K8s Story
- K8s was created in order to solve the problem of manually scaling , healing, etc. as the k8s comes to play it is used for container orchestration and can easily do auto sacling, healing .

Problem with Docker is that :
- Managing container difficult: 1-2 containers -> easy , 100+ containers -> chaos
- Manual Scaling
- No self-healing: If container crashes -> it stays down


- K8s was created bby Google based on their internall system called "Borg".

  As they were already running milions of container , so they need automation at massive scale. In 2014 they created Borg (Automatic scaling and healing etc.) and it works well , so they OSS it and it spublic version is what KUBERNETES is.

- Kubernetes means: The abbreviation “K8s” is also commonly used, where “8” represents the eight letters between “K” and “s.”

. The name "Kubernetes" comes from the Ancient Greek word for "helmsman" or "pilot"

-------------------

### Task 2: Draw the Kubernetes Architecture

![ad7bade2-8dc1-4547-935f-2a3972a74d70](https://github.com/user-attachments/assets/b0874042-1e28-4049-bac4-5315a2cd4d64)

**Control Plane (Master Node):**

- API Server — the front door to the cluster, every command goes through it

- etcd — the database that stores all cluster state

- Scheduler — decides which node a new pod should run on

- Controller Manager — watches the cluster and makes sure the desired state matches reality

**Worker Node:**

- kubelet — the agent on each node that talks to the API server and manages pods
- kube-proxy — handles networking rules so pods can communicate
- Container Runtime — the engine that actually runs containers (containerd, CRI-O)

```
What happens when you run kubectl apply -f pod.yaml? Trace the request through each component.
What happens if the API server goes down?
What happens if a worker node goes down?
```

What happens when:
`👉 kubectl apply -f pod.yaml`
-

**Step-by-step flow (write like this):**

1. kubectl sends request → API Server

2. API Server validates & stores data → etcd

3. Scheduler checks → assigns pod to a node

4. kubelet (on that node) → sees new pod

5. kubelet → tells container runtime to run container

6. kube-proxy → ensures networking works

**👉 One-line summary:**

`kubectl → API Server → etcd → Scheduler → kubelet → Container Runtime`


2) => **If API Server goes down?**

- Cluster cannot be controlled

- kubectl commands will fail

- Existing pods keep running (no immediate crash)

3) **If Worker Node goes down?**

- Pods on that node stop working

- Controller Manager detects failure

- Scheduler creates new pods on other nodes

--------

### Task 3: Install kubectl (How to Approach)

Check:

`kubectl version` : if error

- Go to official website and follow the steps and download the kubectl

-----------

### Task 4: Setup Cluster

**kind**

- Runs Kubernetes using Docker containers

- Lightweight

- Best for DevOps learners

```
kind create cluster --name devops-cluster
kubectl get nodes
```

output:

<img width="698" height="392" alt="image" src="https://github.com/user-attachments/assets/6a9d3659-6293-4998-94f5-2cb6325bf782" />

<img width="780" height="108" alt="image" src="https://github.com/user-attachments/assets/7766c29d-a70e-4149-b953-848486c55f61" />

-------

### Task 5: Explore Cluster

**Commands Meaning:**

**👉 Check cluster**

` kubectl cluster-info`

<img width="976" height="111" alt="image" src="https://github.com/user-attachments/assets/615c44ea-216b-4c72-a1d2-aeef58d5a419" />

**👉 Nodes**

`kubectl get nodes`

<img width="780" height="108" alt="image" src="https://github.com/user-attachments/assets/4dfb9184-f714-4f7e-b865-45d785116be5" />

**👉 Full node details**

`kubectl describe node <node-name>`

<img width="1061" height="567" alt="image" src="https://github.com/user-attachments/assets/da357958-7e0c-4ce9-b153-de75365c2716" />

**See system pods:**

`kubectl get pods -n kube-system`

<img width="927" height="211" alt="image" src="https://github.com/user-attachments/assets/1fffe900-84af-46b6-b8bb-57e76a9102be" />

You will see:

kube-apiserver

etcd

kube-scheduler

kube-controller-manager

kube-proxy

coredns

**IMPORTANT:**

```
_The components of Kubernetes architecture run as pods inside the kube-system namespace._
```
----------

### Task 6: Cluster Lifecycle (Muscle Memory)

🔁**Delete cluster**

`kind delete cluster --name devops-cluster`

<img width="773" height="106" alt="image" src="https://github.com/user-attachments/assets/3c7f5f3f-ed93-45db-b740-7e8d13f47408" />

**🔁 Recreate**

`kind create cluster --name devops-cluster`

<img width="682" height="97" alt="image" src="https://github.com/user-attachments/assets/e74327c5-e789-4e4c-9a87-77436297de3b" />

## 🔍 Context Commands

**👉 Current cluster**

`kubectl config current-context`

<img width="556" height="45" alt="image" src="https://github.com/user-attachments/assets/6dc50eb7-22b4-4d9d-aecf-f04a98d9f289" />

**👉 All clusters**

`kubectl config get-contexts`

**👉 Full config**

`kubectl config view`

<img width="950" height="451" alt="image" src="https://github.com/user-attachments/assets/d4da23d1-56c7-411f-bfe8-0c9bdc692a28" />


## Final Concept (VERY IMPORTANT)
**What is kubeconfig?**

_kubeconfig is a configuration file that stores cluster details, user credentials, and contexts, allowing kubectl to connect to the correct Kubernetes cluster._

**Where is it stored?**

👉 Default location:

`~/.kube/config `
