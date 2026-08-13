# Contributing / Content Standards

This is primarily a personal knowledge repository, but it follows the same standards as any professional public repo.

## Content Standards

1. **No exaggerated experience.** Every non-trivial claim uses one of the maturity labels defined in the root [README.md](README.md#6-knowledge-maturity-labels): `Production Experience`, `Hands-on / POC`, `Learning / Overview`, `Reference Architecture`, `Interview Preparation`.
2. **No confidential information.** No employer names, client names, internal system names, proprietary architecture, credentials, tokens, or secrets.
3. **No personal identifiers.** No real names, emails, phone numbers, or personally identifying screenshots.
4. **Technical accuracy over cleverness.** Prefer correct, current, boring answers to trendy but inaccurate ones. Call out when guidance is opinionated versus universally agreed.
5. **Trade-offs over absolutes.** Nearly every architecture decision should include trade-offs and alternatives, not a single "correct" answer.
6. **Emerging technology labeling.** AI, MCP, LLMOps, and agentic architecture content must be explicitly labeled per the maturity scale — never implied as production-scale operational experience unless it genuinely is.
7. **Consistent Markdown.** Use `#`/`##`/`###` heading hierarchy, fenced code blocks with language hints, and relative links between files.
8. **No vendor marketing language.** Describe products by what they do and their trade-offs, not by marketing copy.

## File Naming

- Lowercase, hyphen-separated: `landing-zones.md`, `scenario-questions.md`.
- Every top-level domain folder has a `README.md` as its index.

## Adding a New Topic

1. Pick the correct domain folder.
2. Follow the existing file's structure: **What it is → Why it matters → Architecture considerations → Common mistakes → Interview questions → Strong answer → Follow-ups → Trade-offs**.
3. Link the new file from that folder's `README.md`.
4. If it touches AI/GenAI/MCP, apply a maturity label.

## Pull Requests (if repository is later opened up)

- One topic per PR where practical.
- Keep the tone technical and neutral — no first-person "war stories" unless clearly marked as illustrative/hypothetical for interview-answer practice.
