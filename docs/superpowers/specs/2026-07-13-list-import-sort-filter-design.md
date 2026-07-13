# List Import And Sort Filter Design

## Goal

Add list-page filters under the existing "开始学习" area in `index.html` so the
word list can be filtered by import batch and ordered alphabetically.

## Scope

- Modify only the list page UI and list rendering logic in `index.html`.
- Do not change the `WORDS` data, detail page, SRS storage, or study-mode queue.
- The new filters affect only the home list display.

## Import Batch Rules

The app does not store per-card timestamps, so batch membership is derived from
the stable append ranges in `WORDS`:

- `0-584`: original/old cards.
- `585-698`: 2026-07-11 import.
- `699-810`: 2026-07-13 import.

These ranges match the existing import history and preserve SRS index behavior.

## UI

Keep the current status chips: 全部, 新词, 学习中, 待复习, 已掌握, 搜索.

Add one compact secondary filter row below it:

- 导入: 全部批次, 旧词, 7月11, 7月13.
- 排序: 默认, A-Z, Z-A.

The row scrolls horizontally like the existing chip row to fit mobile screens.

## Behavior

`renderList()` applies filters in this order:

1. Status filter.
2. Import batch filter.
3. Search query.
4. Sort mode.

Default sort preserves `WORDS` order. Alphabetical sort changes only display
order and still opens details by the original `WORDS` index.

## Verification

- `WORDS.length` remains 811.
- No `WORDS` content changes.
- Status, import, search, and sort filters can combine.
- `startStudy()` still uses `getDueWords()` and is not affected by list sort.
