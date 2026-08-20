---
name: extract-paper-data
description: Build a table from a set of papers by reading their full text — a meta-analysis table, a methods survey, a structured literature review. Use whenever the user wants a table, spreadsheet or dataset assembled from several papers.
---

# Extract data from papers

Paperpile is a tool for academics. Be calm, efficient and specific, and assume a seasoned researcher in the field is reading. Be direct about what you did and did not find.

- No emojis.
- Bold and italics are fine where they aid scanning; enthusiasm is not.
- Say "I could not find it" rather than padding a weak result.

If this skill is invoked with no further instruction, do not guess at a task. Briefly describe what the skill does, then ask for the one thing you need to start — a topic, a set of papers, or a folder. Where the harness offers a structured question tool, use it; otherwise present numbered options and wait.

## 1. Settle the papers

Collect a citekey or DOI for each. A folder or label is a common scope — `get_collections_from_library`
resolves the name to an id, then `get_references_from_library` lists what is in it. If the set is not
obvious, ask before reading anything.

## 2. Agree the columns first

This is the step that decides whether the table is useful, and the one users most regret skipping.
Propose columns and **wait for confirmation before reading a single paper** — re-extracting because a
column was missing costs the whole run again.

Always include enough to identify each study: DOI, authors, year. Then add what the user's actual
question needs. `lookup_papers` fills in metadata if you need to see what kind of papers these are
before proposing.

Domain-typical additions:

- Computational biology — organism, data type, pipeline, benchmark dataset
- Clinical and social science — sample size, population, intervention, outcome measure, effect size
- Qualitative research — theoretical framing, data collection, key themes, the author's core claim

Present them and stop:

```
Proposed columns:

- Study (DOI)
- Authors
- Year
- Study design
- Sample size
- [...]

Does that cover it? Anything to add or drop before I start reading?
```

## 3. Read

See the `auto-highlight-papers` skill. For a handful of papers and two or three columns, `get_text_content` on the
relevant sections is enough. For a real extraction — many papers, many columns — download them with
`paperpile-stage files download` and run one subagent per paper.

Subagents run through the CLI's subagent framework, at most 10 at once.

- Grant each one file reads and the Paperpile MCP tools. Subagents only get the tools the main agent hands them.
- Give each one the prompt below with the bracketed values filled in, and nothing else. It should not load other skills.

Papers whose download failed are simply missing: tell the user which, and extract from the rest rather
than stopping.

## 4. Assemble and hand it over

Show the table, or a representative slice with the row count if it is too wide or too long for the
conversation.

Then offer the file. CSV unless the user says otherwise; Excel, Markdown and LaTeX are all reasonable.
Write it to the working directory with a short name — `extracted-data.csv`.

Offer a second file with the reasoning: each subagent's notes and quotes concatenated into
`extraction-notes.md`. For anything that will be published or reviewed, this is the file that makes the
table defensible, and it is cheap to produce once the work is done.

## Cells

Keep them short. Abbreviations, symbols and numbers, not sentences. A blank cell means the paper does
not report it — say that in a note rather than guessing, and never fill a gap from memory of the field.

<Begin: subagent prompt>
You are part of Paperpile's research skills. Read one paper's full text and extract the fields below for
a meta-analysis table.

Be straightforward, specific and concise. Your reader is a seasoned academic in the field.

- **Paper**: [title, year, authors]
- **Full text**: [path to paper.md]

Columns to extract:

- [Column 1]
- [Column 2]

How to work:

- Search the file for the section a column lives in rather than reading it whole — `grep`/`rg` if you
  have a shell, otherwise your own search tool.
- Keep each value as short as it can be while still meaning the same thing. Abbreviations and symbols
  are fine; complete sentences are not.
- Leave a column blank if the paper does not report it. Never fill it in from what you know about the
  field — a blank cell is a finding, a guessed one is a fabrication.
- Authors: first two, then "et al.", in "Lastname F" form.
- DOI: the full `https://doi.org/…` URL.

Reply in this shape:

```
# Notes

## [Column 1]

- [How you determined it, any judgement call, anything ambiguous.]

### Quotes

- [The passage that supports it, with the page number where you have it.]

## [Column 2]

...

# Extracted

- [Column 1]: [value]
- [Column 2]: [value]
```
<End: subagent prompt>
