.. _representing-judicial-process:

Representing judicial processes
================================

Overview
--------

BODS can be used to represent the parties to a judicial or quasi-judicial process by linking process records to the persons and entities involved. This makes it possible to surface beneficial ownership connections that emerge through litigation — for example, identifying when a known beneficial owner is also a party to a significant commercial dispute or insolvency proceeding.

Judicial process support in BODS is aligned with the Brazilian **Módulo Nacional de Interoperabilidade (MNI)**, the interoperability standard published by the Conselho Nacional de Justiça (CNJ) under Resolução CNJ 46. Although the data model reflects Brazilian procedural law, the core concepts — a process number, a presiding body, procedural parties, and legal representatives — are broadly applicable to judicial systems that use a civil-law docket structure.

A judicial process is represented by a statement with ``recordType`` set to ``judicialProcess``. The parties and representatives to that process are then expressed as Relationship statements linking Person or Entity records to the judicial process record, using two new ``interest.type`` values:

* ``judicialParty`` — a party with a designated procedural pole (*polo processual*)
* ``judicialRepresentative`` — a lawyer or other legal representative acting for a party

The procedural pole and additional Brazilian MNI flags are carried in a ``judicialInterestDetails`` object attached to each relevant Interest.

Connecting judicial process records to the ownership graph
----------------------------------------------------------

The primary join key between a judicial process and the beneficial ownership graph is the official identifier of each party. In Brazil this is:

* **CPF** (*Cadastro de Pessoas Físicas*) for natural persons — use ``identifier.scheme`` ``"BR-CPF"``
* **CNPJ** (*Cadastro Nacional da Pessoa Jurídica*) for legal entities — use ``identifier.scheme`` ``"BR-CNPJ"``
* **OAB** (*Ordem dos Advogados do Brasil*) for lawyers — use ``identifier.scheme`` ``"BR-OAB"``

A Person or Entity statement that carries one of these identifiers in its ``recordDetails.identifiers`` array can be matched across datasets — for example, to link a beneficial owner register entry to a judicial party list — without publishing private data beyond what the source systems already disclose.

When modelling judicial process data:

* ``recordDetails.numero`` MUST be the 20-digit CNJ process number in unmasked form (digits only, no separators).
* ``recordDetails.nivelSigilo`` MUST be set. Use ``0`` for publicly accessible processes. Values ``1``–``5`` indicate increasing confidentiality; publishers SHOULD omit or redact ``recordDetails`` content that is not authorised for public disclosure at the given level.
* ``recordDetails.orgaoJulgador`` MUST be present and MUST include ``instancia`` using the ``judicialInstance`` codelist.
* Relationship statements MUST set ``interests[].type`` to ``"judicialParty"`` or ``"judicialRepresentative"``.
* ``interests[].judicialInterestDetails.polo`` SHOULD be set for ``judicialParty`` interests, using the ``judicialPole`` codelist.
* The relationship ``subject`` MUST be the ``recordId`` of the judicial process record, and ``declarationSubject`` on every statement in the declaration MUST also be that ``recordId``.
* Judicial interests represent procedural participation, not ownership or control. ``interests[].beneficialOwnershipOrControl`` MUST NOT be ``true`` on interests of type ``judicialParty`` or ``judicialRepresentative`` — the schema rejects such data. Data consumers performing beneficial ownership analysis SHOULD exclude these interest types.

Modelling scenarios
-------------------

Individual as plaintiff, company as defendant
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

A natural person brings an action against a company. A lawyer represents the plaintiff.

**Statements required:**

1. A ``judicialProcess`` statement — the process record.
2. A ``person`` statement for the plaintiff — carries a ``BR-CPF`` identifier.
3. An ``entity`` statement for the defendant — carries a ``BR-CNPJ`` identifier.
4. A ``person`` statement for the lawyer — carries a ``BR-OAB`` identifier.
5. A ``relationship`` statement linking the plaintiff to the process — ``type: "judicialParty"``, ``polo: "AT"`` (active pole).
6. A ``relationship`` statement linking the defendant to the process — ``type: "judicialParty"``, ``polo: "PA"`` (passive pole).
7. A ``relationship`` statement linking the lawyer to the process — ``type: "judicialRepresentative"``, ``polo: "AT"`` (representing the active pole).

See :ref:`examples-judicial-process` for the full JSON.

Multiple parties on each pole
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Where several persons or entities appear on the same procedural pole, each is represented by its own Person or Entity statement and its own Relationship statement. The Relationship statements share the same ``subject`` (the ``recordId`` of the judicial process) and all carry the same ``polo`` value.

Third-party interventors
^^^^^^^^^^^^^^^^^^^^^^^^

A third party intervening in the process (*terceiro interveniente*) is modelled as a ``judicialParty`` interest with ``polo: "TC"``. If the interventor brings their own legal representative, an additional ``judicialRepresentative`` Relationship statement is added.

Public interest oversight (*custos legis*)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

When the Ministério Público participates as *fiscal da lei* — in an oversight role rather than as a party — model this as a ``judicialParty`` interest with ``polo: "FL"``. Set ``recordDetails.intervencaoMP`` to ``true`` on the judicial process record to signal the intervention at the process level.

Linked processes
^^^^^^^^^^^^^^^^

Where a process is formally linked to another (for example, a repetitive appeal linked to a leading case under *recursos repetitivos*), use ``recordDetails.vinculacoes`` on the ``judicialProcess`` statement. Each entry specifies a ``tipo`` from the ``judicialProcessBinding`` codelist and the ``numeroProcessoVinculado`` of the linked process.

If the linked process is also published in BODS, its ``judicialProcess`` statement will carry the same 20-digit number in its ``recordDetails.numero`` field, enabling graph traversal between the two records.

Confidentiality
---------------

``recordDetails.nivelSigilo`` maps to the six CNJ confidentiality levels defined in Resolução CNJ 46:

.. list-table::
   :widths: 10 30 60
   :header-rows: 1

   * - Level
     - Label
     - Meaning
   * - 0
     - Public (*público*)
     - Freely accessible. All ``recordDetails`` fields MAY be published.
   * - 1
     - Restricted (*segredo de justiça*)
     - Access restricted to parties and their representatives. Publishers SHOULD omit party names and identifiers.
   * - 2
     - Internal (*interno*)
     - Access restricted to tribunal staff. Publishers SHOULD omit all ``recordDetails`` beyond ``numero`` and ``nivelSigilo``.
   * - 3
     - Confidential (*confidencial*)
     - As level 2; additional statutory basis applies.
   * - 4
     - Secret (*secreto*)
     - Classified information. Publishers MUST omit all ``recordDetails`` beyond ``numero`` and ``nivelSigilo``.
   * - 5
     - Absolute secrecy (*sigilo absoluto*)
     - Highest classification. Publishers MUST omit all ``recordDetails`` beyond ``numero`` and ``nivelSigilo``.

Publishers handling processes with ``nivelSigilo >= 1`` SHOULD consult applicable data protection legislation and their own disclosure authorisation before publishing any ``recordDetails`` fields beyond ``numero``, ``classeProcessual``, and ``nivelSigilo``.

Schema reference
----------------

See :ref:`schema-judicial-process-record` for the full schema definition of the ``judicialProcess`` record details object and its sub-objects, and :ref:`schema-judicial-interest-details` for the ``JudicialInterestDetails`` object.

Codelists used:

* :ref:`codelist-judicial-instance` — values for ``orgaoJulgador.instancia``
* :ref:`codelist-judicial-pole` — values for ``judicialInterestDetails.polo``
* :ref:`codelist-judicial-process-binding` — values for ``vinculacoes[].tipo``
