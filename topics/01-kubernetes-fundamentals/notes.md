# Kubernetes Fundamentals

## Core concepts and definitions

### Kubernetes core concepts

- **Kubernetes** is a portable, extensible, open source platform for managing containerized workloads and services with declarative configuration and automation.
- A **cluster** consists of a **control plane** plus **worker nodes**. The control plane manages cluster state and scheduling, while nodes run Pods.
- Core **control plane components** include **kube-apiserver**, **etcd**, **kube-scheduler**, and **kube-controller-manager**.
  - The **kube-apiserver** is the central management entity that exposes the Kubernetes API and processes RESTful requests.
- **etcd** stores the cluster's configuration and state data, while the **kube-apiserver** is the main entry point for cluster requests.
- Core **node components** include the **kubelet**, a **container runtime**, and **kube-proxy**.
- The **kubelet** is an agent that runs on every node. It ensures containers are running as expected, reports node/Pod status to the API server, and manages the container lifecycle through the container runtime.
- The **container runtime** (for example containerd or CRI-O) is the component that directly starts, stops, and monitors containers.
- **kube-proxy** helps implement Service networking on nodes by routing traffic and managing IP rules.
- A node supports up to **110 Pods** by default in Kubernetes validated configurations, including system Pods.

### Kubernetes objects and API basics

- A **Kubernetes object** is a persistent record of intent. Common manifest fields are `apiVersion`, `kind`, `metadata`, and `spec`; the system reports current state in `status`.
- Kubernetes uses a **declarative model**: you describe the desired state, and controllers work continuously to reconcile actual state toward it.
- **API groups and versions** identify which API endpoint a resource uses, such as `v1` for core resources and `apps/v1` for Deployments.
- Kubernetes API versions follow a deprecation policy: GA API versions must not be removed within a major version, and deprecated behaviors must continue to function for at least **one year after their announced deprecation**.
- Services and Pods are typically represented as **YAML objects** in manifests; JSON is also valid but less common for human-authored files.
- A **Pod** is the smallest deployable unit in Kubernetes. It usually runs a single container, but it can also run tightly coupled containers that share a network namespace and can share volumes.
  - A **sidecar** container runs alongside the main container to handle supporting tasks such as logging, monitoring, or configuration management.
- Pods are usually managed through higher-level controllers instead of being created directly.
- Common workload controllers:
  - A **Deployment** manages Pods declaratively through ReplicaSets. It is commonly used for stateless applications, rollouts, rollbacks, and scaling.
  - A **ReplicaSet** keeps a desired number of matching Pods running, but it is usually created and managed by a Deployment rather than used directly.
  - A **DaemonSet** ensures one Pod runs on every (or selected) node.
  - A **StatefulSet** is used for stateful applications that need stable network identities and persistent storage.
  - A **Job** runs one or more Pods to completion. A **CronJob** schedules Jobs; when triggered, the CronJob controller spawns a Job, and the Job controller then creates the Pod.
- **Labels** are key/value pairs attached to objects, and **selectors** are how controllers match the objects they manage.
- A **ConfigMap** supplies non-sensitive configuration data to Pods as environment variables or mounted files.
- The Kubernetes **garbage collector** automatically removes resources that are no longer needed, such as orphaned ReplicaSets, completed Jobs, and terminated Pods.

### Administration

- **Administration** at the KCNA level focuses on reading cluster state, switching contexts, inspecting resources, and understanding which objects are namespaced or cluster-scoped.
- `kubectl` communicates with the **kube-apiserver** and uses kubeconfig data to decide which cluster, user, and context to use.
- **Namespaces** organize many common resources inside one cluster. Object names must be unique within a namespace, but not across namespaces.
- Common administrative tasks include listing API resources, inspecting manifests, and checking whether a resource is namespaced or cluster-scoped.
- Resource metrics (CPU and memory) are exposed through the **`metrics.k8s.io`** API group, provided by the **Metrics Server** cluster addon. `kubectl top` and the Horizontal Pod Autoscaler consume this API.

### Scheduling

- The **scheduler** selects a node for each unscheduled Pod based on cluster state and the Pod's constraints.
- **Resource requests** are important for scheduling because they express how much CPU and memory a Pod asks the cluster to reserve.
- **nodeSelector** is the simplest node-selection mechanism: a Pod spec lists required node labels, and the scheduler places the Pod only on matching nodes.
- **Node affinity** provides more expressive node selection:
  - `requiredDuringSchedulingIgnoredDuringExecution` is a hard requirement; the Pod is scheduled only on matching nodes.
  - `preferredDuringSchedulingIgnoredDuringExecution` is a soft preference; the scheduler tries to honor it but may place the Pod elsewhere.
