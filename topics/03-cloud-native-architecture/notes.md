# Cloud Native Architecture

## Observability

### Core concepts and definitions

- **Observability** is the ability to understand system behavior from telemetry data.
- The three common telemetry signals are **metrics**, **logs**, and **traces**.
- **Metrics** are numeric measurements over time (such as CPU usage or request latency).
- **Logs** are timestamped event records produced by applications and platform components.
- **Traces** represent end-to-end request flow across services and help identify where latency or errors occur.
- Kubernetes exposes operational signals through component logs, events, and metrics endpoints.

### Key commands and examples

- Inspect workload logs:
  - `kubectl logs <pod-name> -n <namespace>`
  - `kubectl logs <pod-name> -c <container-name> -n <namespace>`
- Inspect recent cluster events:
  - `kubectl get events -A --sort-by=.lastTimestamp`
- Inspect resource usage (if Metrics Server is installed):
  - `kubectl top nodes`
  - `kubectl top pods -A`
- Inspect pod conditions and failure clues:
  - `kubectl describe pod <pod-name> -n <namespace>`

### Exam-focused reminders

- Be able to distinguish when to use logs vs metrics vs traces.
- `kubectl describe` and `kubectl get events` are high-yield first steps in troubleshooting.
- `kubectl top` depends on cluster metrics collection (not built into the API server by itself).

## Cloud native ecosystem and principles

### Core concepts and definitions

- Per CNCF, cloud native technologies empower organizations to build and run scalable applications in modern, dynamic environments.
- Cloud native systems commonly use **containers**, **microservices**, **declarative APIs**, and **automation**.
- Kubernetes is a key orchestration platform in the cloud native ecosystem, but the ecosystem also includes observability, networking, runtime, and delivery projects.
- CNCF hosts projects at different maturity stages (such as sandbox, incubating, and graduated).
- The CNCF Landscape is used to navigate categories of tools and projects.

### Key commands and examples

- Quickly inspect Kubernetes component versions:
  - `kubectl version --short`
- Explore available API groups/resources:
  - `kubectl api-resources`
  - `kubectl api-versions`
- Inspect labels used for selecting and grouping workloads:
  - `kubectl get pods -A --show-labels`

### Exam-focused reminders

- Focus on principles and trade-offs, not memorizing every CNCF project.
- Kubernetes is central to cloud native operations, but it is one part of a broader ecosystem.
- Declarative configuration and automation are recurring cloud native themes.

## Cloud native community and collaboration

### Core concepts and definitions

- CNCF and Kubernetes are open source communities with vendor-neutral governance.
- Community collaboration happens through public repositories, issues, pull requests, and design discussions.
- Kubernetes work is organized through SIGs (Special Interest Groups) and community meetings.
- The Cloud Native Computing Foundation provides events, working groups, and end-user/community programs.

### Key commands and examples

- Show project metadata and source links for Kubernetes packages:
  - `kubectl version`
- Get help text and official command references quickly:
  - `kubectl help`
  - `kubectl options`
- Use repository workflows for collaboration:
  - Open issues for bug reports/questions
  - Open pull requests for proposed changes

### Exam-focused reminders

- Understand the role of CNCF as a foundation and ecosystem steward.
- Know that collaboration is open, asynchronous, and standards-driven.
- For exam scenarios, connect community practices with project reliability and long-term sustainability.

## References

- See the root `resources.md` file for official and supplementary links.
