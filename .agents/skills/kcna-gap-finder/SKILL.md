---
name: kcna-gap-finder
description: Find gaps in KCNA study notes — missing concepts, commands, or sections. Use when the user asks about gaps, coverage, or what to add.
---

## Steps

### 1. Determine the scope

The user names a topic file, topic number (01–04), or domain name. Confirm the file exists in `topics/`.

**Completion criterion**: A topic file path is confirmed and exists.

### 2. Inventory what's covered

Read the file. For each sub-area in that topic's domain (see Reference), record:
- Whether the three required sections exist (Core concepts and definitions, Key commands and examples, Exam-focused reminders)
- What concepts, commands, and definitions are present
- The depth of coverage — mere mention, explained, or well-covered with examples

**Completion criterion**: Every sub-area in the topic's domain has a coverage assessment.

### 3. Identify gaps

Compare the inventory against the domain expectations. Flag gaps per the taxonomy in Reference: missing sub-area, empty section, missing concept, missing command, thin coverage.

**Completion criterion**: Every expectation in the domain reference for this topic has been checked against the inventory and assigned either a gap type or confirmed as covered.

### 4. Report

Produce a gap report:
- Topic file and domain analyzed
- What's covered, summarized by sub-area
- Each gap, with priority and a specific, actionable recommendation
- End with a count: total gaps, by priority. If zero gaps, state so explicitly.

**Completion criterion**: Every finding from step 3 has a report entry with priority and a concrete, actionable recommendation. The report ends with a summary count.

## Reference

### Domain expectations

**01-kubernetes-fundamentals** — sub-areas:
- **Kubernetes core concepts**: control plane components (kube-apiserver, etcd, kube-scheduler, kube-controller-manager), node components (kubelet, container runtime, kube-proxy), cluster architecture, Kubernetes objects (apiVersion, kind, metadata, spec, status), declarative model, API groups/versions, Pods, multi-container Pods, Deployments, ReplicaSets, DaemonSets, StatefulSets, Jobs, CronJobs, labels/selectors, Services (ClusterIP, NodePort, LoadBalancer), ConfigMaps, Namespaces
- **Administration**: kubectl basics, kubeconfig, contexts, namespaces, cluster-scoped vs namespaced resources, inspecting resources (`kubectl get`, `kubectl describe`, `kubectl explain`, `kubectl api-resources`), `kubectl exec`, `kubectl port-forward`, `kubectl logs`, `kubectl cluster-info`
- **Scheduling**: scheduler role, resource requests and limits, QoS classes (Guaranteed, Burstable, BestEffort), node selection (nodeName, nodeSelector, affinity/anti-affinity, taints/tolerations), Pending state causes
- **Containerization**: container images, image tags, registries, container runtimes, container vs VM isolation, image immutability, container lifecycle, init containers

**02-container-orchestration** — sub-areas:
- **Networking**: Services (ClusterIP, NodePort, LoadBalancer), DNS for Services/Pods (CoreDNS), Ingress, NetworkPolicies (ingress/egress rules), CNI plugin model
- **Security**: RBAC (Roles, ClusterRoles, RoleBindings), ServiceAccounts, Secrets, SecurityContexts, Pod Security Standards (Privileged, Baseline, Restricted), authn/authz basics
- **Troubleshooting**: logs, events, `kubectl describe`, `kubectl exec`, `kubectl top`, debugging Pods (Pending, CrashLoopBackOff, ImagePullBackOff, OOMKilled, probe failures), node conditions, common failure modes
- **Storage**: Volumes (hostPath, emptyDir), PersistentVolumes, PersistentVolumeClaims, StorageClasses, CSI, ephemeral vs persistent storage, ConfigMap/Secret mounted as volumes

**03-cloud-native-architecture** — sub-areas:
- **Observability**: metrics, logs, traces, Prometheus, OpenTelemetry, Fluentd, Jaeger, Envoy, metrics-server, health probes (liveness, readiness, startup), `kubectl top`
- **Cloud native ecosystem and principles**: CNCF role, containers, microservices, declarative APIs, immutable infrastructure, service discovery, auto-scaling (HPA, VPA, Cluster Autoscaler), service mesh (Linkerd, Istio), serverless/FaaS (Knative), CNCF project maturity (sandbox, incubating, graduated), graduated projects, supply chain security (SBOM, image signing)
- **Cloud native community and collaboration**: open source governance, SIGs, WGs, KEPs, CNCF TOC, community meetings, CNCF ambassador program

**04-cloud-native-application-delivery** — sub-areas:
- **Application delivery**: CI/CD concepts, Deployments and rollouts (maxSurge, maxUnavailable, revisionHistoryLimit), rollbacks, `kubectl rollout`, canary/blue-green strategies, Helm (charts, repositories, releases, values), GitOps principles, Argo CD/Flux, image building (Dockerfile, multi-stage builds, tagging), container registries
- **Debugging**: `kubectl debug`, ephemeral containers, `kubectl port-forward`, `kubectl exec`, `kubectl logs`, `kubectl top`, debugging failing Pods (CrashLoopBackOff, ImagePullBackOff, OOMKilled, probe failures), interpreting pod status conditions, resource diagnostics (CPU throttling, memory pressure)

### Gap taxonomy

| Type | Definition | Priority |
|---|---|---|
| **Missing sub-area** | An entire sub-area with no content under any heading | High |
| **Empty section** | A section heading exists but the body contains only sub-headings with no content | High |
| **Missing concept** | A key term or concept from the domain expectations absent from a populated sub-area | Medium |
| **Missing command** | A common kubectl command for the topic not listed in Key commands | Medium |
| **Thin coverage** | A sub-area mentioned but lacking depth — few examples, no exam reminders, definitions without supporting detail | Low |
