# Kubernetes Fundamentals

## Core concepts and definitions

- **Kubernetes** is a portable, extensible, open source platform for managing containerized workloads and services with declarative configuration and automation.
- A **cluster** consists of a **control plane** plus **worker nodes**. The control plane manages cluster state and scheduling, while nodes run Pods.
- Core **control plane components** include **kube-apiserver**, **etcd**, **kube-scheduler**, and **kube-controller-manager**.
- A **Kubernetes object** is a persistent record of intent. Common manifest fields are `apiVersion`, `kind`, `metadata`, and `spec`; the system reports current state in `status`.
- A **Pod** is the smallest deployable unit in Kubernetes. It usually runs a single container, but can run tightly coupled containers that share network and storage resources.
- Pods are usually managed through higher-level controllers instead of being created directly.
- A **Deployment** manages Pods declaratively through ReplicaSets. It is commonly used for stateless applications, rollouts, rollbacks, and scaling.
- A **Service** exposes one or more Pods behind a stable network endpoint. Services usually target Pods through label selectors, which decouples clients from changing Pod IPs.
- **Namespaces** isolate groups of namespaced resources inside one cluster. Object names must be unique within a namespace, but not across namespaces.

## Key commands and examples

- Inspect cluster and workloads:
  - `kubectl get nodes`
  - `kubectl get pods -A`
  - `kubectl get deployments,svc -n <namespace>`
- Inspect resource details:
  - `kubectl describe pod <pod-name> -n <namespace>`
  - `kubectl explain deployment.spec`
- Apply declarative configuration:
  - `kubectl apply -f manifest.yaml`
  - `kubectl rollout status deployment/<name> -n <namespace>`
- Work with namespaces:
  - `kubectl get namespace`
  - `kubectl config set-context --current --namespace=<namespace>`

### Minimal manifest reminder

```yaml
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
          image: nginx:1.14.2
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
```

## Exam-focused reminders

- Think in terms of **desired state**: you declare intent in manifests, and controllers reconcile actual state toward it.
- **Do not rely on Pod IPs** for stable access. Use a **Service** because Pods are ephemeral.
- **Deployments manage Pods** for stateless workloads; ReplicaSets are usually managed indirectly by the Deployment.
- **Namespaces scope many common objects** such as Pods, Services, and Deployments, but resources like **Nodes** and **PersistentVolumes** are cluster-scoped.
- A Service DNS name follows the pattern `<service>.<namespace>.svc.cluster.local`.
- For production use, avoid treating the `default` namespace as the permanent home for every workload.

## Official references

- KCNA certification overview: https://training.linuxfoundation.org/certification/kubernetes-cloud-native-associate/
- Kubernetes concepts overview: https://kubernetes.io/docs/concepts/overview/
- Kubernetes cluster architecture: https://kubernetes.io/docs/concepts/architecture/
- Kubernetes objects: https://kubernetes.io/docs/concepts/overview/working-with-objects/
- Pods: https://kubernetes.io/docs/concepts/workloads/pods/
- Deployments: https://kubernetes.io/docs/concepts/workloads/controllers/deployment/
- Services: https://kubernetes.io/docs/concepts/services-networking/service/
- Namespaces: https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/
