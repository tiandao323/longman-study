# Red Book 132-Word Import Design

## Goal

Import the 132 headwords from
`C:\Users\大爱仙尊\Downloads\red book132.pdf` into the `WORDS` array in
`index.html` as complete `考研` learning cards.

The current application has 699 cards. The PDF is the authoritative ordered
source list. Any PDF headword that already exists in `WORDS` remains unchanged;
only missing headwords are appended.

## Scope and Constraints

- Modify only `index.html` for the product data change.
- Append new cards after the current final card. Do not insert, sort, or rewrite
  existing cards because SRS state is stored by `WORDS` index in
  `localStorage['lmsrs11']`.
- Keep existing cards as canonical versions. Matching is case-insensitive and
  must not create duplicates.
- Do not change PWA, IndexedDB, dictionary-import, service-worker, or UI code.
- Keep temporary extraction, generation, and coverage files under
  `D:\English note\tmp\redbook-20260713-132\`.

## Data Sources

1. `red book132.pdf` supplies the ordered headword list.
2. `longman_dict.json` supplies pronunciation, bilingual definitions, source
   sense structure, examples, word-family candidates, collocations, synonyms,
   and antonyms where available.
3. The original LDOCE5 MDX is the fallback only for PDF words missing from the
   parsed JSON.
4. Existing `WORDS` cards define the schema and style baseline.

## Card Construction

Each new record uses the established top-level contract:
`word`, `phonetic`, `tag`, `cn`, `entries`, `collocations`, `wordFamily`,
`thesaurus`, and `antonyms`.

- `tag` is always `考研`.
- Include exam-relevant standard written-English senses. Exclude only clearly
  archaic, dialectal, slang-only, or overly specialist senses.
- Preserve accurate dictionary definitions and Chinese glosses, but write fresh
  bilingual examples suitable for exam reading rather than copying source
  examples.
- Include high-value phrasal verbs, fixed frames, preposition patterns,
  collocations, word-family members, synonyms, and antonyms when reliable.
- Empty synonym or antonym fields are preferred over weak or misleading
  relations.

## Execution Flow

1. Extract and normalize the 132-word PDF list.
2. Parse current `WORDS` and derive the missing set.
3. Generate temporary candidate cards and coverage records before editing
   `index.html`.
4. Validate JSON syntax, required fields, missing word coverage, duplicate
   prevention, and card count.
5. Append only validated new cards to the end of `WORDS`.

## Verification

- The PDF word set is fully accounted for as either pre-existing or appended.
- `WORDS.length` increases by exactly the number of missing PDF words.
- Existing 699 cards are unchanged and stay in their original order.
- No case-insensitive duplicate headwords exist after the import.
- Every added card has required top-level fields, nonempty `cn`, at least one
  entry and sense, and valid bilingual examples.
- `index.html` parses successfully as JavaScript data after merge.

## Non-Goals

- No UI redesign or learning-flow change.
- No full dictionary reparse.
- No cleanup of historical batch artifacts unrelated to this import.
