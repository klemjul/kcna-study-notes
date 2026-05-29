# Container Orchestration

## Networking

### Core concepts and definitions

- A **Service** typically provides a stable virtual IP and DNS name for a set of Pods selected by labels (except headless Services, which have no virtual IP).
- Common Service types are **ClusterIP** (internal), **NodePort** (node-level exposure), and **LoadBalancer** (external LB integration).
- In many clusters, **kube-proxy** programs node networking rules so Service traffic is forwarded to healthy Pod endpoints, though some CNIs replace this behavior.
- Kubernetes DNS resolves Service names (for example, `my-svc.my-ns.svc.cluster.local`); Pod/endpoint records depend on Service type and DNS configuration.
- **NetworkPolicy** defines allowed inbound and outbound traffic for selected Pods; enforcement depends on the cluster CNI plugin.

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

### Exam-focused reminders

- A Service selects Pods by **labels**, not by Pod names.
- **ClusterIP** is the default Service type.
- NetworkPolicy is additive and only works when the CNI plugin supports policy enforcement.

## Security

### Core concepts and definitions

- **Authentication** verifies identity, and **authorization** decides what that identity can do.
- Kubernetes commonly uses **RBAC** for authorization with `Role`/`ClusterRole` plus `RoleBinding`/`ClusterRoleBinding`.
- A **ServiceAccount** provides an identity for Pods when they call the Kubernetes API.
- **Secrets** store sensitive values (for example, credentials, tokens, cert data) and can be consumed as env vars or mounted files.
- **Security context** settings control runtime security properties such as user/group IDs, Linux capabilities, and privilege escalation behavior.

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

### Exam-focused reminders

- **Roles** are namespace-scoped; **ClusterRoles** are cluster-scoped (and can also be bound in a namespace).
- Use least privilege: grant only the verbs/resources required.
- Secrets are base64-encoded in manifests, so RBAC and etcd protection remain important.

## Troubleshooting

### Core concepts and definitions

- Troubleshooting starts with object state (`Pending`, `CrashLoopBackOff`, `ImagePullBackOff`, probe failures, scheduling errors).
- `Events` provide a time-ordered explanation of scheduler, kubelet, and controller actions.
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
- Access modes include `ReadWriteOnce`, `ReadOnlyMany`, and `ReadWriteMany` (driver-dependent).
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
- Binding is primarily driven by requested size, access mode, and StorageClass.
- Reclaim policy (`Delete`/`Retain`) controls what happens to backing storage after PVC release.

## References

- See the root `resources.md` file for official and supplementary links.
