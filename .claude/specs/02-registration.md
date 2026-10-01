# Spec: Registration

## Overview
Make the existing `/register` page actually create accounts. Today the route
only renders `register.html` on GET, and the form's POST goes nowhere. This step
adds POST handling: validate the input, hash the password, insert a row into
`users`, and send the new user to the sign-in page with a success message.

## Depends on
- Step 1 — Database setup (`users` table, `get_db()`, `init_db()`, `seed_db()`)

## Routes

| Method | URL | What it does | Login required |
| --- | --- | --- | --- |
| GET | `/register` | Renders the empty registration form (unchanged behaviour) | No |
| POST | `/register` | Validates the form, creates the user, redirects to `/login` with a success flash. On a validation error, re-renders `register.html` with `error` and the previously entered `name` / `email` | No |

`/login` stays GET-only in this step — actual sign-in is the next step. It only
needs to show the flashed success message.

### POST `/register` validation (in this order)
1. Strip whitespace from `name` and `email`; lowercase `email`.
2. All three fields are required → `"All fields are required."`
3. `email` must contain `@` and a `.` after it → `"Please enter a valid email address."`
4. `password` must be at least 8 characters → `"Password must be at least 8 characters."`
5. Email must not already exist → `"An account with this email already exists."`

On success: `flash("Account created — please sign in.")` then
`redirect(url_for("login"))` (Post/Redirect/Get, so a refresh does not resubmit).

## Database changes
None. The `users` table from Step 1 already has `name`, `email UNIQUE`,
`password_hash` and `created_at`.

Add two helpers to `database/db.py`:
- `get_user_by_email(email)` → returns the `sqlite3.Row` or `None`
- `create_user(name, email, password)` → hashes the password with
  `generate_password_hash`, inserts the row, commits, returns the new `id`.
  It must close its connection. If the insert raises `sqlite3.IntegrityError`
  (a race on the UNIQUE email), the route treats it as the duplicate-email error.

## Templates

**Modify `templates/register.html`**
- Keep the existing layout, classes and `{% if error %}` block.
- Add `value="{{ name or '' }}"` to the name input and
  `value="{{ email or '' }}"` to the email input so input survives a failed
  submit. Never re-populate the password.
- Add `minlength="8"` to the password input.
- Change `action="/register"` to `action="{{ url_for('register') }}"`.

**Modify `templates/login.html`**
- Above the `{% if error %}` block, render flashed messages:
  `{% with messages = get_flashed_messages() %}` … one
  `<div class="auth-success">` per message.

No new templates.

## Files to change
- `app.py`
  - Import `request`, `redirect`, `url_for`, `flash` from flask
  - Import `create_user`, `get_user_by_email` from `database.db`
  - Set `app.secret_key` (needed for `flash`), read from the
    `SECRET_KEY` environment variable with a dev-only fallback string
  - Change `/register` to `methods=["GET", "POST"]` and implement the logic above
- `database/db.py` — add `get_user_by_email()` and `create_user()`
- `templates/register.html` — see Templates
- `templates/login.html` — see Templates
- `static/css/style.css` — add `.auth-success` next to `.auth-error`, styled
  the same way but using `var(--accent)` / `var(--accent-light)`

## Rules for implementation
- No SQLAlchemy or ORMs
- Parameterised queries only (`?` placeholders, never f-strings or `%` in SQL)
- Passwords hashed with werkzeug (`generate_password_hash`); never store or log the plain password
- Use CSS variables - never hardcode hex values
- All templates extend base.html
- Keep all SQL inside `database/db.py`; `app.py` only calls the helpers
- Every connection opened in a helper is closed before returning
- Do not log the user in or create a session yet — that belongs to the login step
- Do not add new pip packages

## Definition of done
- [ ] `GET /register` shows the form exactly as before
- [ ] Submitting a valid new name / email / 8+ char password redirects to `/login`
- [ ] `/login` shows "Account created — please sign in." after that redirect, and the message is gone on refresh
- [ ] A new row exists in `users`; its `password_hash` is a werkzeug hash, not the plain password
- [ ] The email is stored lowercased and trimmed (e.g. ` New@Example.com ` → `new@example.com`)
- [ ] Registering with `demo@spendly.com` shows "An account with this email already exists." and no row is added
- [ ] A password shorter than 8 characters shows the length error (test with the browser's `minlength` removed via dev tools)
- [ ] After any error, the name and email fields keep what was typed; the password field is empty
- [ ] Error and success boxes use CSS variables only — no hex values added to `style.css`
- [ ] App starts with `python app.py` without errors, and the seed data is untouched
