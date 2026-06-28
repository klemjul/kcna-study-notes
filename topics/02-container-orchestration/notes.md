# Container Orchestration

## Networking

### Core concepts and definitions

- A **Service** typically provides a stable virtual IP and DNS name for a set of Pods selected by labels (except headless Services, which have no virtual IP).
- Common Service types are **ClusterIP** (internal), **NodePort** (node-level exposure), **LoadBalancer** (external LB integration), and **ExternalName** (DNS alias).
- An **Endpoint** resource tracks the IP addresses and ports of the Pods that match a Service's selector, enabling the Service to route traffic to them.
- In many clusters, **kube-proxy** programs node networking rules so Service traffic is forwarded to healthy Pod endpoints, though some CNIs replace this behavior.
- Kubernetes DNS resolves Service names (for example, `my-svc.my-ns.svc.cluster.local`); Pod/endpoint records depend on Service type and DNS configuration.
- **NetworkPolicy** defines allowed inbound and outbound traffic for selected Pods; enforcement depends on the cluster CNI plugin.
- Pods on the same node communicate using direct, NAT-less networking through the node-level network namespace provided by the CNI plugin.
- The **Ingress API** defines HTTP/HTTPS routing rules so that an Ingress controller can expose multiple Services through a single external endpoint, using host-based and path-based routing.
- An **IngressClass** defines a class of Ingress controllers. Set `spec.ingressClassName` on an Ingress resource to choose which controller handles it when multiple Ingress controllers are installed.
- The **Gateway API** is a Kubernetes SIG-Network standard for configuring L4 and L7 traffic management, including load balancing, TLS termination, and routing, with support for vendor-specific implementations.
- A **service mesh** (for example Istio or Linkerd) is a dedicated infrastructure layer that manages service-to-service communication, providing traffic management, mTLS encryption, resilience patterns, and observability without changing application code.

### Key commands and examples

- Inspect networking objects:
  - `kubectl get svc -A`
  - `kubectl get endpoints -A`
  - `kubectl get networkpolicy -A`
- Investigate Service behavior:
  - `kubectl describe svc <service-name> -n <namespace>`
  - `kubectl describe endpoints <service-name> -n <namespace>`
- Verify in-cluster name resolution:
  - `kubectl exec -it <pod-name> -n <namespace> -- nslookup <service-name>`
  - `kubectl exec -it <pod-name> -n <namespace> -- dig <service-name>`
- Troubleshoot DNS from the Pod perspective:
  - Run a temporary debug Pod in the same namespace and use `nslookup`/`dig` to confirm that the cluster DNS resolves the target service name end-to-end.

### Exam-focused reminders

- A Service selects Pods by **labels**, not by Pod names.
- **ClusterIP** is the default Service type.
- An **Endpoint** stores the IPs and ports of Pods backing a Service.
- **Ingress** is for L7 HTTP/HTTPS routing; **Gateway API** is the newer standard for both L4 and L7 traffic management.
- NetworkPolicies whitelist traffic for selected Pods; multiple policies selecting the same Pod are unioned. Enforcement requires a CNI plugin that supports NetworkPolicy.
- A **service mesh** focuses on managing network traffic between services (load balancing, encryption, traffic control, observability).

## Security

### Core concepts and definitions

- **Authentication** verifies identity, and **authorization** decides what that identity can do.
- Kubernetes commonly uses **RBAC** for authorization with `Role`/`ClusterRole` plus `RoleBinding`/`ClusterRoleBinding`.
- Other authorization modes include **ABAC** (Attribute-Based Access Control), **Node Authorization** (for kubelet access), and **Webhook Mode** (external authorization service).
- A **ServiceAccount** provides an identity for Pods when they call the Kubernetes API. Kubernetes automatically mounts a projected volume containing the service account token at `/var/run/secrets/kubernetes.io/serviceaccount/token`.
- **Secrets** store sensitive values (for example, credentials, tokens, cert data) and can be consumed as env vars or mounted files.
- **Security context** settings control runtime security properties such as user/group IDs, Linux capabilities, and privilege escalation behavior.
- **Pod Security Standards** define three policy levels:
  - **Privileged** — unrestricted, for trusted workloads.
  - **Baseline** — prevents known privilege-escalation paths while allowing common applications.
  - **Restricted** — most stringent; requires non-root, disallows privilege escalation, blocks hostPath and host networking, drops capabilities, enforces seccomp, and requires a read-only root filesystem.
- The **Pod Security Admission controller** enforces these standards by reading labels on namespaces, such as `pod-security.kubernetes.io/enforce=restricted`.
- **Falco** is a CNCF runtime security tool that detects anomalous container and Pod activity by monitoring system calls.
- **Open Policy Agent (OPA)** is a general-purpose policy engine that Kubernetes can use to validate requests and apply policies across the cluster, commonly through admission control.

### Key commands and examples

