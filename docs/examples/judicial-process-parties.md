# Judicial process parties (MNI)

This example shows a civil compensation claim (*ação de indenização por dano moral*) filed before a first-instance court in São Paulo. A CPF-identified individual plaintiff is in the active pole, a CNPJ-identified company is in the passive pole, and an OAB-identified lawyer represents the plaintiff.

The seven statements — one judicial process, two persons, one entity, and three relationships — demonstrate how beneficial ownership graph data can be extended with judicial party information sourced from the Brazilian MNI/CNJ interoperability standard.

For modelling guidance see [Representing judicial processes](../standard/modelling/repr-judicial-process.md).

```json
[
  {
    "statementId": "example-jp-001-stmnt-00000000000001",
    "declarationSubject": "example-jp-001",
    "recordId": "example-jp-001",
    "recordType": "judicialProcess",
    "recordDetails": {
      "numero": "10001234520250260001",
      "classeProcessual": 7,
      "codigoLocalidade": "SP",
      "nivelSigilo": 0,
      "intervencaoMP": false,
      "dataAjuizamento": "2025-03-10",
      "orgaoJulgador": {
        "codigoOrgao": "8026",
        "nomeOrgao": "3ª Vara Cível do Foro Regional de Pinheiros",
        "codigoMunicipioIBGE": 3550308,
        "instancia": "ORIG"
      },
      "valorCausa": 120000.00,
      "assuntos": [
        {
          "codigoNacional": 10706,
          "descricao": "Indenização por Dano Moral"
        }
      ]
    },
    "publicationDetails": {
      "publicationDate": "2025-06-09",
      "bodsVersion": "0.4",
      "publisher": {
        "name": "Example Publisher",
        "url": "https://example.org"
      }
    },
    "statementDate": "2025-06-09"
  },
  {
    "statementId": "example-jp-001-stmnt-00000000000002",
    "declarationSubject": "example-jp-001",
    "recordId": "person-plaintiff-001",
    "recordType": "person",
    "recordDetails": {
      "isComponent": false,
      "personType": "knownPerson",
      "names": [
        {
          "type": "legal",
          "fullName": "Maria da Silva Santos"
        }
      ],
      "identifiers": [
        {
          "scheme": "BR-CPF",
          "id": "123.456.789-00"
        }
      ],
      "nationalities": [
        {
          "name": "Brazil",
          "code": "BR"
        }
      ]
    },
    "publicationDetails": {
      "publicationDate": "2025-06-09",
      "bodsVersion": "0.4",
      "publisher": {
        "name": "Example Publisher",
        "url": "https://example.org"
      }
    },
    "statementDate": "2025-06-09"
  },
  {
    "statementId": "example-jp-001-stmnt-00000000000003",
    "declarationSubject": "example-jp-001",
    "recordId": "entity-defendant-001",
    "recordType": "entity",
    "recordDetails": {
      "isComponent": false,
      "entityType": {
        "type": "registeredEntity"
      },
      "name": "Banco Exemplo S.A.",
      "identifiers": [
        {
          "scheme": "BR-CNPJ",
          "id": "00.000.000/0001-91"
        }
      ],
      "jurisdiction": {
        "name": "Brazil",
        "code": "BR"
      }
    },
    "publicationDetails": {
      "publicationDate": "2025-06-09",
      "bodsVersion": "0.4",
      "publisher": {
        "name": "Example Publisher",
        "url": "https://example.org"
      }
    },
    "statementDate": "2025-06-09"
  },
  {
    "statementId": "example-jp-001-stmnt-00000000000004",
    "declarationSubject": "example-jp-001",
    "recordId": "person-lawyer-001",
    "recordType": "person",
    "recordDetails": {
      "isComponent": false,
      "personType": "knownPerson",
      "names": [
        {
          "type": "legal",
          "fullName": "João Carlos Pereira"
        }
      ],
      "identifiers": [
        {
          "scheme": "BR-OAB",
          "id": "SP 123456"
        }
      ]
    },
    "publicationDetails": {
      "publicationDate": "2025-06-09",
      "bodsVersion": "0.4",
      "publisher": {
        "name": "Example Publisher",
        "url": "https://example.org"
      }
    },
    "statementDate": "2025-06-09"
  },
  {
    "statementId": "example-jp-001-stmnt-00000000000005",
    "declarationSubject": "example-jp-001",
    "recordId": "rel-plaintiff-jp-001",
    "recordType": "relationship",
    "recordDetails": {
      "isComponent": false,
      "subject": "example-jp-001",
      "interestedParty": "person-plaintiff-001",
      "interests": [
        {
          "type": "judicialParty",
          "judicialInterestDetails": {
            "polo": "AT",
            "interessePublico": false,
            "assistenciaJudiciaria": false
          }
        }
      ]
    },
    "publicationDetails": {
      "publicationDate": "2025-06-09",
      "bodsVersion": "0.4",
      "publisher": {
        "name": "Example Publisher",
        "url": "https://example.org"
      }
    },
    "statementDate": "2025-06-09"
  },
  {
    "statementId": "example-jp-001-stmnt-00000000000006",
    "declarationSubject": "example-jp-001",
    "recordId": "rel-defendant-jp-001",
    "recordType": "relationship",
    "recordDetails": {
      "isComponent": false,
      "subject": "example-jp-001",
      "interestedParty": "entity-defendant-001",
      "interests": [
        {
          "type": "judicialParty",
          "judicialInterestDetails": {
            "polo": "PA",
            "interessePublico": false
          }
        }
      ]
    },
    "publicationDetails": {
      "publicationDate": "2025-06-09",
      "bodsVersion": "0.4",
      "publisher": {
        "name": "Example Publisher",
        "url": "https://example.org"
      }
    },
    "statementDate": "2025-06-09"
  },
  {
    "statementId": "example-jp-001-stmnt-00000000000007",
    "declarationSubject": "example-jp-001",
    "recordId": "rel-lawyer-jp-001",
    "recordType": "relationship",
    "recordDetails": {
      "isComponent": false,
      "subject": "example-jp-001",
      "interestedParty": "person-lawyer-001",
      "interests": [
        {
          "type": "judicialRepresentative",
          "judicialInterestDetails": {
            "polo": "AT"
          }
        }
      ]
    },
    "publicationDetails": {
      "publicationDate": "2025-06-09",
      "bodsVersion": "0.4",
      "publisher": {
        "name": "Example Publisher",
        "url": "https://example.org"
      }
    },
    "statementDate": "2025-06-09"
  }
]
```

## Statement breakdown

| # | `recordId` | `recordType` | Role |
|---|-----------|-------------|------|
| 1 | `example-jp-001` | `judicialProcess` | The process itself — 1st-instance court, SP, `nivelSigilo: 0` |
| 2 | `person-plaintiff-001` | `person` | Plaintiff — identified by BR-CPF |
| 3 | `entity-defendant-001` | `entity` | Defendant — identified by BR-CNPJ |
| 4 | `person-lawyer-001` | `person` | Plaintiff's lawyer — identified by BR-OAB |
| 5 | `rel-plaintiff-jp-001` | `relationship` | Plaintiff → process, `judicialParty`, polo `AT` |
| 6 | `rel-defendant-jp-001` | `relationship` | Defendant → process, `judicialParty`, polo `PA` |
| 7 | `rel-lawyer-jp-001` | `relationship` | Lawyer → process, `judicialRepresentative`, polo `AT` |
