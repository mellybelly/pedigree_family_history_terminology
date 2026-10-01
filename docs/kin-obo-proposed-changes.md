# KIN to OBO Foundry: Proposed changes

_Snapshot of the shared review doc, exported 2026-10-01. Comments and edits happen in the doc; this copy is versioned with the ontology._ The plan itself is in [kin-obo-plan.md](kin-obo-plan.md).

Every change proposed in the plan, from file-wide settings down to each of KIN's 57 terms. Nothing has been applied yet; work happens on `claude/wizardly-wright-2nfcdk` after review.

## Ontology-wide changes

These 16 changes apply to the whole file or the repository, not to single terms.

| Area | Current | Proposed | Phase |
| --- | --- | --- | --- |
| Term IRIs | `http://purl.org/ga4gh/kin.owl#KIN_003` (3 digits) | `http://purl.obolibrary.org/obo/KIN_0000003`, same number. The GA4GH IRI is recorded on each term and keeps resolving. | 1 |
| Ontology IRI | `http://purl.org/ga4gh/kin.owl` | `http://purl.obolibrary.org/obo/kin.owl` | 1 |
| Version IRI | `.../ga4gh/rel/releases/2021-08-05/kin.owl` (wrong path) | `http://purl.obolibrary.org/obo/kin/releases/YYYY-MM-DD/kin.owl`, plus `owl:versionInfo` | 1 |
| Prefixes | Default prefix points to `fh.owl`; `ga4gh:` points to `rel.owl#` | Remove the stale prefixes; declare `obo:` and `KIN:` | 1 |
| License | Apache-2.0 for the whole repo; none in the ontology | `dcterms:license` CC BY 4.0 in the header. The repo is split: CC BY 4.0 for the ontology, Apache-2.0 for the code. | 1 |
| Header metadata | One comment ("A family relationships ontology.") | Title, description, scope statement (any organism), contact person with ORCID, homepage, issue tracker | 1 |
| Registry | Not registered | `KIN` in Bioregistry with both URI formats; OBO Foundry registration request | 1, 3 |
| Labels | camelCase (`isBiologicalParentOf`), TitleCase classes | Lowercase phrases ("biological parent of"). The camelCase label is kept as an exact synonym. | 2 |
| Definitions | `skos:definition`, some without a language tag | `IAO:0000115` with `@en` and a definition source. 37 definitions are generalized to any organism. | 2 |
| Imports | None | An RO module with BFO domain and range axioms stripped; PATO biological sex, female, male | 2 |
| Deprecation | No policy; 12 missing IDs (033–035, 037, 040–045, 048, 049) | `owl:deprecated` plus `IAO:0100001` (term replaced by). Obsolete stubs for missing IDs if they were ever published. | 2 |
| Human-only flag | None | An annotation on adoptive, step and legal relations marking them human-only | 2 |
| Formats and tooling | Functional syntax only; Maven and JFact | ODK repo, ROBOT reason and report, releases in OWL, OBO and JSON. The JUnit tests are kept. | 1 |
| Tests | 3 JUnit reasoner tests | Add tests for unknown sex, for the sibling fixes below, and a CI check that no BFO term enters | 1, 2 |
| Mappings | FHIR FamilyMember SSSOM (119 rows, not yet valid) | New SSSOM file mapping the HL7 SPCU sex codes to KIN. FamilyMember work deferred. | 2 |
| Documentation | README ("pre-alpha") | Docs site, CONTRIBUTING, OBO code of conduct, human and non-human examples | 2, 3 |

## Axiom fixes found in review

Reading the axioms turned up eight problems that aren't in the main plan. Each needs a reviewer's agreement before it changes.

| # | Term(s) | Problem | Proposed fix |
| --- | --- | --- | --- |
| 1 | KIN\_997 sex | Sex is defined as exactly "female or male", so anyone of unknown or specified sex is inferred to be female or male | Remove the covering axiom. Unknown sex is not disjoint with female or male. |
| 2 | KIN\_008 full sibling, KIN\_009–011 multiple birth | Symmetric plus transitive makes everyone with a sibling their own sibling. This is the same problem fixed for KIN\_007 in issue #34. | Remove transitivity. Add a test that no one is their own sibling. |
| 3 | KIN\_001 relative of | Symmetric plus transitive: everyone is their own relative, and chains through partners or step-relations make unrelated people relatives | Remove transitivity |
| 4 | KIN\_009 multiple birth sibling | Placed under full sibling, but litter-mates and some twins have different fathers (multiple sires, which is common in dogs and cats) | Move under biological sibling. Monozygotic (KIN\_010) stays under full sibling. |
| 5 | KIN\_029 mitochondrial donor | Placed under relative of, not biological relative of, although the donor contributes mtDNA | Move under biological relative of (for review) |
| 6 | KIN\_013 parental sibling | No property chain, so aunts and uncles are never inferred | Add the chain biological sibling ∘ biological parent → parental sibling |
| 7 | KIN\_031 has sex | No range, and the definition says "person" | Range KIN sex (KIN\_997). Subproperty of RO:0000053 "bearer of". Generalize the definition. |
| 8 | KIN\_005 gestational carrier of | The range is set to Woman, but the range of this relation is the child. Any child with a gestational carrier is inferred to be a woman. | Replace with a domain of "has sex some female" |

