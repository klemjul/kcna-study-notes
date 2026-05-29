# Cloud Native Application Delivery

## Application delivery

### Core concepts and definitions

- **Application delivery** in Kubernetes is the process of deploying, updating, and rolling back workloads using declarative manifests.
- A **Deployment** manages stateless application rollout through ReplicaSets and supports rolling updates and rollbacks.
- A **rolling update** replaces Pods gradually to limit downtime and reduce risk during version changes.
- **Readiness probes** control when a Pod can receive traffic, which protects users during startup and rollouts.
- A **rollback** restores a previous ReplicaSet revision when a rollout is unhealthy.

### Key commands and examples

- Create or update application resources declaratively:
  - `kubectl apply -f deployment.yaml`
  - `kubectl get deploy,rs,pods -n <namespace>`
- Track and control rollout progress:
  - `kubectl rollout status deployment/<name> -n <namespace>`
  - `kubectl rollout history deployment/<name> -n <namespace>`
  - `kubectl rollout undo deployment/<name> -n <namespace>`
- Trigger a new rollout (for example after changing image):
  - `kubectl set image deployment/<name> <container>=<image:tag> -n <namespace>`

### Exam-focused reminders

- Delivery in Kubernetes is usually **declarative** (`apply`) rather than imperative one-off commands.
- Remember Deployment relationships: **Deployment -> ReplicaSet -> Pods**.
- `kubectl rollout status` is the fastest command to confirm whether an update is progressing successfully.
- If a rollout fails, inspect events/probes and then use `kubectl rollout undo` for fast recovery.

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
  - `kubectl debug -it <pod-name> -n <namespace> --image=busybox`

### Exam-focused reminders

- Start from **symptoms**, then validate with `describe`, `logs`, and `events` before changing manifests.
- For restart loops, `logs --previous` is often the key command to identify why the last container crashed.
- Distinguish scheduling issues (Pod never starts) from runtime issues (Pod starts then fails).
- Readiness and liveness failures can look similar; readiness impacts traffic routing, liveness triggers restarts.
