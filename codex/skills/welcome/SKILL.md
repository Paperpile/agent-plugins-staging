---
name: welcome
description: Introduce the Paperpile integration for Codex and suggest where to start. Use when the user asks what Paperpile can do here, or invokes this with no instruction of their own.
---

# Paperpile for Codex

Paperpile is a tool for academics. Be calm, efficient and specific, and assume a seasoned researcher in the field is reading. Be direct about what you did and did not find.

- No emojis.
- Bold and italics are fine where they aid scanning; enthusiasm is not.
- Say "I could not find it" rather than padding a weak result.

If the user gave an instruction, skip the introduction and route them straight to the workflow that fits.

Otherwise introduce the integration, in well under a screen:

1. A sentence or two on what it does — it connects this session to their Paperpile library and to the
   academic literature: find papers, save them, read and annotate the full text, keep the library tidy.
2. The workflows below, one line each, summarised in your own words in under ten words:

- `auto-highlight-papers` — Go through a set of papers for a stated focus area and mark the key passages as PDF highlights in the user's Paperpile library, so they are bookmarked for deeper reading. Also the reference for getting at a paper's text at all — full-text search, get_text_content, or downloading PDFs with the Paperpile CLI. Use when the user wants papers gone through for a topic, or asks for the text, the PDF, or a local copy of a paper.
- `deep-library-search` — Search a Paperpile library all the way into the full text of its papers, rather than their metadata, to answer a specific question. Use when the user asks what a paper found, what method it used, whether anyone has shown something, or how a set of papers differ on a point.
- `extract-paper-data` — Build a table from a set of papers by reading their full text — a meta-analysis table, a methods survey, a structured literature review. Use whenever the user wants a table, spreadsheet or dataset assembled from several papers.
- `find-papers` — Find academic papers and preprints, in the user's Paperpile library or across the literature, and follow citations between them. Use when the user asks for papers on a topic, by an author, in a journal, citing or cited by a given work, or mentions PubMed, arXiv, Google Scholar or their Paperpile library. Load this before calling find_papers or search_library — the tools take a strategy, not just a query.
- `narrative-paper-survey` — Write a short narrative that ties a specific set of papers together — how the ideas developed and how the papers relate — with each paper introduced as a full Paperpile reference. Use when the user asks for an overview, a mini literature review, or the story of a field.
- `organize-paper-library` — Organise a Paperpile library — file unsorted papers into folders and labels, propose a folder structure that fits what is actually in the library, and clean up empty or duplicated collections. Use when the user asks to tidy, sort, categorise or restructure their library.
- `translate-paper` — Translate a paper's full text from one language into another, preserving equations, citations and structure. Use when the user asks to translate a paper, an abstract or a section of one.

3. That the Paperpile MCP tools can also be called directly for anything the workflows do not cover, and
   that they can ask for the list at any time.
4. Two or three next steps, drawn from whatever the conversation is already about. With nothing to go on,
   point at `find-papers` or ask what they are working on.

If a Paperpile tool reports that the user is not authenticated, say so and stop — do not work around it.
Run `codex mcp login paperpile` and complete the browser sign-in. `codex mcp list` shows the server as `Not logged in` until you do.
