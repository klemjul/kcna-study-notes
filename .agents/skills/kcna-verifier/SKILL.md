---
name: kcna-verifier
description: Verify KCNA study notes for accuracy against official Kubernetes and CNCF documentation.
disable-model-invocation: true
---

## Steps

### 1. Determine what to verify

The user names a file path. If they don't, ask. If they name a specific heading or claim within the file, narrow scope to that.

**Completion criterion**: A file path is known and the file exists in this repo.

### 2. Extract factual claims

Read the file. List every factual claim — a statement about what Kubernetes does, how a component works, what a command does, or what a term means. Exclude structure, advice, opinions, and links to `resources.md`.

As you extract, flag any claim that:
- Contradicts another claim in the same file (**internal inconsistency**)
- Names a command, flag, API version, or resource absent from the official Kubernetes docs (**likely hallucinated**)
- Makes a version-specific statement without a source (**unverifiable version claim**)

**Completion criterion**: Every factual statement in scope is identified and categorized.

### 3. Verify against authoritative sources

Check each claim against sources in this order:
1. **Kubernetes official documentation** (`kubernetes.io/docs`)
2. **CNCF official materials** (`cncf.io`)
3. **Linux Foundation KCNA curriculum**

Use `webfetch` to pull the relevant doc page. Cross-check exact wording — the model's pretraining is a starting point, not the final answer. For widely-known basics (e.g., "a Pod is the smallest deployable unit"), spot-check one authoritative page rather than verifying from scratch.

Treat third-party blogs, tutorials, and LLM output as **not authoritative** — a claim confirmed only by them is **unverifiable**.

**Completion criterion**: Every claim has a disposition.

### 4. Report

Produce a report listing every issue. For each:
- Claim text (quoted)
- File and line number
- Disposition and explanation
- Source URL and relevant quote (for contradicted findings)

Skip confirmed claims — the report lists only problems. End with a count: total claims checked, confirmed, flagged. If zero issues, state that.

**Completion criterion**: Every finding from step 2 has a report entry, and the report ends with a summary count.

## Reference

### Authoritative source hierarchy

1. `kubernetes.io/docs` — canonical for API objects, commands, concepts
2. `cncf.io` — canonical for cloud-native definitions, landscape, projects
3. Linux Foundation KCNA exam page — defines exam scope
4. `resources.md` — the repo's curated link collection (points at the above)

### What counts as a verifiable claim

- API resource names, versions, and fields (e.g., `apps/v1`, `.spec.replicas`)
- Command names, flags, and subcommands (e.g., `kubectl logs -f`)
- Component responsibilities (e.g., "kube-scheduler assigns Pods to nodes")
- Concept definitions (e.g., "a Pod is the smallest deployable unit")
- Behavioural statements (e.g., "a Pending Pod may be unschedulable")
- Version-specific facts (e.g., features, graduation status)

Not a claim: opinion, advice, structural notes, links to `resources.md`.

### Dispositions

| Disposition | Meaning |
|---|---|
| **Confirmed** | Matches official source |
| **Contradicted** | Conflicts with official source |
| **Unverifiable** | Cannot confirm or refute from official sources |
| **Likely hallucinated** | Names a command, flag, or resource absent from official docs |

### Conventions from AGENTS.md

- Verify against official sources first; third-party sources are complementary only.
- If a claim cannot be confirmed from official docs, mark it as a note/question, not a fact.
