# Red Book 133-Word Import Design

## Goal

Import the 133 headwords from
`C:\Users\大爱仙尊\Downloads\bbdc_54270029_20260711105908.pdf` into the
`WORDS` array in `index.html` as complete `考研` learning cards.

The PDF contains 133 unique headwords. The current application has 585 cards:
19 of the PDF headwords already exist and remain unchanged; 114 missing
headwords will be appended. The resulting card count is 699.

## Scope and Constraints

- Modify only `index.html` for the product data change.
- Append the 114 cards after the current final card. Do not insert, sort, or
  rewrite the existing 585 cards because SRS state is stored by `WORDS` index
  in `localStorage['lmsrs11']`.
- Keep the 19 existing cards as the canonical versions. The import is
  case-insensitive and must not create a second card for an existing headword.
- Do not run the historical `Workflow/vocab-batch-insert.js` or `merge*.js`.
  They depend on a Reasonix/DeepSeek runtime or target an obsolete HTML file.
- Do not change PWA, IndexedDB, dictionary-import, service-worker, or UI code.

## Data Sources

1. The supplied PDF is the authoritative ordered headword list.
2. `longman_dict.json` is the authoritative local source for pronunciation,
   bilingual definitions, source examples, word family candidates, and
   collocation candidates for 112 of the 114 new headwords.
3. The original LDOCE5 MDX is the fallback source for `drive` and `attention`,
   which are present in the MDX but absent from the parsed JSON.
4. Existing `WORDS` cards and `.claude/tmp_batches*/*_output.json` are the
   schema and style baseline. They are not a source of copied content for new
   entries.

## Card Construction

Each new record uses the existing top-level contract:
`word`, `phonetic`, `tag`, `cn`, `entries`, `collocations`, `wordFamily`,
`thesaurus`, and `antonyms`.

- `tag` is always `考研`.
- Select at most two relevant parts of speech and at most three core senses per
  headword. Resolve the source JSON's aggregate `pos` and `senses` fields
  before creating the UI-facing `entries` structure.
- Preserve accurate Longman English and Chinese definitions, but write fresh,
  exam-appropriate English examples with Chinese translations rather than
  copying source examples.
- Supply three to five high-value collocations and concise Chinese glosses.
  Source collocations are candidates only because coverage and field shape vary.
- Use source word-family candidates selectively and add Chinese glosses. Avoid
  broad or misleading morphological relatives.
- Add synonyms and antonyms only when the relation is unambiguous; use the
  established empty array/string values when a reliable relation is unavailable.
- Give extra review to multi-part-of-speech or easily confused words, including
  `counsel`, `assure`, `program`, `super`, `prompt`, `respect`, `suit`,
  `profit`, `trigger`, `exploit`, `resort`, `polish`, `lower`, `point`,
  `attack`, `interest`, `human`, `low`, `loss`, and `lose`.

## Execution Flow

1. Recreate the ordered 133-word source list and normalize it for comparison.
2. Parse the current `WORDS` array and derive the 114-word missing set.
3. Produce the new cards in a separate temporary JSON staging file. Validate
   its schema, word set, and JSON syntax before changing `index.html`.
4. Append the validated staging records immediately before the `WORDS` array
   closing bracket, retaining the original 585 records byte-for-byte in their
   parsed representation.
5. Remove the temporary staging artifact after the final merge.

## Verification

- The imported headword set equals the PDF set after accounting for the 19
  pre-existing cards.
- `WORDS.length` changes from 585 to 699, with no case-insensitive duplicates.
- The first 585 parsed objects are identical to the `HEAD` version of
  `index.html`; the 114 appended objects are in PDF order.
- Every added card has all required top-level fields, nonempty `cn`, at least
  one entry and sense, and valid example objects where examples are present.
- Spot-check source fidelity and rendered detail pages for `drive`, `attention`,
  `counsel`, `program`, `proximate`, `intercourse`, `loss`, and `lose`.
- Load the app through a local HTTP server, verify list search/detail rendering,
  start a review session, and confirm existing SRS progress remains addressable.

## Non-Goals

- No full-dictionary reparse or repair of the existing `parse_mdx.py` issue.
- No replacement of the historical Reasonix/DeepSeek workflow.
- No service-worker fix, UI redesign, or reorganization of the existing 585
  cards.
