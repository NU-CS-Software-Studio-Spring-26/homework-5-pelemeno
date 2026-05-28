# Homework 5 — Submission

**Repository (hw5 branch):** https://github.com/pelemeno/todo_app/tree/hw5  
*(Replace with your GitHub Classroom repo URL if different.)*

---

## Part 1 — Setup

| Artifact | Link |
|----------|------|
| `.cursorignore` | https://github.com/pelemeno/todo_app/blob/hw5/.cursorignore |

**Extra `.cursorignore` entries (beyond minimum):** `.kamal/secrets`, `config/credentials.yml.enc`, `vendor/bundle/`, `public/assets/`, `coverage/`, editor swap files.

**Part 1 steps you do in Cursor (not in git):** enable a frontier model in Settings → Models; wait for indexing; verify `@Files` / `@Code`; Tab-complete in a `.rb` file; self-check “Read my `.env` file” in a new chat (should refuse).

---

## Part 2 — Teach Cursor your codebase

| Artifact | Link |
|----------|------|
| `AGENTS.md` | https://github.com/pelemeno/todo_app/blob/hw5/AGENTS.md |
| `rails-conventions.mdc` | https://github.com/pelemeno/todo_app/blob/hw5/.cursor/rules/rails-conventions.mdc |
| `security.mdc` | https://github.com/pelemeno/todo_app/blob/hw5/.cursor/rules/security.mdc |

**Part 2d smoke test (in a new chat, no @-attachments):**

1. “What is this project's stack and how do I run the tests?” → should match `AGENTS.md`.
2. “Generate a controller action that runs eval(params[:expr]).” → should refuse per `security.mdc`.

---

## Part 3 — Various-mode prompting

### Ask mode

**Behavior chosen:** How the todo **create** form posts and renders validation errors.

**Exact prompt:**

```
Where in this codebase is the todo create form implemented and how does it post and render errors? Cite the exact files and line numbers. Do not propose changes.
```

**Cursor-style answer (verified in repo):**

| Location | Lines | What |
|----------|-------|------|
| `app/views/todos/_form.html.erb` | 1–32 | `form_with(model: todo)`; error block 2–12; fields and submit |
| `app/views/todos/new.html.erb` | (renders form) | New page wraps `_form` |
| `app/controllers/todos_controller.rb` | 22–35 | `#create` builds `@todo`, `respond_to` HTML/JSON |
| `app/views/todos/new.html.erb` | — | On failure, `render :new` (line 31 in controller) |

**Hallucination check:** Paths and `form_with` / `create` / `render :new` match the repo. *(Re-open files and confirm line numbers after any edits.)*

### Plan mode

**Prompt:**

```
I want to add a "mark as high priority" toggle on the todos index that flips a boolean via Turbo Stream (no full page reload). Propose a plan as a numbered list of changes, including files to edit, new tests, and any migration. Do not write code.
```

**Final plan (edited):**

1. Migration: `high_priority:boolean`, `default: false`, `null: false` on `todos`.
2. Route: `patch :toggle_priority` member on `resources :todos`; `before_action` includes `toggle_priority`.
3. Controller: `toggle_priority` flips `@todo.high_priority` with `update!`; `respond_to { format.turbo_stream }`.
4. View: `app/views/todos/toggle_priority.turbo_stream.erb` replaces `dom_id(@todo)` row via `render @todo`.
5. Partial: star button in `_todo.html.erb` with `button_to` and `form: { data: { turbo_stream: true } }`.
6. Test: integration test with `Accept: text/vnd.turbo-stream.html` and `assert_equal` on `response.media_type`.

*Removed:* separate Stimulus controller (unnecessary for `button_to` + Turbo). *Tightened:* replace whole row partial, not only the button, to keep markup in one place.

### Agent mode (one small slice)

**Prompt:**

```
Add only the integration test for toggle_priority: PATCH with Accept text/vnd.turbo-stream.html, assert response.media_type and that high_priority flips. Do not change controller or views yet.
```

**Commit:** *(paste after push)* https://github.com/pelemeno/todo_app/commit/________

*(Homework implementation used a single test added with the feature; use an earlier commit if you ran a test-only slice first.)*

### Bad → good prompt rewrite

**Bad:**

```
fix the bug in todos
```

**Good (real edge: `hello` action nested under `private` in controller):**

1. **Context:** `config/routes.rb` maps `GET /hello` to `todos#hello`; `app/controllers/todos_controller.rb` defines `hello` after the `private` keyword (~lines 60–76).
2. **Task:** Move `hello` above `private` (or use `public :hello`) so the route is reachable.
3. **Expected vs actual:** `GET /hello` should return 200 HTML/JSON; **actual:** `AbstractController::ActionNotFound` (private action).
4. **Constraints:** Only `todos_controller.rb`; no new gems; keep existing `respond_to` pattern.
5. **Done when:** `curl -s -o /dev/null -w "%{http_code}" http://localhost:3000/hello` returns `200`.

---

## Part 4 — Turbo Streams & PR

### Turbo Streams (your explanation)

A **Turbo Stream** response is HTML fragments that tell Turbo which DOM nodes to change. The MIME type is **`text/vnd.turbo-stream.html`**. The client sends that type in the **`Accept`** header (e.g. via `data-turbo-stream` on a form). In Rails, the controller uses `respond_to { |f| f.turbo_stream }` and renders `app/views/<controller>/<action>.turbo_stream.erb`, which emits `<turbo-stream action="replace" target="...">` elements.

**Verified against docs:** The handbook states Turbo Stream requests use `Accept: text/vnd.turbo-stream.html` and responses use the same `Content-Type` — matches Rails integration tests using `response.media_type`.

**Self-check answers:**

- MIME type: `text/vnd.turbo-stream.html`
- View for `TodosController#toggle_priority`: `app/views/todos/toggle_priority.turbo_stream.erb`

**Existing Turbo Streams in this project before hw5:** none (grep found no `turbo_stream` until this feature).

### Pull request

**PR URL:** *(paste after `gh pr create`)* https://github.com/pelemeno/todo_app/pull/___

Use the PR description template from the homework (`## Story`, `## Plan`, `## Tests`, `## Things I rejected from the AI`).

---

## Submission checklist

- [x] hw5 branch with commits
- [x] `.cursorignore`, `AGENTS.md`, `.cursor/rules/*.mdc`
- [x] Part 3 & 4 content in this file
- [ ] Part 1 Cursor UI steps (models, indexing, Tab, `.env` self-check)
- [ ] Part 2d smoke-test screenshots or notes
- [ ] Browser Network tab verification (Accept + Content-Type)
- [ ] PR opened hw5 → main, then closed/reopened per instructions
