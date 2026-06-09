# Schema reference — judicial process additions

This document describes the schema objects and codelists added by the MNI judicial process integration. These sections extend the [main schema reference](reference.rst).

---

## Record Details (judicial process)

Details of a judicial process (*processo judicial*) conforming to the Brazilian MNI/CNJ standard (Módulo Nacional de Interoperabilidade, Resolução CNJ 46).

See [Representing judicial processes](modelling/repr-judicial-process.md) for detailed requirements and modelling guidance.

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `numero` | string | yes | Unique CNJ process number per Resolution 65 — exactly 20 consecutive digits without mask (`NNNNNNN-DD.AAAA.J.TT.OOOO` without separators). Pattern: `\d{20}` |
| `classeProcessual` | integer | yes | Procedural class code conforming to CNJ Resolution 46 (Tabelas Processuais Unificadas). |
| `nivelSigilo` | integer (0–5) | yes | Process confidentiality level: 0 (*público*) to 5 (*sigilo absoluto*). Controls which parties may access the process data. |
| `orgaoJulgador` | OrgaoJulgador | yes | The court or tribunal body responsible for adjudicating the process. |
| `codigoLocalidade` | string | no | Code identifying the locality or comarca where the process is filed. |
| `intervencaoMP` | boolean | no | Whether the Ministério Público is intervening in the process. |
| `dataAjuizamento` | string (date) | no | The date on which the process was filed (*data de ajuizamento*). |
| `valorCausa` | number (≥ 0) | no | The monetary value of the cause of action (*valor da causa*) in the currency of the jurisdiction. |
| `assuntos` | array of AssuntoProcessual | no | Subject-matter classifications using national TPU codes and/or local tribunal codes. |
| `movimentos` | array of MovimentoProcessual | no | Chronological record of procedural events and movements in the process docket. |
| `outrosNumeros` | array of string | no | Legacy or alternative numbering schemes for this process. |
| `vinculacoes` | array of VinculacaoProcessual | no | Formal links to related processes (rescissory action, delegated jurisdiction, repetitive appeal, general repercussion). |
| `outrosParametros` | array of Parametro | no | Extensible key/value pairs for process data not covered by the standard structure. |

### OrgaoJulgador

The court or tribunal body adjudicating the process (*órgão julgador*).

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `codigoOrgao` | string | yes | Unique code identifying the adjudicating body within the tribunal system. |
| `nomeOrgao` | string | yes | Full name of the adjudicating body. |
| `codigoMunicipioIBGE` | integer | yes | Brazilian IBGE numeric code for the municipality of the adjudicating body. |
| `instancia` | string (enum) | yes | The judicial instance of the adjudicating body. See [Judicial Instance](#judicial-instance) codelist. |

### AssuntoProcessual

A subject-matter classification for the process using national TPU codes (Resolução CNJ 46) and/or local tribunal codes.

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `codigoNacional` | integer | no | National TPU subject code as defined by Resolução CNJ 46. |
| `codigoLocal` | string | no | Tribunal-specific local subject code. |
| `descricao` | string | no | Textual description of the subject matter. |

### MovimentoProcessual

A procedural event or movement recorded in the process docket (*andamento processual*).

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `dataHora` | string (date-time) | yes | Date and time at which the movement was recorded. Must include timezone offset (RFC 3339). |
| `codigoNacional` | integer | no | National TPU movement code as defined by Resolução CNJ 46. |
| `descricaoLocal` | string | no | Tribunal-specific textual description of the movement. |
| `identificadorMovimento` | string | no | Unique identifier for the movement within the tribunal's system. |
| `complemento` | string | no | Additional textual information complementing the movement description. |

### VinculacaoProcessual

A formal link between this process and a related process.

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `tipo` | string (enum) | yes | The nature of the relationship between the two processes. See [Judicial Process Binding](#judicial-process-binding) codelist. |
| `numeroProcessoVinculado` | string | yes | The 20-digit CNJ unique number of the linked process. Pattern: `\d{20}` |

### Parametro

An extensible key/value pair for process data not covered by the standard structure (*outroParametro*).

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `nome` | string | yes | The name of the additional parameter. |
| `valor` | string | yes | The value of the additional parameter. |

---

## JudicialInterestDetails

Additional details for interests representing participation in a judicial process (`judicialParty` or `judicialRepresentative`). This object is carried in `interests[].judicialInterestDetails` on Relationship statements.

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `polo` | string (enum) | no | The procedural pole (*polo processual*) of this party in the judicial process. See [Judicial Pole](#judicial-pole) codelist. |
| `interessePublico` | boolean | no | Whether the party's participation is designated as having a public interest (*interesse público*). |
| `assistenciaJudiciaria` | boolean | no | Whether the party is receiving legal aid assistance (*assistência judiciária gratuita*). |

---

## Codelists

### Judicial Instance

Values for `orgaoJulgador.instancia`.

| Code | Title | Description |
|------|-------|-------------|
| `ORIG` | Original jurisdiction | Court exercising original (first-instance) jurisdiction over the process. |
| `REV` | Appellate review | Court exercising second-instance appellate review. |
| `ESP` | Special review | Court exercising special review jurisdiction (e.g. STJ in Brazil). |
| `EXT` | Extraordinary review | Court exercising extraordinary review jurisdiction (e.g. STF in Brazil). |
| `ADM` | Administrative | Administrative body acting in a judicial or quasi-judicial capacity. |

### Judicial Pole

Values for `judicialInterestDetails.polo`. This is an open codelist — local systems may define additional values.

| Code | Title | Description |
|------|-------|-------------|
| `AT` | Active pole | Party that initiated or is in the active position of the proceedings (*polo ativo* / author). |
| `PA` | Passive pole | Party responding to the proceedings (*polo passivo* / defendant). |
| `TC` | Third party | Party participating as a third party (*terceiro interveniente*). |
| `FL` | Public interest watchdog | Party participating as *fiscal da lei* or *custos legis* in oversight of the public interest. |

### Judicial Process Binding

Values for `vinculacoes[].tipo`.

| Code | Title | Description |
|------|-------|-------------|
| `AR` | Rescissory action | This process is an *ação rescisória* seeking to annul the linked process. |
| `CD` | Delegated jurisdiction | The adjudicating body's jurisdiction was delegated from the linked process. |
| `RR` | Repetitive appeal | This process is a repetitive appeal (*recurso repetitivo*) linked to the referenced leading case. |
| `RG` | General repercussion | This process has been recognised as having general repercussion (*repercussão geral*) linked to the referenced leading case. |
