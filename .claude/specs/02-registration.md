# Spec: Registration

## Overview
Add working account registration to Spendly. The `/register` page already renders a form, but submitting it does nothing. This step makes the form create a real user in the `users` table with a hashed password, rejects duplicate emails, and validates the input. It is the first step that writes user data, and it is needed before login (Step 3) and profile (Step 4) can work, since both depend on a user record existing.

## Depends on
- Step 1 (Database setup): `users` table, `get_db()`, and `init_db()` must be in place.

## Routes
- `GET /register` — render the registration form (already implemented, keep as-is) — public
- `POST /register` — validate the submitted form, create the user, and redirect to `/login` on success; re-render the form with an error on failure — public

The existing `register()` function in `app.py` gains `methods=["GET", "POST"]`. Keep the URL and function name unchanged.

## Database changes
No database changes. The `users` table already defined in `database/db.py` (`id`, `name`, `email` UNIQUE, `password_hash`, `created_at`) covers this feature.

## Templates
- **Create:** none
- **Modify:**
  - `templates/register.html` — the form already posts to `/register` and shows `{{ error }}` when set. Keep the field names (`name`, `email`, `password`). Re-fill `name` and `email` with the submitted values on error so the user does not have to retype them.

## Files to change
- `app.py` — allow `POST` on the `register()` route; read form fields, validate, call the database layer, and handle errors.
- `database/db.py` — add a helper such as `create_user(name, email, password)` that hashes the password and inserts the row using `get_db()`. It should raise a clear error on duplicate email so `app.py` can show a friendly message.
- `templates/register.html` — preserve submitted values on error (see above).

## Files to create
None.

## New dependencies
No new dependencies. `werkzeug.security.generate_password_hash` is already available through Flask/Werkzeug.

## Rules for implementation
- No SQLAlchemy or ORMs
- Parameterised queries only — no f-strings or `%` formatting in SQL
- Passwords hashed with `werkzeug.security.generate_password_hash`; never store or log plaintext passwords
- Validation rules:
  - `name`, `email`, and `password` are all required; strip whitespace from `name` and `email`
  - `email` must contain `@` and a domain part (basic check is enough)
  - `password` must be at least 8 characters
  - Duplicate email → show "An account with this email already exists." (catch the UNIQUE constraint violation; do not rely on a prior SELECT alone)
- Re-render `register.html` with `error` set on validation failure; do not expose raw exception text to the user
- Use CSS variables — never hardcode hex values
- All templates extend `base.html`
- Use `url_for()` for links and redirects (for example `url_for('login')`)
- Preserve the `# ---` section banners in `app.py`
- Do not add session or login logic in this step; that belongs to Step 3

## Definition of done
- [ ] `GET /register` renders the form with no errors
- [ ] Submitting valid name, email, and an 8+ character password redirects to `/login`
- [ ] A new row appears in `users` with the correct `name` and `email`
- [ ] The stored `password_hash` is not equal to the plaintext password and passes `check_password_hash`
- [ ] Submitting an email that already exists shows "An account with this email already exists." and creates no new row
- [ ] Submitting with a missing name, missing email, or missing password shows an error and creates no row
- [ ] Submitting a password shorter than 8 characters shows an error and creates no row
- [ ] After a validation error, the name and email fields keep their submitted values
- [ ] Emails differing only by case are treated as duplicates, or the behaviour is documented in the spec (decision: store lowercased email and check against lowercased input)
- [ ] The app starts with `python app.py` on port 5001 without errors
- [ ] No hardcoded hex colours are added to templates or CSS