- Scheduling can also be influenced by selectors, affinity and anti-affinity rules, taints, and tolerations.
- A Pod often stays in **Pending** when it cannot be scheduled yet, for example because no node matches its constraints or available resources, or because its container images are still being downloaded.
- Official Pod phases are **Pending**, **Running**, **Succeeded**, **Failed**, and **Unknown**. Common kubectl `Status` reasons such as **CrashLoopBackOff** (restarts are failing) and **ImagePullBackOff** (image cannot be pulled) describe container conditions, not Pod phases.

### Containerization

- A **container image** packages an application and its dependencies in a portable format that can run consistently across environments.
- Containers are isolated processes that share the host kernel, which makes them lighter than full virtual machines.
- Kubernetes relies on a **container runtime** on each node to pull images and run containers inside Pods.
- The **Open Container Initiative (OCI)** maintains open standards for containers:
  - **OCI Image Format** — defines a common container image format.
  - **OCI Runtime Specification** — defines how a runtime executes a container.
  - **OCI Distribution Specification** — defines how images are distributed and stored.
- **runc** is the reference implementation of the OCI Runtime Specification.
- Common Kubernetes container runtimes include **containerd** and **CRI-O**; both implement the Kubernetes **Container Runtime Interface (CRI)**.
- CRI-O is a lightweight runtime specifically designed for Kubernetes.
- Production image best practices include using **small base images**, **multi-stage builds**, **pinned dependency checksums**, and running vulnerability scanning in CI/CD rather than relying only on runtime scans.
- Images are commonly stored in a **registry**, and using immutable image references helps make deployments more predictable.

## Key commands and examples

### Inspect cluster and workloads

- Inspect cluster and workloads:
  - `kubectl cluster-info`
  - `kubectl get nodes`
  - `kubectl get pods -A`
  - `kubectl get deployments -n <namespace>`
- Inspect resource details:
  - `kubectl describe pod <pod-name> -n <namespace>`
  - `kubectl describe node <node-name>`
  - `kubectl api-resources`
  - `kubectl explain deployment.spec`
- Scale a Deployment:
  - `kubectl scale deployment/<name> --replicas=<count> -n <namespace>`
- View container logs (including terminated multi-container Pods):
  - `kubectl logs <pod-name> -n <namespace> --previous`
  - `kubectl logs <pod-name> -c <container-name> -p -n <namespace>`

### Apply declarative configuration

- Apply declarative configuration:
  - `kubectl apply -f manifest.yaml`
  - `kubectl delete -f manifest.yaml`

### Work with namespaces and context

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
          image: nginx:1.27.4
```

## Exam-focused reminders

### Be ready to explain

- What happens after `kubectl apply -f deployment.yaml` from API submission to running Pods.
- Why a Pod can remain in **Pending**.
- The difference between a **ReplicaSet** and a **Deployment**.
- How kubeconfig, contexts, and namespaces affect `kubectl` behavior.

### High-yield facts

- Think in terms of **desired state**: you declare intent in manifests, and controllers reconcile actual state toward it.
- **Deployments manage Pods** for stateless workloads; ReplicaSets are usually managed indirectly by the Deployment.
- **DaemonSets** run one Pod per node; **StatefulSets** provide stable identity and storage for stateful workloads.
- **Resource requests** affect scheduling decisions; a Pod that cannot be placed stays Pending.
- **Namespaces scope many common objects**, while resources like **Nodes** are cluster-scoped.
- A **Pod** is the unit Kubernetes schedules, not an individual container.
- Container images are portable build artifacts; the runtime on the node is what actually starts the containers.
- The Kubernetes API follows a deprecation policy: GA API versions must not be removed within a major version, and deprecated behaviors must continue to function for at least **one year after their announced deprecation**.
- A Kubernetes node supports up to **110 Pods** by default.
- A CronJob does not directly create a Pod; the **CronJob controller creates a Job**, and the **Job controller creates the Pod**.
- The kubelet runs on **every node** and manages container lifecycle through the container runtime.

## References

- See the root `resources.md` file for official and supplementary links.
