---
name: auto-highlight-papers
description: Go through a set of papers for a stated focus area and mark the key passages as PDF highlights in the user's Paperpile library, so they are bookmarked for deeper reading. Also the reference for getting at a paper's text at all — full-text search, get_text_content, or downloading PDFs with the Paperpile CLI. Use when the user wants papers gone through for a topic, or asks for the text, the PDF, or a local copy of a paper.
---

# Auto-highlight papers

Paperpile is a tool for academics. Be calm, efficient and specific, and assume a seasoned researcher in the field is reading. Be direct about what you did and did not find.

- No emojis.
- Bold and italics are fine where they aid scanning; enthusiasm is not.
- Say "I could not find it" rather than padding a weak result.

If this skill is invoked with no further instruction, do not guess at a task. Briefly describe what the skill does, then ask for the one thing you need to start — a topic, a set of papers, or a folder. Where the harness offers a structured question tool, use it; otherwise present numbered options and wait.

The researcher still does the reading. This skill finds the passages worth their attention and leaves a
highlight at each one, so the library carries the bookmarks afterwards.

## 1. Settle the focus

You need a focus area or a statement of interest — the thing that makes a passage worth marking. If the
user has not given one, ask for it before reading anything. "Highlight the key parts" without a focus
produces highlights on whatever happened to be quotable, which is worse than none.

## 2. Settle the scope

Which papers: a folder, a label, a search, a list of citekeys. Confirm the shortlist before you start
reading — reading is the expensive half. Use the `find-papers` skill if the user described the papers
rather than naming them.

## 3. Find the passages

Three routes to a paper's text. Pick before you start; switching later wastes a download.

| | `search_library` | `get_text_content` | `paperpile-stage files download` |
| --- | --- | --- | --- |
| Gives you | matching passages, with `fileId` and `pageNumber` | text, in the response | `paper.pdf`, `paper.md`, `reference.bib` on disk |
| Good for | a specific question — it jumps straight to the relevant sections | reading a section in full, quoting | grepping, scripts, handing a file to a subagent |

**Start with `search_library` when the focus is specific.** It already searches PDF full text and
annotations, so a query drawn from the focus area returns the passages themselves rather than papers to
read — and each hit carries the `fileId` and `pageNumber` that `highlight_text` and `get_text_content`
take. Use `get_text_content` to read around a hit before deciding, or when the focus is broad enough
that no query stands in for it. Reach for the CLI when the files themselves are the point: many papers
at once, repeated searching across them, or the user literally asked for the PDF.

## 4. Highlight what matters

Aim for a handful per paper — three to six for a focused question. Prefer few, well-chosen passages
over broad coverage: a highlight on every paragraph is the same as no highlight at all. Mark the
sentence that carries the claim, not the paragraph around it.

## 5. Say what you did

Close with a short summary: the focus you worked to, how many papers you went through, and an
enumeration of what was highlighted — paper, section, and one line on why that passage. This is what
tells the researcher where to start reading, so it is not optional.

Name any paper you could not cover and why: no PDF attached, nothing matching the focus, a scanned
document with no extractable text.

## Reading with get_text_content

Name the paper by `citekey` (or `reference_id`) and its main PDF is used; `file_id` picks a specific
attachment. `ranges` takes up to 50 requests in one call, each of them exactly one of:

- `{ "pages": "4-9" }` — 1-indexed, inclusive. A bare `"7"` is one page.
- `{ "characters": "3500-3800" }` — 0-indexed, end exclusive. A bare `"3500"` reads to the end.
- `{ "all_content": true }`

`max_characters` caps the whole response, 20,000 by default and 100,000 at most. It is spent range by
range in the order given, so a budget too small for the first range leaves the later ones empty —
`text: ""` with `result_truncated: true`, which reads like a missing page but is not. Raise
`max_characters` or ask for fewer ranges.

Each range comes back as `{ range, text, result_truncated }` and nothing else, so the response does not
tell you how long the document is. Only the first 100 pages are ever extracted: an empty result for a
high page number means you have run past what exists, or past that cap, and the two are
indistinguishable from here. Do not report a paper as ending where the text stops.

Batching is the trick worth knowing: one call can pull page 1 of each of twenty papers, or the six
passages you located with `search_library`, for the price of one round trip.

Character offsets describe *this* extraction only. Never store them, never quote them back to the user,
and never reuse them against a different file. Cite a page number, or leave a highlight.

## Downloading with the CLI

The Paperpile CLI is a single binary with no runtime dependencies.

```bash
curl -fsSL https://cli.paperpile.com/install.sh | sh -s -- --channel staging
```

Then sign in once, in a real terminal — it opens a browser:

```bash
paperpile-stage auth login
paperpile-stage auth status
```

You cannot do this on the user's behalf. If `auth status` reports no credential, or a command exits 3,
tell the user to run `paperpile-stage auth login` themselves and stop until they have. In an automated
environment, `PAPERPILE_API_KEY` (from Settings → API keys) is read instead, and it needs read-write
scope: a read-only key can list references but cannot fetch file contents.

Download by citekey, repeating the flag for each paper — at most 50 per call:

```bash
paperpile-stage files download --citekey smith2023method --citekey jones2024result --to ./papers
```

`--reference-id` names a paper by id instead. `--library` targets a shared library. `--no-markdown` and
`--no-bibtex` drop what you do not need; `--supplementary` adds the non-primary attachments.

It writes one folder per reference:

```
papers/
  manifest.json
  <reference_id>/
    paper.pdf
    paper.md              extracted plain text
    reference.bib
    supplementary/        only with --supplementary
```

`manifest.json` is the thing to read afterwards: `references` maps each reference id to `ok` or `error`
with a reason, and `errors` lists the citekeys and ids that matched nothing at all. When output is
piped it is JSON, so the command's own stdout carries the same report.

A paper with no PDF attached comes back as an error for that reference and the rest still download.
Report what failed; do not retry it.

## When there is no PDF

Both routes need a PDF attached to the reference. `get_references_from_library` lists what a reference
has — a `Files:` line in `MARKDOWN`, a `files` array in `JSON` — and nothing there means there is
nothing to read yet. The same listing carries the citekey, which is the handle both routes take.

Every library tool works on the personal library unless told otherwise. When the user means a shared
one — "the lab's library", "our group's folder" — call `get_libraries` first and pass the id it returns
as `library_id` to `search_library`, `get_references_from_library`, `get_text_content`,
`highlight_text`, `save_to_library`, `update_references` or `update_collections`. The catalog tools
search the literature rather than a library and take no `library_id` at all.

`get_collections_from_library` is the exception that already spans them: it returns folders and labels
from every shared library you can reach, unless you set `include_shared_libraries: false`.

`save_to_library` queues a PDF crawl for anything it saves unless `find_pdfs` is false, but the PDF is
never there by the time the call returns and may never arrive. Set `wait_for_pdf: true` when reading the
paper is the very next thing you plan to do, and read `pdf_status` in the result. Otherwise check back
later with `get_references_from_library`.

If a paper is paywalled and the crawl found nothing, say so. The user can attach the PDF from their
browser with the Paperpile extension, and it will be there next time.

## Leaving a highlight

`highlight_text` puts a real PDF highlight on a passage, so it survives in the library instead of only
in this conversation. Give it the `citekey` and the exact `text` — long enough to be unique in the
document — plus an optional `note`, and `color` (`yellow`, `red`, `blue`, `green`).

Use the `note` for why the passage matters. A highlight the researcher finds in three weeks with no
note is a sentence they have to re-derive.

A passage that crosses a page boundary comes back as one note per page. That is expected, not a
duplicate.
