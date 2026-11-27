# Spec: Login and Logout

## Overview
Make sign-in and sign-out work for Spendly. The `/login` page already renders a form that posts to `/login`, but the route only accepts `GET`, so submitting does nothing. This step adds `POST /login`, which looks up the user by email, verifies the password against the hash created during registration, and stores the user's identity in a Flask session. It also replaces the `/logout` stub so it clears that session and returns the visitor to the landing page. This is the first step that tracks who is signed in, and it is required before profile (Step 4) and expense pages (Steps 7–9) can know which user's data to show.

## Depends on
- Step 1 (Database setup): `users` table, `get_db()`, and `init_db()` must be in place.
- Step 2 (Registration): users must exist with `password_hash` values created by `generate_password_hash`, so there is something to log in with.

## Routes
- `GET /login` — render the sign-in form (already implemented; keep as-is) — public
- `POST /login` — validate the submitted email and password, start a session on success and redirect to `/profile`; re-render the form with a generic error on failure — public
- `GET /logout` — clear the session and redirect to `/` — public (safe to call when not signed in)

The existing `login()` function in `app.py` gains `methods=["GET", "POST"]`. The existing `logout()` function replaces its placeholder body in place. Keep both URLs and function names unchanged.

## Database changes
No database changes. The existing `users` table provides `id`, `name`, `email`, and `password_hash`, which is all this feature needs.

## Templates
- **Create:** none
- **Modify:**
  - `templates/login.html` — render `{{ error }}` (already present) and re-fill the `email` input with the submitted value on error using `value="{{ email }}"`. Do not re-fill the password. Keep the field names `email` and `password`.

## Files to change
- `app.py` —
  - set `app.secret_key` (required for `session`), read from the `SECRET_KEY` environment variable with a development fallback;
  - allow `POST` on the `login()` route; read and normalise form fields, look up the user, verify the password, set session values, and redirect;
  - replace the body of the `logout()` placeholder with session clearing and a redirect to `/`.
- `database/db.py` — add `get_user_by_email(email)` that returns the matching `sqlite3.Row` or `None`, using `get_db()` and a parameterised query.
- `templates/login.html` — preserve the submitted email on error (see above).

## Files to create
None.

## New dependencies
No new dependencies. `flask.session` is part of Flask, and `werkzeug.security.check_password_hash` is already available through Werkzeug.

## Rules for implementation
- No SQLAlchemy or ORMs
- Parameterised queries only — no f-strings or `%` formatting in SQL
- Verify passwords with `werkzeug.security.check_password_hash`; never store or log plaintext passwords
- Strip whitespace from `email` and lowercase it before lookup, matching the registration behaviour
- Use one generic error for both an unknown email and a wrong password: "Invalid email or password." Do not reveal which one failed
- If `email` or `password` is missing or empty, show "Email and password are required." and do not query the database
- Re-render `login.html` with `error` set on failure, with status 401 — do not expose raw exception text
- Store only `user_id` and `name` in `session`; never store the password or the hash
- `SECRET_KEY` must come from the environment; the fallback value is for local development only and must not be used in production
- `/logout` must clear the session (`session.clear()`) and redirect with `url_for('landing')`
- Use CSS variables — never hardcode hex values
- All templates extend `base.html`
- Use `url_for()` for links and redirects
- Preserve the `# ---` section banners in `app.py`
- Do not add registration or profile logic in this step

## Definition of done
- [ ] `GET /login` renders the sign-in form with no errors
- [ ] Signing in with `demo@spendly.com` / `demo123` redirects to `/profile`
- [ ] After sign-in, the session cookie is set and `session["user_id"]` matches the demo user's `id` in `users`
- [ ] Signing in with a wrong password shows "Invalid email or password." with the email field still filled and the password field empty
- [ ] Signing in with an unregistered email shows the same "Invalid email or password." message
- [ ] Signing in with an empty email or password shows "Email and password are required." and does not change the session
- [ ] Email is matched case-insensitively and ignores surrounding whitespace (e.g. ` DEMO@spendly.com `)
- [ ] The stored `password_hash` is never shown in the page, the session, or the server log
- [ ] `GET /logout` clears the session and redirects to `/`
- [ ] After logout, revisiting a session-only page does not show the signed-in user (verified once Step 4 exists; for now, inspect the cookie in browser devtools)
- [ ] The app starts with `python app.py` on port 5001 without errors
- [ ] No hardcoded hex colours are added to templates or CSS
