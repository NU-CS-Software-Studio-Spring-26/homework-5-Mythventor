# AGENTS.md

## Stack
Rails 8.1.3 sample todo app, SQLite (sqlite3 2.9.4), Hotwire/Turbo (turbo-rails 2.0.23, 
stimulus-rails 1.3.4), importmap-rails 2.2.3, propshaft 1.3.2, jbuilder 2.15.0, 
Minitest 6.0.6 with Capybara and Selenium for system tests, 
background jobs via solid_queue 1.4.0.

## Commands
- Setup:  `bundle install && bin/rails db:create db:migrate`
- Run:    `bin/rails server`
- Test:   `bin/rails test`
- System tests: `bin/rails test:system`
- Lint:   `bundle exec rubocop`
- Console: `bin/rails console`

## Conventions
- Controllers respond with HTML and Turbo Streams; avoid JSON unless explicitly required
- Use `bin/rails generate` for boilerplate — never hand-write migrations or models
- Shared partials live in `app/views/shared/`
- Use strong parameters in every controller action
- Background jobs go through solid_queue via ActiveJob

## Don'ts
- No new gems without approval
- No inline JavaScript in ERB files
- No `skip_before_action :verify_authenticity_token`
- Do not seed real user data — use `db/seeds.rb` only
- Do not import models or migrations from other projects