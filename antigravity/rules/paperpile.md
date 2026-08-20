# Paperpile

The user has Paperpile installed. The `paperpile` MCP server connects this session to their reference
library and to the academic literature.

Load the matching skill before acting on any of these. Each one carries the tool parameters, the search
strategy and the output format that the tools expect, and none of that is guessable from the tool
schemas alone:

- `auto-highlight-papers` — Go through a set of papers for a stated focus area and mark the key passages as PDF highlights in the user's Paperpile library, so they are bookmarked for deeper reading. Also the reference for getting at a paper's text at all — full-text search, get_text_content, or downloading PDFs with the Paperpile CLI. Use when the user wants papers gone through for a topic, or asks for the text, the PDF, or a local copy of a paper.
- `deep-library-search` — Search a Paperpile library all the way into the full text of its papers, rather than their metadata, to answer a specific question. Use when the user asks what a paper found, what method it used, whether anyone has shown something, or how a set of papers differ on a point.
- `extract-paper-data` — Build a table from a set of papers by reading their full text — a meta-analysis table, a methods survey, a structured literature review. Use whenever the user wants a table, spreadsheet or dataset assembled from several papers.
- `find-papers` — Find academic papers and preprints, in the user's Paperpile library or across the literature, and follow citations between them. Use when the user asks for papers on a topic, by an author, in a journal, citing or cited by a given work, or mentions PubMed, arXiv, Google Scholar or their Paperpile library. Load this before calling find_papers or search_library — the tools take a strategy, not just a query.
- `narrative-paper-survey` — Write a short narrative that ties a specific set of papers together — how the ideas developed and how the papers relate — with each paper introduced as a full Paperpile reference. Use when the user asks for an overview, a mini literature review, or the story of a field.
- `organize-paper-library` — Organise a Paperpile library — file unsorted papers into folders and labels, propose a folder structure that fits what is actually in the library, and clean up empty or duplicated collections. Use when the user asks to tidy, sort, categorise or restructure their library.
- `translate-paper` — Translate a paper's full text from one language into another, preserving equations, citations and structure. Use when the user asks to translate a paper, an abstract or a section of one.

For anything the workflows do not cover, call the `paperpile` MCP tools directly.
