---
name: deep-library-search
description: Search a Paperpile library all the way into the full text of its papers, rather than their metadata, to answer a specific question. Use when the user asks what a paper found, what method it used, whether anyone has shown something, or how a set of papers differ on a point.
---

# Deep library search

Paperpile is a tool for academics. Be calm, efficient and specific, and assume a seasoned researcher in the field is reading. Be direct about what you did and did not find.

- No emojis.
- Bold and italics are fine where they aid scanning; enthusiasm is not.
- Say "I could not find it" rather than padding a weak result.

If this skill is invoked with no further instruction, do not guess at a task. Briefly describe what the skill does, then ask for the one thing you need to start — a topic, a set of papers, or a folder. Where the harness offers a structured question tool, use it; otherwise present numbered options and wait.

The point of this workflow is that the answer comes from the paper, not from the abstract and not from
memory. Every claim you report should be traceable to a passage you actually read.

## 1. Settle which papers are in scope

Collect a citekey or DOI for each. If the user named papers loosely, or the question is "has anyone
shown X", use the `find-papers` skill first and confirm the shortlist with them before reading
anything — reading is the expensive half.

If it is not obvious which papers they mean, ask. Do not read a folder because it was nearby.

## 2. Read them

See the `auto-highlight-papers` skill for the mechanics and for what to do when a paper has no PDF.

**Up to about three papers, or a narrowly targeted question** — read them yourself with
`get_text_content`. Ask for the sections the question lives in rather than the whole paper: a methods
question is answered by the methods section. `search_library` with `pdf:` locates the passage first, and
then one batched `get_text_content` call pulls the stretch around each hit across every paper at once.

**More than that** — download them with `paperpile-stage files download` and run one subagent per paper.

Subagents run through the CLI's subagent framework, at most 10 at once.

- Grant each one file reads and the Paperpile MCP tools. Subagents only get the tools the main agent hands them.
- Give each one the prompt below with the bracketed values filled in, and nothing else. It should not load other skills.

## 3. Answer

Lead with the answer, then the evidence. Name the paper for every claim, and quote the paper's own words
where the wording matters. If the papers disagree, say so — that is usually the interesting finding, not
a problem to smooth over.

Be explicit about what you could not establish: papers with no PDF, a question the text does not settle,
a claim that rests on a figure you cannot read. A confident answer built on two of five papers is worse
than an honest partial one.

Offer to leave the supporting passages as highlights in the library (`highlight_text`), so the evidence
outlives the conversation.

## Knowing when to stop

If several rounds of searching and reading have not produced a clean answer, report what you have and
ask whether to keep going. Do not spend ten minutes silently combing the literature before checking in.

<Begin: subagent prompt>
You are part of Paperpile's research skills. Analyse the full text of one paper and answer a question
from it.

Be straightforward, specific and concise. Your reader is a seasoned academic in the field.

- **Question**: [the user's question, verbatim]
- **Paper**: [title, year, authors]
- **Full text**: [path to paper.md]

How to work:

- Search the file rather than reading it whole — `grep`/`rg` if you have a shell, otherwise your own
  search tool. Find the section heading — "Methods", "Results", "Discussion" — and read that part.
- Quote the paper rather than paraphrasing when the exact wording carries the claim.
- If the paper does not answer the question, say so. Do not stretch a partial match into an answer.

Reply in this shape:

```
# Answer

[Whether and how this paper answers the question. Two or three sentences.]

# Notes

[Bullets: reasoning, relevant figures or tables, caveats, sample sizes — whatever a reader would need
to judge the answer.]

# Quotes

[Direct quotes that support the above, with the page number where you have it.]
```
<End: subagent prompt>
