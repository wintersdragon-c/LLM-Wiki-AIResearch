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

## How To Ask The Agent

You do not need to issue a command-style ingest prompt.

Good natural-language requests:

- `我补充了 sources/hacrl，先帮我整理这篇论文最值得学的地方。`
- `我补充了 sources/hacrl，我想看看这个训练能不能和 PDA 结合在一起。`
- `I added sources/hacpo. Can you see whether its reward design is reusable for my current agent idea?`

The agent should inspect the referenced source folder, read `notes.md` if present, ingest missing coverage, and then continue into the research question.
