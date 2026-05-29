# Kubernetes Fundamentals

## Cluster architecture

- **Kubernetes** is a portable, extensible, open source platform for managing containerized workloads and services with declarative configuration and automation.
- A **cluster** consists of a **control plane** plus **worker nodes**. The control plane manages cluster state and scheduling, while nodes run Pods.
- Core **control plane components** include **kube-apiserver**, **etcd**, **kube-scheduler**, and **kube-controller-manager**.
- **etcd** stores the cluster's configuration and state data, while the **kube-apiserver** is the main entry point for cluster requests.
- Core **node components** include the **kubelet**, a **container runtime**, and **kube-proxy**.
- The **kubelet** makes sure the Pods assigned to its node are running, while **kube-proxy** helps implement Service networking on nodes.
- A **Kubernetes object** is a persistent record of intent. Common manifest fields are `apiVersion`, `kind`, `metadata`, and `spec`; the system reports current state in `status`.

## Fundamental resources

- A **Pod** is the smallest deployable unit in Kubernetes. It usually runs a single container, but can run tightly coupled containers that share network and storage resources.
- Pods are usually managed through higher-level controllers instead of being created directly.
- A **Deployment** manages Pods declaratively through ReplicaSets. It is commonly used for stateless applications, rollouts, rollbacks, and scaling.
- A **ReplicaSet** keeps a desired number of matching Pods running, but it is usually created and managed by a Deployment rather than used directly.
- A **Service** exposes one or more Pods behind a stable network endpoint. Services usually target Pods through label selectors, which decouples clients from changing Pod IPs.
- Common Service types are **ClusterIP** for internal access, **NodePort** for access through a node port, and **LoadBalancer** when an external load balancer is available.
- **Labels** are key/value pairs attached to objects, and **selectors** are how Deployments and Services match the Pods they manage or expose.
- A **ConfigMap** stores non-confidential configuration data for workloads.
- A **Secret** stores sensitive information such as passwords, tokens, or keys.
- **Namespaces** isolate groups of namespaced resources inside one cluster. Object names must be unique within a namespace, but not across namespaces.

## Declarative model, APIs, and kubectl

- Kubernetes uses a **declarative model**: you describe the desired state, and controllers work continuously to reconcile actual state toward it.
- **API groups and versions** identify which API endpoint a resource uses, such as `v1` for core resources and `apps/v1` for Deployments.
- When you run `kubectl apply -f deployment.yaml`, the manifest is sent to the **kube-apiserver**, stored as desired state, and then controllers, the scheduler, and kubelets work to create and run the matching Pods.
- A Pod often stays in **Pending** when it cannot be scheduled yet, for example because of insufficient cluster resources, node constraints, or storage binding requirements.

## Key commands and examples

- Inspect cluster and workloads:
  - `kubectl get nodes`
  - `kubectl get pods -A`
  - `kubectl get deployments,svc -n <namespace>`
- Inspect resource details:
  - `kubectl describe pod <pod-name> -n <namespace>`
  - `kubectl api-resources`
  - `kubectl explain deployment.spec`
- Apply declarative configuration:
  - `kubectl apply -f manifest.yaml`
  - `kubectl rollout status deployment/<name> -n <namespace>`
  - `kubectl delete -f manifest.yaml`
- Work with namespaces and context:
  - `kubectl get namespace`
  - `kubectl config set-context --current --namespace=<namespace>`
  - `kubectl config use-context <context-name>`

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
- **Services use selectors** to find the matching Pods they expose.
- Default Service type is **ClusterIP**; **NodePort** and **LoadBalancer** expose workloads more broadly.
- **ConfigMaps are for non-sensitive configuration**; **Secrets are for sensitive data**.
- **Namespaces scope many common objects** such as Pods, Services, Deployments, ConfigMaps, and Secrets, but resources like **Nodes** and **PersistentVolumes** are cluster-scoped.
- A Service DNS name follows the pattern `<service>.<namespace>.svc.cluster.local`.
- For production use, avoid treating the `default` namespace as the permanent home for every workload.

## References

- See the root `resources.md` file for official and supplementary links.
