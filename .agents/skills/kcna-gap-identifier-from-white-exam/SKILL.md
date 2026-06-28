---
name: kcna-gap-identifier-from-white-exam
description: Identify study-note gaps from a provided KCNA practice exam.
disable-model-invocation: true
---

## Steps

### 1. Capture the white exam

The user provides exam content directly or names a file in the repo. If they name a file, read it. If nothing is provided, ask for it.

**Completion criterion**: An exam artifact is present and readable.

### 2. Extract tested concepts

List every Kubernetes/CNCF concept, command, component, or domain tested by each exam item.

**Completion criterion**: Every exam item has at least one extracted concept.

### 3. Map concepts to topic files

For each extracted concept, identify the topic file (01–04) and required section that should cover it.

**Completion criterion**: Every extracted concept is mapped to a topic file and section.

### 4. Check coverage

Read each relevant topic file. For each mapped concept, decide whether it is present in the correct section, present elsewhere, missing, or thinly covered.

**Completion criterion**: Every mapped concept has a coverage assessment using the gap taxonomy in Reference.

### 5. Report gaps

Produce a report:

- Exam artifact summarized
- Concepts extracted, grouped by topic file
- Each gap, with type, priority, and a concrete recommendation
- End with a count: total concepts checked, gaps by priority

**Completion criterion**: Every finding from step 4 has a report entry with gap type, priority, and recommendation. The report ends with a summary count.

## Reference

### Topic file mapping

| Topic                                | File                                                   | Covers                                                                           |
| ------------------------------------ | ------------------------------------------------------ | -------------------------------------------------------------------------------- |
| 01 Kubernetes fundamentals           | `topics/01-kubernetes-fundamentals/notes.md`           | Cluster architecture, core objects, kubectl basics, scheduling, containerization |
| 02 Container orchestration           | `topics/02-container-orchestration/notes.md`           | Networking, security, troubleshooting, storage                                   |
| 03 Cloud-native architecture         | `topics/03-cloud-native-architecture/notes.md`         | Observability, ecosystem/principles, community                                   |
| 04 Cloud-native application delivery | `topics/04-cloud-native-application-delivery/notes.md` | CI/CD, deployments, rollouts, Helm, GitOps, debugging                            |

### Required sections

Every topic `notes.md` must contain:

- Core concepts and definitions
- Key commands and examples
- Exam-focused reminders

### Gap taxonomy

| Type                | Definition                                                           | Priority |
| ------------------- | -------------------------------------------------------------------- | -------- |
| **Missing concept** | A tested concept absent from the topic file                          | High     |
| **Missing command** | A tested kubectl command absent from Key commands                    | Medium   |
| **Wrong section**   | Concept is present but not in the section where it belongs           | Medium   |
| **Thin coverage**   | Concept is mentioned but lacks definition, example, or exam reminder | Low      |
