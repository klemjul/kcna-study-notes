# Cloud Native Architecture

## Observability

### Core concepts and definitions

- **Observability** is the ability to understand system behavior from telemetry data.
- The three common telemetry signals are **metrics**, **logs**, and **traces**.
- **Metrics** are numeric measurements over time (such as CPU usage or request latency).
- **Logs** are timestamped event records produced by applications and platform components.
- **Traces** represent end-to-end request flow across services and help identify where latency or errors occur. Distributed tracing tracks the entire journey of a user request from frontend through backend microservices, making it the right tool for request-flow visibility.
- Kubernetes exposes operational signals through component logs, events, and metrics endpoints.
- **Health probes** let Kubernetes check container health: **liveness** (restart if unhealthy), **readiness** (stop sending traffic), and **startup** (delay liveness/readiness until startup completes).
- Key CNCF observability projects:
  - **Prometheus** — metrics collection and alerting; pulls metrics from targets via HTTP, stores time-series data, uses PromQL for queries.
    - Prometheus metric types: **Counter** (always increasing), **Gauge** (can go up or down), **Histogram** (samples grouped in buckets), and **Summary** (similar to histogram but calculates configurable quantiles).
  - **kube-state-metrics** — exposes the current state of Kubernetes objects (Deployments, Pods, Nodes, etc.) as Prometheus metrics by scraping the API server.
  - **OpenTelemetry** — standardized framework for generating, collecting, and exporting telemetry (metrics, logs, traces); provides SDKs, the OTLP protocol, and a Collector component to decouple data sources from backends.
  - **Fluentd** — pluggable log aggregation and forwarding; collects logs from multiple sources, transforms them, and routes to backends. **Fluent Bit** is a lightweight variant optimized for container/edge environments.
  - **Jaeger** — distributed tracing; captures spans (units of work), propagates trace context across services, and supports sampling to manage overhead.
  - **Envoy** — high-performance proxy that also serves as an observability data plane, providing network-level metrics, tracing, and access logs when used in service meshes.
- The **metrics-server** is a lightweight cluster addon that collects CPU and memory metrics from kubelets and serves them to `kubectl top` and the Horizontal Pod Autoscaler. It is not enabled by default in most clusters.

### Key commands and examples

- Inspect workload logs:
  - `kubectl logs <pod-name> -n <namespace>`
  - `kubectl logs <pod-name> -c <container-name> -n <namespace>`
- Inspect recent cluster events:
  - `kubectl get events -A --sort-by=.metadata.creationTimestamp`
- Inspect resource usage (if Metrics Server is installed):
  - `kubectl top nodes`
  - `kubectl top pods -A`
- Inspect pod conditions and failure clues:
  - `kubectl describe pod <pod-name> -n <namespace>`
- Check container logs for probe failures:
  - `kubectl logs <pod-name> --previous` (inspect logs of a previous crashed container)
- Inspect container restart counts (high restart counts may indicate failing liveness probes):
  - `kubectl get pods -A`

### Exam-focused reminders

- Be able to distinguish when to use logs vs metrics vs traces (logs → specific events; metrics → aggregate trends/alerts; traces → request flow and latency breakdown).
- `kubectl describe` and `kubectl get events` are high-yield first steps in troubleshooting.
- `kubectl top` depends on the **metrics-server** cluster addon (not built into the API server by itself).
- **Liveness probe** restarts the container on failure; **readiness probe** controls Service traffic; **startup probe** protects slow-starting containers from liveness kills.
- Prometheus and OpenTelemetry are the two most commonly referenced CNCF observability projects on the exam.
- Know what each graduated observability project provides: Prometheus → metrics+alerts, Fluentd → log aggregation, Jaeger → distributed tracing, Envoy → proxy/observability data plane.
- **kube-state-metrics** exposes cluster object state (not resource usage) as Prometheus metrics.
- Prometheus metric types: Counter, Gauge, Histogram, Summary.

## Cloud native ecosystem and principles

### Core concepts and definitions

- Per CNCF, cloud native technologies empower organizations to build and run scalable applications in modern, dynamic environments such as public, private, and hybrid clouds.
- Cloud native systems commonly use **containers**, **microservices**, **declarative APIs**, and **automation**.
- Common cloud-native personas:
  - **Platform Engineers** design, build, and maintain the underlying platform infrastructure so application developers can deploy and run services efficiently.
  - **FinOps** focuses on optimizing cloud costs and financial management of cloud spending.
  - **Site Reliability Engineers (SREs)** are typically responsible for reliability, uptime, and incident management procedures.
- **Immutable infrastructure** means servers and containers are never modified after deployment; changes are shipped as new images or instances that replace the old ones entirely.
- **Service discovery** enables services to locate each other dynamically. Kubernetes provides it via DNS (CoreDNS assigns stable DNS names to Services and Pods), environment variables, and ClusterIP Services. Service meshes layer richer routing and discovery on top.
- Kubernetes is a key orchestration platform in the cloud native ecosystem, but the ecosystem also includes observability, networking, runtime, and delivery projects.
- CNCF hosts projects at three maturity stages: sandbox, incubating, and graduated.
- The CNCF Landscape is used to navigate categories of tools and projects.
- Key CNCF graduated projects (high-yield for the exam):
  - **Helm** — package manager for Kubernetes
  - **Prometheus** — metrics collection and alerting
  - **Envoy** — high-performance service proxy / data plane
  - **etcd** — distributed key-value store (Kubernetes backing store)
  - **containerd** — container runtime
  - **Fluentd** — unified logging layer
  - **Jaeger** — distributed tracing
  - **Linkerd** — lightweight service mesh
  - **Argo** — GitOps and workflow engine
