# Homework 5 — Submission

Repository (hw5 branch): https://github.com/NU-CS-Software-Studio-Spring-26/homework-5-pelemeno/tree/hw5


## Part 1, set up

.cursorignore: https://github.com/NU-CS-Software-Studio-Spring-26/homework-5-pelemeno/blob/hw5/.cursorignore

## Part 2, Teach Cursor your codebase

`AGENTS.md:` https://github.com/NU-CS-Software-Studio-Spring-26/homework-5-pelemeno/blob/hw5/AGENTS.md
`rails-conventions.mdc:` https://github.com/NU-CS-Software-Studio-Spring-26/homework-5-pelemeno/blob/hw5/.cursor/rules/rails-conventions.mdc
`security.mdc:` https://github.com/NU-CS-Software-Studio-Spring-26/homework-5-pelemeno/blob/hw5/.cursor/rules/security.mdc

## Part 3, Various-mode prompting

### Ask mode

Behavior chosen:
How the todo create form posts and renders validation errors.

Prompt:
Where in this codebase is the todo create form implemented and how does it post and render errors? Cite the exact files and line numbers. Do not propose changes.

Cursor answer:
| Location | Lines |
|----------|-------|
| `app/views/todos/_form.html.erb` | 1–22 |
| `app/views/todos/new.html.erb` | 5 |
| `app/views/todos/index.html.erb` | 16 |
| `config/routes.rb` | 2–6 |
| `app/controllers/todos_controller.rb` | 13–16 |
| `app/controllers/todos_controller.rb` | 22–35 |
| `app/controllers/todos_controller.rb` | 83–85 |

Cursor did not hallucinate the paths.

### Plan mode

Prompt:
I want to add a "mark as high priority" toggle on the todos index that flips a boolean via Turbo Stream (no full page reload). Propose a plan as a numbered list of changes, including files to edit, new tests, and any migration. Do not write code.


Plan (edited):
1. Migration: `high_priority:boolean`, `default: false`, `null: false` on `todos`.
2. Route: `patch :toggle_priority` member on `resources :todos`; `before_action` includes `toggle_priority`.
3. Controller: `toggle_priority` flips `@todo.high_priority` with `update!`; `respond_to { format.turbo_stream }`.
4. View: `app/views/todos/toggle_priority.turbo_stream.erb` replaces `dom_id(@todo)` row via `render @todo`.
5. Partial: star button in `_todo.html.erb` with `button_to` and `form: { data: { turbo_stream: true } }`.
6. Test: integration test with `Accept: text/vnd.turbo-stream.html` and `assert_equal` on `response.media_type`.

Removed separate stimulus controller (unnecessary for `button_to` + Turbo) and replaced whole row partial, not only the button, to keep markup in one place.

### Agent mode

Prompt:
Add only the integration test for toggle_priority: PATCH with Accept text/vnd.turbo-stream.html, assert response.media_type and that high_priority flips. Do not change controller or views yet.

Commit: https://github.com/NU-CS-Software-Studio-Spring-26/homework-5-pelemeno/commit/9529ecb

Bad:
fix the bug in todos

Good (`hello` action nested under `private` in controller):
1. Context: `config/routes.rb` maps `GET /hello` to `todos#hello`; `app/controllers/todos_controller.rb` defines `hello` after the `private` keyword (~lines 60–76).
2. Task: Move `hello` above `private` (or use `public :hello`) so the route is reachable.
3. Expected vs actual: `GET /hello` should return 200 HTML/JSON; actual: `AbstractController::ActionNotFound` (private action).
4. Constraints: Only `todos_controller.rb`; no new gems; keep existing `respond_to` pattern.
5. Done when: `curl -s -o /dev/null -w "%{http_code}" http://localhost:3000/hello` returns `200`.

## Part 4, Ship a small feature end-to-end

A Turbo Stream response is HTML fragments that tell Turbo which DOM nodes to change. The MIME type is `text/vnd.turbo-stream.html`. The client sends that type in the `Accept` header (e.g. via `data-turbo-stream` on a form). In Rails, the controller uses `respond_to { |f| f.turbo_stream }` and renders `app/views/<controller>/<action>.turbo_stream.erb`, which emits `<turbo-stream action="replace" target="...">` elements.

Verified against docs: The handbook states Turbo Stream requests use `Accept: text/vnd.turbo-stream.html` and responses use the same `Content-Type`. This matches Rails integration tests using `response.media_type`.

Existing Turbo Streams in this project before hw5: none (grep found no `turbo_stream` until this feature).

### Pull request

PR URL: https://github.com/NU-CS-Software-Studio-Spring-26/homework-5-pelemeno/pull/1
