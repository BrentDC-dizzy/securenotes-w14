# Combined Lab — Checklist

## Part A — make the security real (`app/main.py`)
- [x] **TASK 1 (A04)** hash_password / verify_password use bcrypt
- [x] **TASK 2 (A07)** create_token / read_token use a signed, expiring JWT
- [x] **TASK 3 (A07)** get_current_user raises 401 on a bad/expired token or unknown user (fail closed)
- [x] **TASK 4 (A01)** get_note returns 403 when the note isn't the caller's
- [x] **TASK 5 (A01)** /admin/users requires the admin role and never returns the password column

## Part B — write the tests (`tests/`)
API (`test_api.py`)
- [ ] register then login returns a JWT (three dotted parts)
- [ ] wrong password returns 401
- [ ] create a note, then see it in your list

Security (`test_security.py`)
- [ ] bad token rejected (401)
- [ ] a user can't read another's note (403)  ← A01
- [ ] a user can read their own note (200)
- [ ] same login error for bad user vs bad password  ← A07
- [ ] a non-admin can't list users (403)  ← A01
- [ ] admin list has no password field  ← A04

**Goal:** all 5 tasks done AND all 11 tests green (`pytest -q`). Then `pytest --cov=app`.

## Reflection (4–5 sentences)
Pick one security test. Explain what it checks, which OWASP risk it guards, and what would break in the
app if a teammate deleted that one check.

The security test `test_user_cannot_read_another_users_note` directly guards against OWASP A01: Broken Access Control (specifically Insecure Direct Object Reference or BOLA). It verifies that an authenticated user (Alice) cannot access private notes belonging to another user (Bob) simply by modifying the note ID in the path `GET /notes/2`. If a teammate accidentally deleted the ownership validation `if row["owner_id"] != user["id"]:` from `app/main.py`, this test would immediately fail in the test suite and block the regression. Without this test locking in the security policy, any logged-in user could silently access and exfiltrate private notes belonging to everyone else in the system.
