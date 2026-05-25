# Workflow: Lit `:key` Binding MVP

Date: 2026-05-25
Status: completed

## Goal

Add an experimental `:key=${...}` binding to this Lit fork so keyed `Array.map()` lists can preserve and move DOM nodes by item identity.

## Done When

- `html` templates can parse `:key=${expr}` without emitting a DOM attribute.
- Iterable child commits can detect keyed template results and reorder existing item parts by key.
- Existing unkeyed iterable behavior remains unchanged.
- Dev-mode tests cover reordering, insertion/removal, and duplicate keys.
- A focused test command has been run or a clear blocker is documented.

## Context

- Current Lit already has `keyed(key, value)` and `repeat(items, keyFn, renderFn)`.
- Desired DX is closer to React/Vue: `items.map((item) => html`<x-row :key=${item.id}></x-row>`)`.
- MVP assumes each keyed list item is represented by a template with one root keyed element. Multi-root/fragments can be designed later.

## Constraints And Risks

- `@key` is not used because `@` is already event listener syntax.
- `:key` is a runtime identity signal, not a DOM attribute.
- Key extraction must happen before item update so the iterable algorithm can choose the correct existing `ChildPart`.
- Compiled-template and SSR behavior may need a later follow-up; first batch targets uncompiled browser runtime and tests.

## Batch Plan

| Batch | Goal           | Work                                                                        | Verification                | Status    |
| ----- | -------------- | --------------------------------------------------------------------------- | --------------------------- | --------- |
| 1     | Runtime MVP    | Parse `:key`, store template key metadata, implement keyed iterable reorder | Focused `lit-html` tests    | completed |
| 2     | Hardening      | Add dev warnings/errors for invalid/duplicate keys and edge cases           | Extended tests/static check | completed |
| 3     | Docs/follow-up | Document experimental contract and remaining limitations                    | Review changed docs         | follow-up |

## Progress

### Batch 1

- Status: completed
- Done: API direction selected and implemented for uncompiled browser runtime:
  `:key=${expr}` is parsed as a non-DOM key binding and keyed iterable commits
  reuse/move existing child parts by key.
- Verification: `npm --workspace lit-html run build:ts`; Chromium dev web tests
  for `development/**/*_test.(js|html)` with 484 passed, 0 failed, 4 skipped.
- Remaining: compiled-template/SSR support and public docs can be handled as a
  follow-up if this API direction is accepted.
- Next: review API/runtime tradeoffs before broadening support beyond the MVP.

## Final Result

- Result: MVP implemented in `packages/lit-html/src/lit-html.ts` with tests in
  `packages/lit-html/src/test/lit-html_test.ts`.
- Verification: TypeScript build passed; lit-html dev browser suite passed on
  Chromium. Full local default multi-browser run was blocked until Playwright
  browsers were installed; only Chromium was installed for this batch.
- Remaining: compiled-template metadata, SSR behavior, docs/changelog, and
  performance benchmarking.
- Follow-up: decide whether `:key` should be accepted as public API before
  updating final documentation/changelog.
