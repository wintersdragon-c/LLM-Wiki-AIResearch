# Directory Purpose

This directory stores topic map pages and conditional survey subpages.

## What Goes Here

- `topics/<topic>.md` topic map pages that define a research direction, core sub-problems, current judgment, and known gaps.
- `topics/<topic>/<subtopic>.md` survey subpages when a topic has 5+ papers or clear route divergence.

## What Does Not Go Here

- Single-paper analysis. Put that in `papers/`.
- Cross-topic research synthesis. Put that in `reviews/`.
- Research idea cards. Put those in `ideas/`.
- Reusable mechanisms or failure modes. Put those in `assets/`.

## Neighboring Directories To Check

- `research-map.md` before creating or moving topic pages.
- `papers/` for source papers linked to the topic.
- `ideas/` for hypotheses and proposals linked to the topic.
- `reviews/` for broader synthesis that should not become a topic page.

## Required Frontmatter

```yaml
type: topic
title: "Topic Title"
slug: topic-slug
tags: [topic]
subtopics: []
papers: []
ideas: []
open_questions: []
created: YYYY-MM-DD
updated: YYYY-MM-DD
```

For survey subpages, use `type: topic-survey`.

## Naming Pattern

- Topic map: `topics/llm-reasoning.md`
- Survey subpage: `topics/llm-reasoning/chain-of-thought.md`

## Good Example

Create `topics/agent-systems.md` immediately when agent systems become a major direction. Create `topics/agent-systems/tool-use.md` only after enough papers or route divergence justify a living survey.

## Common Wrong Placement

Do not create a survey subpage after reading one paper. Update the topic map page until the 5+ papers or route-divergence trigger is met.
