# Source Folders

Use `sources/` as the low-friction intake layer.

Recommended pattern:

```text
sources/
  hacpo/
    notes.md
    paper.pdf
    repo/
    figures/
```

Rules:

- Create one folder per source bundle or paper family.
- Put any relevant material inside: PDFs, markdown exports, screenshots, code snapshots, datasets, links saved as text, or copied notes.
- `notes.md` is the only recommended human-written file. Keep it short.
- Do not try to pre-structure the wiki here. The LLM should compile structured pages into `papers/`, `topics/`, `ideas/`, and `assets/`.

Minimal `notes.md` example:

```md
# Notes

- Why this matters: on-policy RL for long-horizon agents
- What I want: extract core mechanism, critique evaluation, propose follow-up ideas
- Related topic: [[topics/agent-systems]]
```

If you want a blank starting point, copy `sources/_template/notes.md`.
