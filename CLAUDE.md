## SUT
https://restful-booker.herokuapp.com

## Stack
Python, pytest, httpx, Pydantic, ruff

## Commands
```bash
pip install -r requirements-dev.txt
pytest
ruff check .
```

## Structure
src/api_client/: httpx client, one method per endpoint
src/models/ contains models of requests and responses 
tests/  contains tests of API 
docs/ contains documentation of system, strategy, test cases, bugs

## Rules
- The tests do not include requests logic
- The tests generate their own data
- Mark known bugs with `xfail(strict=True)`
- Secrets are stored only in `.env`

## Definition of done
- Tests pass locally and in CI
- Ruff check is clean
- README is up to date
- Changes are made via a branch and a pull request with a description

## Phase 1
QA writes the code; the AI only explains and reviews it.
