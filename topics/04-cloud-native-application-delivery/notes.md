# Cloud Native Application Delivery

## Core concepts and definitions

- **Application delivery** covers how software moves from source code to a running workload through build, test, release, and deployment steps.
- **Continuous Integration (CI)** focuses on frequent code integration with automated validation.
- **Continuous Delivery/Deployment (CD)** focuses on reliably promoting validated changes into running environments.

### Application delivery

- Kubernetes commonly delivers application changes through declarative manifests and rolling updates.
- A **Deployment** supports rollout and rollback behavior for stateless applications.
- **GitOps** applies the idea that Git stores the desired state for infrastructure and application manifests.

### Debugging

- Debugging starts with identifying whether the problem is in image build, configuration, scheduling, networking, or runtime behavior.
- In Kubernetes, common first checks are resource status, events, logs, and rollout history.

### Observability

- **Observability** combines metrics, logs, and traces to help you understand system behavior.
- Metrics show trends and thresholds, logs show detailed events, and traces show request flow across services.
- Good observability shortens diagnosis time during releases and incidents.

## Key commands and examples

- `kubectl rollout status deployment/<name> -n <namespace>`
- `kubectl rollout history deployment/<name> -n <namespace>`
- `kubectl logs <pod-name> -n <namespace>`
- `kubectl describe deployment <name> -n <namespace>`

## Exam-focused reminders

- CI/CD is about repeatable delivery, not just one-time deployment.
- **GitOps** uses version-controlled desired state and automated reconciliation.
- **Observability** is not just logging; it includes metrics and traces too.

## References

- See the root `resources.md` file for official and supplementary links.
