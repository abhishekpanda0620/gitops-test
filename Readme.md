# GitOps with Argo CD on Minikube

This repository demonstrates a simple GitOps workflow using **Argo CD** on a local **Minikube cluster**. The goal is to declaratively manage Kubernetes resources from a Git repository.

---

## 🧱 Tech Stack

- Kubernetes via Minikube
- Argo CD for GitOps
- NGINX (demo app)

---

## 📦 Folder Structure

k8s
├── deployment.yaml # NGINX deployment
└── service.yaml # NodePort service


---

## 🚀 Getting Started

### 1. Prerequisites

- [Docker](https://docs.docker.com/get-docker/)
- [Minikube](https://minikube.sigs.k8s.io/)
- [Kubectl](https://kubernetes.io/docs/tasks/tools/)
- [Git](https://git-scm.com/)
- Internet access (to pull Argo CD and app images)

---

### 2. Start Minikube

```bash
minikube start
```

### 3. Install Argo CD

```bash
kubectl create namespace argocd

kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

```
Wait for all Argo CD pods to be running:

```bash
kubectl get pods -n argocd -w

```
### 4. Access Argo CD UI

Forward Argo CD service to localhost:

```bash
kubectl port-forward svc/argocd-server -n argocd 8080:80

```

Then open:
👉 http://localhost:8080

### 5. Login to Argo CD

Get password using:
```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d && echo

```

- Username: admin

- Password: (the output from above)


### 6. Create Application (via UI or CLI)
**Option A: Argo CD UI**
- Click "New App"

- Fill in:

    - App Name: hello-world

    - Repo URL: https://github.com/abhishekpanda0620/gitops-test

    - Path: k8s

    - Revision: HEAD

    - Cluster URL: https://kubernetes.default.svc

    - Namespace: default

    - Sync policy: Manual or Auto

- Sync policy: Manual or Auto

**Option B: CLI**

```yaml
# argo-app.yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: hello-world
  namespace: argocd
spec:
  destination:
    namespace: default
    server: https://kubernetes.default.svc
  project: default
  source:
    repoURL: https://github.com/abhishekpanda0620/gitops-test
    path: k8s
    targetRevision: HEAD
  syncPolicy:
    automated: {}

```
Apply it:

```bash
kubectl apply -f argo-app.yaml -n argocd
```
### 7. Access the App
```bash
minikube service hello-world-service
```


## 🤝 Contributing
Feel free to fork and improve this project. PRs are welcome!