## Term by term

Of KIN's 57 terms, 6 move to RO in batch 1, 5 are candidates for batch 2, 3 are deprecated and 43 stay in KIN with changes. Every term also gets the ontology-wide changes above: a new OBO ID with the same number, a lowercase label with the camelCase form kept as a synonym, and an IAO definition. A KIN term whose relation moves to RO is deprecated with "term replaced by" pointing to the RO term once it exists. Reviewers can change the Destination column.

| ID | Current label | Proposed label | Destination | Changes |
| --- | --- | --- | --- | --- |
| KIN\_001 | isRelativeOf | relative of | KIN | KIN root. Drop the Person domain and range. Remove transitivity (fix 3). Generalize the definition. |
| KIN\_002 | isBiologicalRelativeOf | biological relative of | RO batch 1 | Proposed as the RO kinship root. Generalize the definition. |
| KIN\_003 | isBiologicalParentOf | biological parent of | RO batch 1 | Core RO relation. Generalize the definition. |
| KIN\_032 | isBiologicalChildOf | has biological parent | RO batch 1 | Inverse of KIN\_003 in RO. "biological child of" kept as a synonym. |
| KIN\_027 | isBiologicalMotherOf | maternal parent of | RO batch 1 | Defined by the egg or ovule contributed (open question 1). "biological mother of" kept as a synonym. The Woman domain is removed. |
| KIN\_028 | isBiologicalFatherOf | paternal parent of | RO batch 1 | Defined by the sperm or pollen contributed. "biological father of" kept as a synonym. The Man domain is removed. |
| KIN\_007 | isBiologicalSiblingOf | biological sibling of | RO batch 1 | Defined as sharing at least one biological parent. Symmetric, not transitive. |
| KIN\_008 | isFullsiblingOf | full sibling of | RO batch 2 candidate | Remove transitivity (fix 2). Fixes the label casing. |
| KIN\_012 | isHalfSiblingOf | half sibling of | RO batch 2 candidate | No axiom change |
| KIN\_009 | isMultipleBirthSiblingOf | multiple birth sibling of | RO batch 2 candidate | Move under biological sibling (fix 4). Remove transitivity. Definition covers litter-mates. |
| KIN\_010 | isMonozygoticMultipleBirthSiblingOf | monozygotic multiple birth sibling of | RO batch 2 candidate | Stays under full sibling. Remove transitivity. |
| KIN\_011 | isPolyzygoticMultipleBirthSiblingOf | polyzygotic multiple birth sibling of | RO batch 2 candidate | Remove transitivity |
| KIN\_054 | isMaternalHalfSiblingOf | maternal half sibling of | KIN | Builds on the RO maternal parent relation |
| KIN\_055 | isPaternalHalfSiblingOf | paternal half sibling of | KIN | Builds on the RO paternal parent relation |
| KIN\_013 | isParentalSiblingOf | parental sibling of | KIN | Add the chain sibling ∘ parent (fix 6). Generalize the definition. |
| KIN\_046 | hasParentalSibling | has parental sibling | KIN | Generalize the definition |
| KIN\_058 | isMaternalUncleOf | maternal uncle of | KIN | Man domain becomes "has sex some male". Generalize the definition. |
| KIN\_059 | isPaternalUncleOf | paternal uncle of | KIN | Man domain becomes "has sex some male". Generalize the definition. |
| KIN\_060 | isMaternalAuntOf | maternal aunt of | KIN | Woman domain becomes "has sex some female". Generalize the definition. |
| KIN\_061 | isPaternalAuntOf | paternal aunt of | KIN | Woman domain becomes "has sex some female". Generalize the definition. |
| KIN\_014 | isCousinOf | cousin of | KIN | Chain rebuilt over the RO terms. Generalize the definition. |
| KIN\_015 | isMaternalCousinOf | maternal cousin of | KIN | Chain uses RO maternal parent of |
| KIN\_016 | isPaternalCousinOf | paternal cousin of | KIN | Chain uses RO paternal parent of |
| KIN\_017 | isGrandparentOf | grandparent of | KIN | Chain over RO parent. Later a subproperty of "biological ancestor of" (RO batch 2). |
| KIN\_018 | isGreatGrandparentOf | great-grandparent of | KIN | Same as grandparent of |
| KIN\_036 | isGrandchildOf | grandchild of | KIN | Generalize the definition |
| KIN\_047 | isGreatGrandchildOf | great-grandchild of | KIN | Generalize the definition |
| KIN\_052 | isMaternalGrandparentOf | maternal grandparent of | KIN | Chain uses RO maternal parent of |
| KIN\_053 | isPaternalGrandparentOf | paternal grandparent of | KIN | Chain uses RO paternal parent of |
| KIN\_004 | isSpermDonorOf | sperm donor of | KIN | Under RO paternal parent of. Man domain becomes "has sex some male". Definition covers semen donors in breeding. |
| KIN\_038 | isOvumDonorOf | ovum donor of | KIN | Under RO maternal parent of. Woman domain becomes "has sex some female". |
| KIN\_006 | isSurrogateOvumDonorOf | surrogate ovum donor of | KIN | Woman domain becomes "has sex some female". Generalize the definition. |
| KIN\_005 | isGestationalCarrierOf | gestational carrier of | KIN | Fix the wrong range (fix 8). Definition covers embryo-transfer recipients. |
| KIN\_039 | hasGestationalCarrier | has gestational carrier | KIN | Generalize the definition |
| KIN\_029 | isMitochondrialDonorOf | mitochondrial donor of | KIN | Move under biological relative of (fix 5). Woman domain becomes "has sex some female". |
| KIN\_019 | isSocialLegalRelativeOf | social or legal relative of | KIN | No axiom change |
| KIN\_020 | isParentFigureOf | parent figure of | KIN | Generalize the definition |
| KIN\_021 | isFosterParentOf | foster parent of | KIN | Definition covers cross-fostering in animals |
| KIN\_022 | isAdoptiveParentOf | adoptive parent of | KIN | Annotate as human-only |
| KIN\_023 | isStepParentOf | step-parent of | KIN | Annotate as human-only |
| KIN\_024 | isSiblingFigureOf | sibling figure of | KIN | No axiom change |
| KIN\_025 | isStepSiblingOf | step-sibling of | KIN | Annotate as human-only |
| KIN\_056 | isMaternalStepSiblingOf | maternal step-sibling of | KIN | Annotate as human-only |
| KIN\_057 | isPaternalStepSiblingOf | paternal step-sibling of | KIN | Annotate as human-only |
| KIN\_026 | isPartnerOf | partner of | KIN | Add the synonym "mate of" for non-human pedigrees |
| KIN\_050 | isSeparatedPartnerOf | separated partner of | KIN | Annotate as human-only |
| KIN\_030 | isConsanguineousPartnerOf | consanguineous partner of | KIN | Add the synonym "consanguineous mate of" |
| KIN\_051 | isSeparatedConsanguineousPartnerOf | separated consanguineous partner of | KIN | Annotate as human-only |
| KIN\_031 | hasSex | has sex | KIN | Range KIN sex. Subproperty of RO:0000053. Generalize the definition (fix 7). |
| KIN\_997 | Sex | sex | KIN | Subclass of PATO:0000047. Remove the covering axiom (fix 1). Maps to the HL7 SPCU value set. |
| KIN\_995 | Female | female | KIN | Subclass of PATO:0000383. Maps to SPCU `female-typical`. |
| KIN\_996 | Male | male | KIN | Subclass of PATO:0000384. Maps to SPCU `male-typical`. |
| KIN\_991 | OtherSex | specified sex | KIN | Relabeled. Maps to SPCU `specified`. New definition. |
| KIN\_992 | UnknownSex | unknown sex | KIN | Maps to SPCU `unknown`. Not disjoint with female or male. |
| KIN\_993 | Woman | — | Deprecate | Replaced by "has sex some female" in all axioms |
| KIN\_994 | Man | — | Deprecate | Replaced by "has sex some male" in all axioms |
| KIN\_998 | Person | — | Deprecate | Domain and range dropped. Use RO "in taxon" where a species matters. |
