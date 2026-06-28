# Cloud Native Application Delivery

## Application delivery

### Core concepts and definitions

- **Application delivery** in Kubernetes is the process of deploying, updating, and rolling back workloads using declarative manifests.
- **Infrastructure as Code (IaC)** is the practice of managing and provisioning infrastructure through code rather than manual processes, enabling version control, repeatability, and automation.
- A **Deployment** manages stateless application rollout through ReplicaSets and supports rolling updates and rollbacks.
- A **rolling update** replaces Pods gradually to limit downtime and reduce risk during version changes.
- **Readiness probes** control when a Pod can receive traffic, which protects users during startup and rollouts.
- **Liveness probes** detect when a container is unhealthy and trigger a restart to recover from deadlocks or stalls.
- **Startup probes** protect slow-starting containers by disabling liveness and readiness checks until the application has fully started.
- A **rollback** usually means manually reverting to a previous ReplicaSet revision (for example with `kubectl rollout undo`); Deployments do not auto-rollback by default.
- **GitOps** uses a version-controlled repository (usually Git) as the single source of truth for declarative infrastructure and application state, enabling automated, auditable, and consistent deployments.
- **Argo CD** is a declarative GitOps continuous-delivery tool for Kubernetes. An **ApplicationSet** dynamically generates Argo CD Applications from a single manifest connected to a Git repository, enabling continuous deployment across multiple environments.
- **Helm** is a package manager for Kubernetes that uses templated manifests and value files to parameterize deployments for different environments.
- **Kustomize** is a Kubernetes-native configuration management tool that uses bases and overlays to apply environment-specific patches without modifying the original manifests.
- A **Site Reliability Engineer (SRE)** is typically responsible for reliability, uptime, and creating/executing incident management procedures in cloud-native environments.

### Key commands and examples

- Create or update application resources declaratively:
  - `kubectl apply -f deployment.yaml`
  - `kubectl get deploy,rs,pods -n <namespace>`
- Track and control rollout progress:
  - `kubectl rollout status deployment/<name> -n <namespace>`
  - `kubectl rollout history deployment/<name> -n <namespace>`
  - `kubectl rollout undo deployment/<name> -n <namespace>`
- Trigger a new rollout (for example after changing image):
  - `kubectl set image deployment/<name> <container>=<image>:<tag> -n <namespace>`
- Scale a Deployment up or down:
  - `kubectl scale deployment/<name> --replicas=<count> -n <namespace>`
- Restart a Deployment to roll out fresh Pods (for example after ConfigMap/Secret changes):
  - `kubectl rollout restart deployment/<name> -n <namespace>`

### Exam-focused reminders

- Delivery in Kubernetes is usually **declarative** (`apply`) rather than imperative one-off commands.
- Remember Deployment relationships: **Deployment -> ReplicaSet -> Pods**.
- `kubectl rollout status` is the fastest command to confirm whether an update is progressing successfully.
- If a rollout fails, inspect events/probes and then use `kubectl rollout undo` for fast recovery.
- **GitOps** = Git is the single source of truth for declarative infrastructure and application state.
- **Helm** uses templates + value files for environment-specific configuration; **Kustomize** uses overlays and patches without templating.
- **Argo CD** implements GitOps; an **ApplicationSet** generates Applications from a Git-backed manifest.

## Debugging

### Core concepts and definitions

- **Debugging** starts with observing object state (`Pending`, `CrashLoopBackOff`, `ImagePullBackOff`, readiness failures).
- `kubectl describe` helps identify scheduling failures, probe issues, and image pull errors through Events.
- `kubectl logs` is used to inspect container output; use `--previous` to view logs from a crashed container instance.
- Kubernetes troubleshooting is layered: check **Pod -> node scheduling/events -> networking/service -> configuration/secrets**.
- **Ephemeral containers** can be added to running Pods for interactive troubleshooting in some environments.

### Key commands and examples

- Investigate Pod state and recent events:
  - `kubectl get pods -n <namespace>`
  - `kubectl describe pod <pod-name> -n <namespace>`
  - `kubectl get events -n <namespace> --sort-by=.metadata.creationTimestamp`
- Inspect logs and restart behavior:
  - `kubectl logs <pod-name> -n <namespace>`
  - `kubectl logs <pod-name> -n <namespace> --previous`
  - `kubectl logs <pod-name> -c <container-name> -n <namespace>`
- Debug from inside cluster/network context:
  - `kubectl exec -it <pod-name> -n <namespace> -- sh`
  - `kubectl port-forward pod/<pod-name> 8080:80 -n <namespace>`
  - `kubectl debug -it pod/<pod-name> -n <namespace> --image=busybox --target=<container-name> -- sh`

### Exam-focused reminders

- Start from **symptoms**, then validate with `describe`, `logs`, and `events` before changing manifests.
- For restart loops, `logs --previous` is often the key command to identify why the last container crashed.
- Distinguish scheduling issues (Pod never starts) from runtime issues (Pod starts then fails).
- Readiness and liveness failures can look similar; readiness impacts traffic routing, liveness triggers restarts.

## References

- See the root `resources.md` file for official and supplementary links.
