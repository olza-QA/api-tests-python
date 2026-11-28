# Test Strategy: Restful-booker API

## 1. Purpose and scope
Verify that the Restful-booker REST API behaves as documented: authentication,
booking CRUD, input validation and response contracts.
**In scope:** `/ping`, `/auth`, `/booking` (GET list, GET by id, POST, PUT, PATCH, DELETE).
**Out of scope:** UI, performance, security testing.

## 2. SUT and sources of truth
Public API: https://restful-booker.herokuapp.com. The only specification is the
apidoc (no OpenAPI file), so tests are designed from API documentation.
Where the apidoc is silent, behavior is checked against common HTTP semantics
and recorded as an assumption in `docs/test-cases.md`.

## 3. Risks and constraints
- Shared public instance: data resets periodically, availability is not guaranteed.
- No machine-readable spec: no spec-driven generation, contract is described manually.
- Undocumented and inconsistent error handling: malformed input can return 500 or be accepted
  without validation; findings are tracked in `docs/bugs/`.

## 4. Test types and priorities
| Priority | Type | Why | How |
|---|---|---|---|
| 1 | Smoke | Is the service alive | `/ping`, `/auth`, create + read a booking |
| 2 | Auth | Write access depends on it | token via Cookie and Basic Auth, missing/invalid token |
| 3 | CRUD | Core business flow | create → read back → PUT/PATCH → read back → DELETE → read back |
| 4 | Negative / boundary | Validation and error handling | `parametrize` over invalid inputs |
| 5 | Schema / contract | Response shape does not drift | Pydantic models, two shapes of booking response |

## 5. Test data
Every test creates its own booking and deletes it afterwards (fixtures).
Seed bookings (e.g. id 1) are never used: they belong to the shared instance.
Unique values in names to find and clean up own data.

## 6. Environment and configuration
Base URL and credentials come from environment variables (`.env`, not committed).
`.env.example` documents the required keys. Same config locally and in CI.

## 7. Tooling
Python, pytest, httpx, Pydantic, ruff, GitHub Actions.

## 8. Handling defects
- API behavior does not match the documentation: bug report in `docs/bugs/`;
  the test asserts the documented behavior, `xfail(strict=True)`.
- Undocumented system behavior that contradicts general standards (e.g., 500 on client error):
  bug report with the assumption stated; the test asserts the expected 4xx, `xfail(strict=True)`.
- Undocumented and ambiguous (e.g. 200 + empty list for no matches):
  test asserts the observed behavior, noted in `docs/test-cases.md`.

## 9. Entry / exit criteria
**Entry:** SUT reachable (`/ping`), credentials configured.
**Exit:** smoke, auth and CRUD tests green in CI; every known deviation has a
bug report and an `xfail(strict=True)` test.

## 10. Future / out of scope
Load testing, security checks.