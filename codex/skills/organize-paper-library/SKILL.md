---
name: organize-paper-library
description: Organise a Paperpile library — file unsorted papers into folders and labels, propose a folder structure that fits what is actually in the library, and clean up empty or duplicated collections. Use when the user asks to tidy, sort, categorise or restructure their library.
---

# Organise a paper library

Paperpile is a tool for academics. Be calm, efficient and specific, and assume a seasoned researcher in the field is reading. Be direct about what you did and did not find.

- No emojis.
- Bold and italics are fine where they aid scanning; enthusiasm is not.
- Say "I could not find it" rather than padding a weak result.

If this skill is invoked with no further instruction, do not guess at a task. Briefly describe what the skill does, then ask for the one thing you need to start — a topic, a set of papers, or a folder. Where the harness offers a structured question tool, use it; otherwise present numbered options and wait.

## Nothing changes without approval

Every create, update and delete is shown to the user and approved before it is sent. Present the plan as
a table or a tree, and wait for an explicit yes.

Deletion is the one to be careful with: deleting a folder can detach many references at once, and
`trash_references: true` moves everything in it to the trash. Never pass that flag unless the user asked
for it in those words.

## Where to start

Invoked with no direction, offer these and let the user pick:

1. **Look at my library and suggest what to do** — a quick survey, then a prioritised list.
2. **Sort out folders and labels** — propose a structure that fits what is actually in there.
3. **File the unsorted papers** — put the papers with no folder somewhere sensible.
4. **Clean up** — empty collections, near-duplicate names.
5. Something else.

Say up front that nothing will be changed without their approval.

Every library tool works on the personal library unless told otherwise. When the user means a shared
one — "the lab's library", "our group's folder" — call `get_libraries` first and pass the id it returns
as `library_id` to `search_library`, `get_references_from_library`, `get_text_content`,
`highlight_text`, `save_to_library`, `update_references` or `update_collections`. The catalog tools
search the literature rather than a library and take no `library_id` at all.

`get_collections_from_library` is the exception that already spans them: it returns folders and labels
from every shared library you can reach, unless you set `include_shared_libraries: false`.

## Survey the library

`get_collections_from_library` returns every folder and label with its hierarchy, colour and reference
count. `get_references_from_library` with `random_sample: true`, `limit: 100` and
`output_format: "JSON"` gives a representative sample including abstracts, which is what tells you what
the library is actually about.

Then look for: collections with zero references; a large share of papers with no folder; names that
differ only in spacing or case ("DeepLearning" and "Deep Learning"); and themes in the unsorted papers
that no existing collection covers.

Report it as a prioritised list with a sentence of reasoning each, and point at whichever workflow
below follows from it. A library with almost no structure wants folders first, then filing.

## Propose folders and labels

Read the current structure with `get_collections_from_library`, then sample with
`get_references_from_library` (`random_sample: true`, `limit: 100`, `output_format: "JSON"`) and pull
out the recurring research themes, methods and subfields.

Propose collections that match how *this* researcher's work divides, not a generic taxonomy. If they
have thirty papers on transformer architectures and nowhere to put them, that is the gap worth naming.

Show it as a tree, wait for approval, then apply with `update_collections` — `action: "create"`, `type`
of `folder` or `label`, `parent` for a nested folder, `color` (`style_0` … `style_24`) for a label. At
most 50 per call; split anything larger.

## File the unsorted papers

`filter_for_unorganized: true` means *no folder*. A paper with labels but no folder still counts as
unsorted, which is usually right — folders are the primary structure — but say so if the user's library
is organised by label instead.

Preview before committing to the whole library:

1. `get_collections_from_library`, once, for the full set of destinations.
2. `get_references_from_library` with `filter_for_unorganized: true`, `limit: 10`,
   `output_format: "JSON"`.
3. Work out folders and labels for those ten from title, journal and abstract.
4. Show a table — paper, proposed folders, proposed labels — and offer either to carry on across the
   library or to take extra rules from the user first.
5. On approval, apply with `update_references` (`add_folders`, `add_labels`, at most 50 per call).

Then work through the rest. Hand it to a subagent if the library is large enough that the batches would
crowd out the conversation.

Fetch each batch fresh, with no cursor: papers you just filed drop out of the filter, so the next call
returns the next ten. Stop when a batch comes back the same as the last one — those are the papers you
could not place.

Every paper you leave unplaced is worth reporting individually. It should be rare, and when it is not,
that usually means the folder structure has a gap rather than that the paper is odd.

Codex does not fan out on its own — say how many agents to spawn, how the work splits between them, that you will wait for all of them, and what each should return. The session caps concurrent agent threads, so keep a fan-out to about ten and let the rest follow behind it.

- Give each one the prompt below with the bracketed values filled in, and nothing else. It should not load other skills.

## Check what actually landed

Neither write tool throws when a single item fails. `update_references` returns `updated` and `failed`
alongside `succeeded` and `failedCount`; `update_collections` returns one entry per update in
`results`, carrying an `error` on the ones that did not work. A call can come back looking successful
with half its work undone, so read the failures and name them rather than reporting the batch as done.

Two causes worth recognising: a shared library the user only has read access to, and labels in a shared
folder — a `shared_folder` library has none at all, so creating one there fails every time.

## Clean up

`get_collections_from_library` gives the counts. Empty collections and near-duplicate names are the two
findings worth acting on.

There is no merge operation. Merging means: `update_references` to move the references onto the
surviving collection, then `update_collections` with `action: "delete"` on the empty one. Show both
steps in the plan so the user can see nothing is lost, and run them in that order.

## Next

A tidy library is the point at which a saved search becomes useful — new papers on a topic landing in
the right folder. `find-papers` covers the search side.

<Begin: subagent prompt>
You are part of Paperpile's research skills. File unsorted papers into the library's existing folders
and labels, using their metadata. Keep going until you have processed [TARGET_COUNT] papers.

- Call `get_collections_from_library` once, at the start, for the full set of folders and labels.
- Then repeat: `get_references_from_library` with `filter_for_unorganized: true`, `limit: 10`,
  `output_format: "JSON"`, no cursor. Decide folders and labels for those ten from their title, journal
  and abstract, then apply them with `update_references`.
- Papers you file drop out of the filter, so each call returns the next ten. Stop when a batch comes
  back identical to the previous one — those are the ones you could not place.
- Report back every 100 papers with a count and anything you could not place.

Use your own judgement and call the tools directly. Do not write a script for this, and do not load
other skills.
<End: subagent prompt>
