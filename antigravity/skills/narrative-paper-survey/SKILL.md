---
name: narrative-paper-survey
description: Write a short narrative that ties a specific set of papers together — how the ideas developed and how the papers relate — with each paper introduced as a full Paperpile reference. Use when the user asks for an overview, a mini literature review, or the story of a field.
---

# Write a narrative survey

Paperpile is a tool for academics. Be calm, efficient and specific, and assume a seasoned researcher in the field is reading. Be direct about what you did and did not find.

- No emojis.
- Bold and italics are fine where they aid scanning; enthusiasm is not.
- Say "I could not find it" rather than padding a weak result.

If this skill is invoked with no further instruction, do not guess at a task. Briefly describe what the skill does, then ask for the one thing you need to start — a topic, a set of papers, or a folder. Where the harness offers a structured question tool, use it; otherwise present numbered options and wait.

The deliverable is a readable narrative, not a summary of each paper in turn. If the output could be
reordered without loss, it is a list and not a survey — rewrite it.

## 1. Scope it

Which papers, and what thread ties them together. The papers may come from a folder, a label, a search,
or the user's own list. The thread is the part that is usually unstated — ask for it if the request is
just "summarise these".

If the set does not exist yet, use the `find-papers` skill first, and its "survey how a field developed"
workflow in particular.

## 2. Read them

See the `auto-highlight-papers` skill. For a handful of papers, read the sections that carry the argument with
`get_text_content` — abstract, introduction, conclusion — which is often enough for a narrative. For a
larger set, or when methods detail matters, download with `paperpile-stage files download` and run one
subagent per paper.

Subagents run through the CLI's subagent framework, at most 10 at once.

- Grant each one file reads and the Paperpile MCP tools. Subagents only get the tools the main agent hands them.
- Give each one the prompt below with the bracketed values filled in, and nothing else. It should not load other skills.

## 3. Write it

Follow the ideas, not the papers. Where a method or claim came from, what changed it, what remains
open. Group by argument where the chronology does not carry the story, and say so when the order is
thematic rather than temporal.

Introduce each paper with its full reference block at the point where you first credit its
contribution — not in a list at the end.

Whenever you present a paper to the user, use this four-line block. It is the format Paperpile users recognise, and it stays scannable in a terminal.

```
**Title**
Author1 LN, Author2 LN, Author3 LN
*Journal Abbrev* · Year · Article type · N,NNN citations
https://doi.org/10.1000/xyz123
```

- **Title** — the full canonical title, bold.
- **Authors** — `Last F` or `Last FM`, no periods, comma separated. Show all of them up to five; beyond that show the first three, then `... N more ...`, then the last two. Consortium and working-group names are never abbreviated.
- **Metadata** — journal in italics using its NLM abbreviation (single-word titles are never abbreviated: *Nature*, *Science*, *Cell*, *eLife*), then year, then article type (`Journal article`, `Review`, `Preprint`, `Book chapter`), then the citation count with a thousands separator. Separate the fields with ` · `. Omit any field you do not have rather than guessing it — never print `0` or `N/A`. The article type comes from the reference's `type`: library results carry it in `MARKDOWN`, catalog results only in `JSON`.
- **Link** — the canonical `https://doi.org/…` URL as plain text, not a Markdown link, so it can be copied. An arXiv preprint from the last three months may not have reached the DOI resolver yet; use its `https://arxiv.org/abs/…` URL instead. Drop the line if there is neither.

Put `---` before the first entry and after the last. Separators between entries are optional — use them when the list is long enough to need the visual break.

A comment about a paper goes on its own line after the link, as a `-` bullet.

```
---

**Eleven grand challenges in single-cell data science**
Lähnemann D, Köster J, Szczurek E ... 14 more ... Schönhuth A, Rahmann S, Schreiber S
*Genome Biol* · 2020 · Review · 1,098 citations
https://doi.org/10.1186/s13059-020-1926-6

- Frames the field's open problems; useful as an anchor for anything downstream of scRNA-seq.

---
```

Keep it brief. A survey of fifteen papers should read in a few minutes.

Be honest about the shape of the evidence: a claim resting on one paper reads differently from one
three groups have replicated, and the survey should show which is which.

## 4. Offer to keep it

Show the narrative in the conversation, then ask whether to save it — Markdown in the working directory
unless the user wants something else.

<Begin: subagent prompt>
You are part of Paperpile's research skills. Condense one paper's full text so it can be woven into a
narrative survey.

Be straightforward, specific and concise. Your reader is a seasoned academic in the field.

- **Thread the survey follows**: [the theme, or "not specified"]
- **Paper**: [title, year, authors]
- **Full text**: [path to paper.md]

Condense each major section to roughly a tenth of its length, keeping the claims, the methods and the
conclusions that bear on the thread above. Detail that does not bear on it can go.

Reply in this shape:

```
# Section summary

[Each major section, heavily condensed.]

# Quotes

[Four or five quotes that best carry the paper's contribution or its relation to the thread, with page
numbers where you have them.]
```
<End: subagent prompt>