- **Supply chain security** protects the integrity of software from source to production:
  - **SBOM** (Software Bill of Materials) lists every component, library, and dependency in a software artifact, enabling vulnerability tracking and license compliance.
  - **Image signing** (e.g., with cosign or Notary) cryptographically verifies that container images have not been tampered with and come from a trusted source.
- **Auto-scaling** adjusts resources to meet demand: **Horizontal Pod Autoscaler (HPA)** scales Pod replicas, **Vertical Pod Autoscaler (VPA)** adjusts resource requests/limits, and the **Cluster Autoscaler** adds or removes nodes.
  - HPA uses metrics (CPU, memory, or custom) to add/remove Pod replicas.
  - VPA recommends or automatically sets CPU/memory requests; applying VPA changes typically requires the Pod to be evicted and recreated.
  - Cluster Autoscaler adjusts the number of worker nodes based on pending Pods that cannot be scheduled.
- A **service mesh** (e.g., Linkerd, Istio) abstracts service-to-service communication into a dedicated infrastructure layer, providing observability, traffic control, and security without changing application code.
  - **Linkerd** is a lightweight, Rust-based service mesh focused on simplicity and low resource overhead.
  - **Istio** is a feature-rich service mesh that uses Envoy as a sidecar proxy; it provides advanced traffic management (canary rollouts, circuit breaking), mTLS, and observability via a control plane (istiod).
- **Serverless / FaaS** (Functions as a Service) lets you run code in response to events without managing servers, often implemented in Kubernetes via projects like Knative or on cloud platforms.
- **OpenID Connect (OIDC)** is an authentication protocol that extends OAuth 2.0 to provide a standardized way for clients to verify user identity and obtain profile information from identity providers.

### Key commands and examples

- Quickly inspect Kubernetes component versions:
  - `kubectl version`
- Explore available API groups/resources:
  - `kubectl api-resources`
  - `kubectl api-versions`
- Inspect labels used for selecting and grouping workloads:
  - `kubectl get pods -A --show-labels`
- Inspect horizontal pod autoscalers:
  - `kubectl get hpa -A`

### Exam-focused reminders

- Focus on principles and trade-offs, not memorizing every CNCF project.
- Be able to match key graduated projects to their purpose (Helm → packaging, Prometheus → metrics, Envoy → proxy, containerd → runtime, etcd → data store).
- Kubernetes is central to cloud native operations, but it is one part of a broader ecosystem.
- **Immutable infrastructure** and **declarative configuration** are recurring cloud native themes — replace, don't patch.
- Know the three auto-scaling mechanisms and which dimension each controls (HPA → replicas, VPA → resources, Cluster Autoscaler → nodes).
- Linkerd and Istio are the two primary service mesh projects; know the difference: Linkerd = lightweight/simple, Istio = feature-rich/Envoy-based.
- **Platform Engineers** build the platform; **FinOps** optimizes cloud cost; **SREs** own reliability and incident management.
- **OIDC** (OpenID Connect) is an authentication layer on top of OAuth 2.0.
- Supply chain security concepts (SBOM, image signing) are increasingly tested; understand their role in securing the software lifecycle.

## Cloud native community and collaboration

### Core concepts and definitions

- CNCF and Kubernetes are open source communities with vendor-neutral governance.
- Community collaboration happens through public repositories, issues, pull requests, and design discussions.
- Kubernetes work is organized through SIGs (Special Interest Groups), Working Groups (WGs), and community meetings. Examples include SIG-Apps (application workloads), SIG-Networking (cluster networking), and SIG-Security (security hardening). Each SIG holds regular public meetings and maintains its own subprojects within the kubernetes GitHub organization.
- Kubernetes design proposals follow the **KEP** (Kubernetes Enhancement Proposal) process.
- The Cloud Native Computing Foundation provides events, working groups, and end-user/community programs; the **TOC** (Technical Oversight Committee) stewards the technical direction.
- The **CNCF Ambassador Program** recognizes community members who advocate for cloud native technologies through speaking, content creation, and mentoring; ambassadors help grow the community globally.

### Key commands and examples

- Explore the Kubernetes community repository:
  - `git clone https://github.com/kubernetes/community.git` (contains SIG charters, KEPs, meeting notes)
- Find SIG meeting information:
  - Browse `kubernetes/community/sig-list.md` for the current SIG/WG roster and meeting calendars
- Use repository workflows for collaboration:
  - Open issues for bug reports/questions on relevant kubernetes/ or CNCF repositories
  - Open pull requests for proposed changes; KEPs use a dedicated template in `kubernetes/enhancements`

### Exam-focused reminders

- Understand the role of CNCF as a foundation and ecosystem steward.
- Know that collaboration is open, asynchronous, and standards-driven.
- KEPs are the formal way Kubernetes features are proposed and designed; they live in the `kubernetes/enhancements` repository.
- SIGs own specific technical areas (e.g., SIG-Apps, SIG-Security) and drive feature development within their scope.
- The CNCF Ambassador Program reflects the community's commitment to global advocacy and inclusivity.
- For exam scenarios, connect community practices with project reliability and long-term sustainability.

## References

- See the root `resources.md` file for official and supplementary links.
