# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

The **Beneficial Ownership Data Standard (BODS)** is a JSON Schema specification (using JSON Schema 2020-12) for modelling and publishing information on the beneficial ownership and control of corporate vehicles. Published at [standard.openownership.org](https://standard.openownership.org).

## Setup

```bash
git submodule init && git submodule update  # required for docson JS visualiser
python3 -m virtualenv -p python3.9 .ve      # match build server Python version
source .ve/bin/activate
pip install -r requirements_test.txt        # includes docs and test deps
```

## Commands

```bash
# Tests
pytest tests                                # run all tests
pytest tests/test_schema.py                 # schema validity tests only
pytest tests/test_data.py                   # data validation tests only
pytest -k "test_name"                       # run a single test by name

# Linting
flake8 --exclude=src --max-line-length=119

# Build docs (English)
sphinx-build docs/ _build
cd _build && python3 -m http.server         # serve at http://127.0.0.1:8000/

# Build docs (other language)
sphinx-build -D language=ru docs/ _build
```

## Architecture

### Schema files (`/schema/`)

Six JSON Schema files with URN-based `$id` values that form a registry:

| File | `$id` | Purpose |
|------|-------|---------|
| `statement.json` | `urn:statement` | Main entry point; validator base |
| `components.json` | `urn:components` | Shared component definitions |
| `person-record.json` | `urn:person` | Person statements |
| `entity-record.json` | `urn:entity` | Entity statements |
| `relationship-record.json` | `urn:relationship` | Relationship statements |
| `codelists.json` | `urn:codelists` | Codelist definitions |

All `$ref` pointers use URN IDs (e.g. `"$ref": "urn:components#/$defs/..."`) and are resolved via a `jsonschema` `Registry` built from all six files at once — see `tests/conftest.py:schema_registry()`.

Codelists live in `schema/codelists/` as CSV files and are validated against `schema/codelist-schema.json`.

### Test suite (`/tests/`)

- `conftest.py` — pytest fixtures: `schema_validator` (meta-schema validator), `bods_validator` (statement validator), `codelist_validator`, `schema_registry()`
- `test_schema.py` — schema structural validity (metadata presence, letter case, array items, null types via `jscc`)
- `test_data.py` — validates `/examples/` and `tests/data/` JSON files against the BODS schema; `tests/data/invalid-statements/expected_errors.csv` maps each invalid fixture to its expected error
- `test_docs.py` — documentation-level checks

### Key dependencies

- `jscc` — JSON Schema Compile Checker; provides `validate_metadata_presence`, `validate_array_items`, etc.
- `referencing` — builds the cross-file schema `Registry`
- `jsonschema` with `Draft202012Validator` — schema validation
- Sphinx + `data-standard-sphinx-theme` — documentation; theme lives in a separate repo

### Schema field requirements

Every user-defined field in the schema must have `title`, `description`, and `type` (or `$ref` / `oneOf`). `test_schema.py` enforces this via a patched `validate_metadata_presence` in `conftest.py`. Fields with `$ref` are exempt (they defer to the referenced definition).

### Adding/removing schema files

Update the `schemas` list in `tests/test_schema.py` (the `$id` values) whenever a JSON schema file is added to or removed from `/schema/`.

### Translations

Translations are managed via Transifex. See the [bods-dev-handbook](https://openownership.github.io/bods-dev-handbook/translations.html) for the full translation workflow (extract strings → push to Transifex → fetch → build).
