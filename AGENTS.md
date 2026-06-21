# KCNA Study Notes — Agent Guide..

This is a markdown-only study notes repo. No build system, no tests, no CI, no package manager.

## Repository structure

- `README.md` — overview, certification domains, structure
- `checklist.md` — progress tracker using `- [ ]` / `- [x]` task lists
- `resources.md` — **single source** of all external links (do not put links in topic notes)
- `topics/01-kubernetes-fundamentals/notes.md`
- `topics/02-container-orchestration/notes.md`
- `topics/03-cloud-native-architecture/notes.md`
- `topics/04-cloud-native-application-delivery/notes.md`

## Conventions (from `.github/copilot-instructions.md`)

- Each topic `notes.md` must use at least these sections: **Core concepts and definitions**, **Key commands and examples**, **Exam-focused reminders**.
- All external reference links go in `resources.md`, never in topic notes.
- Verify technical content against **official sources first** (Linux Foundation KCNA, CNCF, Kubernetes docs). Treat third-party sources as complementary only.
- If a statement cannot be confirmed from official docs, mark it as a note/question, not a fact.
- Use concise Markdown with clear headings and bullet points.

## Content guidelines

- `resources.md` should contain durable, high-value links — prefer official documentation pages over blog posts.
- `checklist.md` entries use `- [ ]` for unchecked and `- [x]` for checked items.
- There is no dev server, formatter, linter, or test runner — no commands to run.
