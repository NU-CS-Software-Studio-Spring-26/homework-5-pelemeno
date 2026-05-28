# Agent brief — sample todo app

## Stack

Rails 8.1 sample todo app. SQLite in development and test (`storage/*.sqlite3` per `config/database.yml`); PostgreSQL in production via the `pg` gem. Hotwire: Turbo Rails + Stimulus with importmap (no Node bundler). Propshaft for assets. Minitest under `test/` (not RSpec). Background work uses Solid Queue (`solid_queue`, `bin/jobs`).

## Commands

- Setup: `bin/setup`
- Run app: `bin/dev` (Puma via `bin/rails server`)
- Tests: `bin/rails test`
- Lint: `bin/rubocop`

## Conventions

- Models live in `app/models/`; scaffold-style REST in `TodosController` with `respond_to` for HTML and JSON.
- No authentication layer in this app; all todos are visible to everyone.
- Shared todo markup: partial `app/views/todos/_todo.html.erb`, rendered from index/show; JSON via `*.jbuilder` where present.
- Strong parameters use `params.expect(todo: [...])` in `TodosController#todo_params`.
- Prefer Hotwire (Turbo Drive / Turbo Streams) over custom JavaScript in ERB.

## Don'ts

- Do not add gems without explicit approval.
- Do not use `skip_before_action :verify_authenticity_token` or disable CSRF.
- Do not put inline JavaScript in ERB; use Stimulus controllers under `app/javascript/controllers/` if JS is needed.
- Do not seed real user data; use `db/seeds.rb` only
- Do not hand-edit `db/schema.rb`; use reversible migrations.
- Keep changes scoped to this todo app (no imports from other projects).
