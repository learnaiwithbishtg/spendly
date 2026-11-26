# CLAUDE.md

This file provides guidance to Claude Code when working in this repository.

# Project Overview

Spendly is a lightweight personal expense tracker built with Flask and SQLite.

# Project Structure

```text
Spendly/
├── app.py
├── CLAUDE.md
├── requirements.txt
├── expense_tracker.db
├── database/
│   └── db.py
├── templates/
│   ├── base.html
│   ├── landing.html
│   ├── register.html
│   ├── login.html
│   ├── terms.html
│   └── privacy.html
├── static/
│   ├── css/
│   │   └── style.css
│   └── js/
│       └── main.js
└── tests/
```

# Commands

## Windows Setup

The virtual environment is Windows-specific and git-ignored.

```bash
venv\Scripts\activate
pip install -r requirements.txt
```

## Run the Application

```bash
python app.py
```

The Flask development server runs on port `5001`.

## Tests

`pytest` and `pytest-flask` are included in `requirements.txt`. No test files currently exist.

```bash
pytest                          # Run all tests
pytest tests/test_x.py          # Run a specific test file
pytest tests/test_x.py::test_name  # Run a specific test
```

There is currently no linting, formatting, or build configuration.

# Architecture

Spendly is a server-rendered Flask application using SQLite, Jinja2 templates, HTML, CSS, and vanilla JavaScript. The application currently uses a single Flask module without blueprints.

## app.py

- Contains the Flask application instance and all routes.
- Implemented routes currently render templates: `/`, `/register`, `/login`, `/terms`, and `/privacy`.
- Placeholder routes exist for `/logout`, `/profile`, `/expenses/add`, `/expenses/<id>/edit`, and `/expenses/<id>/delete`.
- Replace placeholder implementations in place, preserving their URLs and endpoint function names.

## database/db.py

The database module is currently unimplemented. It is intended to provide:

- `get_db()` — SQLite connection with `row_factory` and foreign keys enabled.
- `init_db()` — Database schema initialization using `CREATE TABLE IF NOT EXISTS`.
- `seed_db()` — Sample data initialization.

The database file is `expense_tracker.db` and is git-ignored. The database module is not currently used by the application.

## Templates

- All page templates extend `templates/base.html`.
- `base.html` provides shared navigation, footer, Google Fonts, CSS, and JavaScript.
- Page content is defined in the `content` block.
- Page-specific JavaScript is defined in the `scripts` block.
- Page titles are defined in the `title` block.
- Use `url_for()` for navigation and Flask endpoint references.

## Static Assets

- `static/css/style.css` — Shared stylesheet.
- `static/js/main.js` — Shared JavaScript file, currently empty.
- The application uses vanilla JavaScript without frontend frameworks or libraries.

# Implemented vs. Stub Routes

| Route | Status |
|---|---|
| `GET /` | Implemented — renders `landing.html` |
| `GET /register` | Implemented — renders `register.html` |
| `GET /login` | Implemented — renders `login.html` |
| `GET /terms` | Implemented — renders `terms.html` |
| `GET /privacy` | Implemented — renders `privacy.html` |
| `GET /logout` | Stub — Step 3 |
| `GET /profile` | Stub — Step 4 |
| `GET /expenses/add` | Stub — Step 7 |
| `GET /expenses/<id>/edit` | Stub — Step 8 |
| `GET /expenses/<id>/delete` | Stub — Step 9 |

# Conventions

- Preserve the existing `# ---` section banners in `app.py`.
- Replace stub routes in place, preserving their URL and function names.
- Follow the existing project structure and coding patterns.
- Avoid unnecessary refactoring or changes to unrelated files.