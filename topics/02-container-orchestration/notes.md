# Container Orchestration

## Core concepts and definitions

- **Container orchestration** coordinates containerized workloads across multiple machines so that applications stay available, scalable, and easier to operate than with standalone containers.
- Kubernetes is an orchestrator because it schedules workloads, restarts failed Pods, keeps the desired replica count, and exposes workloads through stable networking primitives.
- Orchestration is useful when you need **service discovery**, **self-healing**, **scaling**, and **rolling updates** across many containers.

### Scheduling

- The **scheduler** chooses a node for each unscheduled Pod based on resource requests, constraints, and cluster state.
- **Requests** influence placement decisions because they represent the amount of CPU and memory a Pod asks the cluster to reserve.
- If no node can satisfy the Pod constraints, the Pod can remain in **Pending** until resources or conditions change.

### Networking and service discovery

- Kubernetes networking aims for Pod-to-Pod communication across nodes without NAT between Pods.
- **Services** provide stable access to a changing set of Pods, while internal DNS helps workloads find each other by name.
- `kube-proxy` helps implement Service networking on nodes.

### Storage and resilience

- Containers are often ephemeral, so persistent application data is usually handled through Kubernetes storage resources rather than the container filesystem alone.
- **PersistentVolumes (PV)** represent storage resources, and **PersistentVolumeClaims (PVC)** request storage for workloads.
- Controllers provide **self-healing** by recreating failed Pods to move actual state back toward desired state.

## Key commands and examples

- `kubectl get pods -A`
- `kubectl describe pod <pod-name> -n <namespace>`
- `kubectl get svc -A`
- `kubectl get pv`
- `kubectl get pvc -A`

## Exam-focused reminders

- Think about **why orchestration exists**: scaling, resilience, placement, service discovery, and operational consistency.
- The **scheduler** places Pods; the **kubelet** runs them on the chosen node.
- **Services** give workloads stable access even when Pods are replaced.
- **Persistent storage** needs Kubernetes storage resources; container filesystems alone are usually not enough for durable state.

## References

- See the root `resources.md` file for official and supplementary links.
