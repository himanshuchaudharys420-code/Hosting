# HostX VIP White — Audit Report

## Fixed in this build

- Removed the shipped default administrator credentials; first administrator must be configured with `ADMIN_EMAIL` and a 12+ character `ADMIN_PASSWORD`.
- Removed legacy hard-coded email/username privilege grants during database migration unless the account matches the explicitly configured `ADMIN_EMAIL`.
- Disabled the old `adminrifat` / `123456789` administrator account on upgrade only if it still uses the shipped password.
- Removed plaintext-password authentication.
- Stopped storing the sign-in password in browser local storage; “Remember Me” now remembers only the email and controls the session lifetime.
- Replaced the insecure email-only password reset with a 6-digit email OTP flow (SMTP settings required).
- Added production `SECRET_KEY` enforcement, secure-cookie configuration, and a 14-day remembered session.
- Fixed the duplicate `/health` route and maintenance-mode handling for `/api/health`.
- Fixed Super Admin ownership checks and made empty admin permission lists deny privileged actions.
- Prevented untrusted Host headers from becoming self-ping targets.
- Added bounded ZIP extraction with traversal, symlink, file-count, and expanded-size checks.
- Changed dependency scanning so it inspects import specs without executing uploaded Python modules.
- Sanitized child-process environments so panel secrets are not deliberately copied into hosted scripts or pip subprocesses.
- Fixed request parsing for JSON/form endpoints, upload destination validation, empty file names, and file-save/rename edge cases.
- Added the missing default logo, Render configuration, Procfile, Hindi setup guide, and runtime-artifact `.gitignore`.
- Removed incomplete React starter files that pointed to a missing `src/` directory.

## Tests completed in this environment

- Python `app.py` syntax compilation: PASS
- Jinja parsing for all 24 HTML templates: PASS
- Flask route collision check (66 routes): PASS, no duplicate path/method pairs
- Template `url_for` endpoint check: PASS, no missing endpoints
- `render_template` file check: PASS, no missing templates
- SQLite schema and migrations: PASS
- ZIP helper tests: PASS for normal extraction; traversal and symlink archives rejected
- Dependency scanner tests: PASS; uploaded module code was not executed
- Password/auth and admin bootstrap helper tests: PASS
- Child-process environment check: PASS; `SECRET_KEY` and `SMTP_PASSWORD` are not passed in the constructed child environment
- Inline JavaScript syntax checks: PASS for all scripts; Jinja-interpolated scripts were checked after replacing template expressions with test literals
- `render.yaml` YAML parsing: PASS

## Tests not completed

The sandbox does not have Flask/Werkzeug installed and outbound package installation is unavailable. Therefore a live Flask test-client run, SMTP delivery test, Gunicorn boot test, and actual hosting-provider deployment test could not be run here. Install `requirements.txt` and perform those checks on the deployment host before opening the service.

## Important architecture warning

This app launches uploaded Python programs and pip package installers as subprocesses under the same operating-system account as the Flask panel. Removing inherited environment secrets reduces exposure, but it is **not an OS sandbox**. A user-controlled script may still access files/processes available to that OS account. Do not offer this build as public multi-tenant hosting for untrusted users until each hosted server is isolated in its own container or restricted OS user with filesystem, network, CPU, memory, and process limits.

## Required deployment settings

Set `SECRET_KEY`, `ADMIN_EMAIL`, `ADMIN_PASSWORD`, and `APP_URL`. For Forgot Password, also configure `SMTP_HOST`, `SMTP_PORT`, `SMTP_USER`, `SMTP_PASSWORD`, and `SMTP_FROM`. Attach persistent storage at `/data` if the hosting plan supports it; otherwise SQLite and user files may be lost when the instance is replaced.
