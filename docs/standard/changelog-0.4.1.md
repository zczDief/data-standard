# Changelog — [0.4.1] — 2026-06-09

## Added

- **`judicialProcess` record type** — a new `recordType` value and corresponding `urn:judicialProcess` schema (`schema/judicial-process-record.json`) for representing Brazilian MNI/CNJ judicial processes. The schema captures:
  - `numero` — 20-digit CNJ process number (unmasked, `\d{20}`)
  - `classeProcessual` — TPU procedural class code
  - `nivelSigilo` — confidentiality level (0–5)
  - `orgaoJulgador` — adjudicating body with `instancia` from the `judicialInstance` codelist
  - Optional: `codigoLocalidade`, `intervencaoMP`, `dataAjuizamento`, `valorCausa`, `assuntos`, `movimentos`, `outrosNumeros`, `vinculacoes`, `outrosParametros`

- **`judicialParty` and `judicialRepresentative`** added to the `interest.type` codelist (`schema/codelists/interestType.csv`), enabling Relationship statements to express procedural party and legal representative roles in a judicial process.

- **`judicialInterestDetails`** property added to the Interest object in `schema/relationship-record.json`, carrying a `JudicialInterestDetails` sub-object with:
  - `polo` — procedural pole (`AT`, `PA`, `TC`, `FL`) from the `judicialPole` codelist (open)
  - `interessePublico` — public interest flag (boolean)
  - `assistenciaJudiciaria` — legal aid flag (boolean)

- **Three new codelists** in `schema/codelists/`:

  | File | Codes | Purpose |
  |------|-------|---------|
  | `judicialInstance.csv` | `ORIG`, `REV`, `ESP`, `EXT`, `ADM` | Judicial instance of the adjudicating body |
  | `judicialPole.csv` | `AT`, `PA`, `TC`, `FL` | Procedural pole of a party (open codelist) |
  | `judicialProcessBinding.csv` | `AR`, `CD`, `RR`, `RG` | Type of formal link between two processes |

- **New documentation page**: [Representing judicial processes](modelling/repr-judicial-process.md)

- **New example**: [Judicial process parties (MNI)](../examples/judicial-process-parties.md) — seven-statement package showing plaintiff, defendant, and lawyer linked to a first-instance civil process in São Paulo.

## Schema files affected

| File | Change |
|------|--------|
| `schema/judicial-process-record.json` | **Created** — `$id: urn:judicialProcess` |
| `schema/statement.json` | `recordType` enum extended; new `allOf` if/then routing block for `judicialProcess` |
| `schema/relationship-record.json` | `Interest.type` enum extended; `judicialInterestDetails` property and `JudicialInterestDetails` `$def` added |
| `schema/codelists/recordType.csv` | `judicialProcess` row added |
| `schema/codelists/interestType.csv` | `judicialParty` and `judicialRepresentative` rows added |
| `schema/codelists/judicialInstance.csv` | **Created** |
| `schema/codelists/judicialPole.csv` | **Created** |
| `schema/codelists/judicialProcessBinding.csv` | **Created** |

## Test files affected

| File | Change |
|------|--------|
| `tests/test_schema.py` | `urn:judicialProcess` added to `schemas` list |
| `tests/data/valid-statements/judicialProcess_basic.json` | **Created** |
| `tests/data/valid-statements/judicialProcess_full.json` | **Created** |
| `tests/data/valid-statements/relationship_judicialParty.json` | **Created** |
| `tests/data/valid-statements/relationship_judicialRepresentative.json` | **Created** |
| `tests/data/invalid-statements/judicialProcess_missing_numero.json` | **Created** |
| `tests/data/invalid-statements/judicialProcess_invalid_nivelSigilo.json` | **Created** |
| `tests/data/invalid-statements/judicialProcess_invalid_instancia.json` | **Created** |
| `tests/data/invalid-statements/expected_errors.csv` | Three rows added for the new invalid fixtures |
| `examples/judicial-process-parties.json` | **Created** |
