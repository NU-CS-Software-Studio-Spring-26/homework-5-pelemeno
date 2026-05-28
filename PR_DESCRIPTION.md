## Story

As a user viewing my todo list, I want to mark individual todos as high priority with one click, so that important items stand out without reloading the page.

**Acceptance criteria**

- `Todo` has a `high_priority` boolean attribute.
- On the todos index, every row shows a visible toggle (★ / ☆) for the current priority.
- Clicking the toggle flips priority and returns a Turbo Stream that updates only that row; the rest of the page does not re-render.
- In DevTools → Network: request `Accept` includes `text/vnd.turbo-stream.html`; response `Content-Type` is `text/vnd.turbo-stream.html`; no full document navigation.
- At least one automated test proves the Turbo Stream response (not only HTML).

## Plan

1. Migration: add `high_priority:boolean` to `todos` with `default: false`, `null: false`.
2. Route + controller: member `patch :toggle_priority`; flip boolean; `respond_to { format.turbo_stream }`.
3. Views: `toggle_priority.turbo_stream.erb` replaces `dom_id(@todo)`; star `button_to` in `_todo.html.erb` with `data-turbo-stream`.
4. Test: integration test with `Accept: text/vnd.turbo-stream.html` and `assert_equal` on `response.media_type`.

## Tests

```bash
bin/rails test
```

```
Running 8 tests in a single process (parallelization threshold is 50)
Run options: --seed 61722

# Running:

........

Finished in 0.214562s, 37.2853 runs/s, 60.5886 assertions/s.
8 runs, 13 assertions, 0 failures, 0 errors, 0 skips
```

## Things I rejected from the AI

- A separate Stimulus controller for the toggle — `button_to` with `data: { turbo_stream: true }` is enough.
- Replacing only the button DOM id instead of the whole `_todo` partial — kept one partial for consistent row markup.