- Inspect RBAC and identities:
  - `kubectl get sa -A`
  - `kubectl get roles,rolebindings -A`
  - `kubectl get clusterroles,clusterrolebindings`
- Check effective access:
  - `kubectl auth can-i get pods --as=system:serviceaccount:<namespace>:<serviceaccount> -n <namespace>`
- Inspect Secret metadata and usage:
  - `kubectl get secrets -A`
  - `kubectl describe secret <secret-name> -n <namespace>`
- Apply Pod Security Standards to a namespace:
  - `kubectl label --overwrite ns <namespace> pod-security.kubernetes.io/enforce=restricted`
  - `kubectl label --overwrite ns <namespace> pod-security.kubernetes.io/warn=baseline`
  - `kubectl label --overwrite ns <namespace> pod-security.kubernetes.io/audit=privileged`

### Exam-focused reminders

- **Roles** are namespace-scoped; **ClusterRoles** are cluster-scoped (and can also be bound in a namespace).
- Use least privilege: grant only the verbs/resources required.
- Secrets are base64-encoded in manifests, so RBAC and etcd protection remain important.
- The Pod Security Admission controller reads namespace labels to enforce **Privileged / Baseline / Restricted** standards.
- **Restricted** requires non-root, no privilege escalation, no hostPath/host networking, dropped capabilities, seccomp, and a read-only root filesystem.
- **Falco** is for runtime security monitoring; **OPA** is for policy enforcement and admission control.

## Troubleshooting

### Core concepts and definitions

- Troubleshooting starts with object state (`Pending`, `CrashLoopBackOff`, `ImagePullBackOff`, probe failures, scheduling errors).
- `Events` provide explanations of scheduler, kubelet, and controller actions; they may be aggregated and are not guaranteed to be strictly time-ordered, so sort by timestamp when chronological order matters.
- Container stdout/stderr logs are usually the first source for runtime failure analysis.
- Readiness/liveness/startup probes influence traffic routing and restart behavior.
- Node conditions and resource pressure can cause evictions or scheduling failures.

### Key commands and examples

- Inspect workload status and events:
  - `kubectl get pods -A -o wide`
  - `kubectl describe pod <pod-name> -n <namespace>`
  - `kubectl get events -n <namespace> --sort-by=.metadata.creationTimestamp`
- Inspect logs and container exits:
  - `kubectl logs <pod-name> -n <namespace>`
  - `kubectl logs <pod-name> -n <namespace> --previous`
- Inspect node health:
  - `kubectl get nodes`
  - `kubectl describe node <node-name>`

### Exam-focused reminders

- `describe` is often the quickest way to see scheduling/probe/image errors.
- `--previous` logs help when a container keeps restarting.
- Always confirm namespace/context before troubleshooting to avoid reading the wrong resources.

## Storage

### Core concepts and definitions

- A **Volume** provides storage to containers in a Pod; its lifecycle depends on the volume type.
- **PersistentVolume (PV)** is cluster storage, and **PersistentVolumeClaim (PVC)** is a request for storage by a workload.
- **StorageClass** defines dynamic provisioning behavior (provisioner, reclaim policy, parameters).
- The **Container Storage Interface (CSI)** is an open standard that lets storage vendors write portable drivers for Kubernetes and other orchestrators, preventing vendor lock-in.
- **Ephemeral Storage** is temporary storage tied to the lifecycle of a Pod; it is deleted when the Pod is removed.
- Common Kubernetes storage solutions:
  - **Ceph** — unified distributed storage providing block (RBD), object (RADOS Gateway), and file (CephFS) interfaces.
  - **Rook** — a storage orchestrator for Kubernetes that automates deployment and management of storage systems such as Ceph.
  - **GlusterFS** — distributed file storage.
  - **OpenEBS** — container-attached storage focused on block storage for Kubernetes.
- Access modes include `ReadWriteOnce`, `ReadOnlyMany`, `ReadWriteMany`, and `ReadWriteOncePod` (driver-dependent).
- Persistent storage survives Pod replacement, unlike container writable layers.

### Key commands and examples

- Inspect storage resources:
  - `kubectl get pv`
  - `kubectl get pvc -A`
  - `kubectl get storageclass`
- Investigate claim binding:
  - `kubectl describe pvc <pvc-name> -n <namespace>`
  - `kubectl describe pv <pv-name>`
- Minimal PVC example:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: app-data
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
```

### Exam-focused reminders

- Pods reference **PVCs**, not PVs directly in most workflows.
- A PVC binds to a PV that satisfies its access mode and StorageClass and has at least the requested storage capacity.
- Reclaim policy (`Delete`/`Retain`) controls what happens to backing storage after PVC release.
- **CSI** is the open standard that decouples storage drivers from the Kubernetes core.
- **Ephemeral Storage** is tied to the Pod lifecycle and is not persisted.
- **Ceph** supports block, object, and file interfaces; **Rook** orchestrates Ceph on Kubernetes.

## References

- See the root `resources.md` file for official and supplementary links.
