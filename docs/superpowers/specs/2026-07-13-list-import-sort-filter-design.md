# List Import And Sort Filter Design

## Goal

Add a list-page import-date filter inspired by the "不背单词" UI style. The home
page should stay clean: users tap `按导入时间` to enter a dedicated date-filter
page, select one or more import dates, then confirm to return to the word list.
Remove the little-used status filter chips from the home row.

## Scope

- Modify only the list page UI and list rendering logic in `index.html`.
- Do not change the `WORDS` data, detail page, SRS storage, or study-mode queue.
- The new filters affect only the home list display.
- Keep status badges on each word item, but remove status filtering by 新词,
  学习中, 待复习, and 已掌握.

## Import Batch Rules

The app does not store per-card timestamps, so batch membership is derived from
the stable append ranges in `WORDS`:

- `0-584`: original/old cards.
- `585-698`: 2026-07-11 import.
- `699-810`: 2026-07-13 import.

These ranges match the existing import history and preserve SRS index behavior.
The date-filter page is generated from the batch configuration. If future
imports append cards after the last configured range, the page automatically
shows an extra `新增` row for those unfiled cards until the batch configuration
is named properly.

## UI

Home page:

- White, lightweight "不背单词"-style header with centered `red book`.
- A count summary such as `811词`.
- An orange pill button `按导入时间`.
- Search remains available from the top icon.
- The eye icon toggles all list definitions: hidden by default, then word +
  phonetic + Chinese meanings when enabled.
- Alphabetical sort remains available through a compact top/right action.

Date-filter page:

- Title: `按导入时间筛选`.
- Header shows `已选 N 词` and a `全选` action.
- Rows use checkbox-style selection, date label, and word count.
- Bottom confirm button is disabled when no date is selected.

The page is a full-screen panel like the detail/study pages, with a back button
that returns without applying temporary changes.

## Behavior

`renderList()` applies filters in this order:

1. Selected import dates.
2. Search query.
3. Sort mode.

Default sort preserves `WORDS` order. Alphabetical sort changes only display
order and still opens details by the original `WORDS` index.

## Verification

- `WORDS.length` remains 811.
- No `WORDS` content changes.
- Import, search, and sort filters can combine.
- Status filter chips are absent from the list page.
- Date-filter rows correctly count old/imported/future-unfiled batches.
- Eye toggle shows and hides phonetics and Chinese meanings without changing the
  filtered word set.
- `startStudy()` still uses `getDueWords()` and is not affected by list sort.
