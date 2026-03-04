# Field Descriptions for DLMDM Registers

This document describes the fields used across the four registers and how they relate to each other.

## Lemma Register Fields

- **LEMID** — unique identifier of the lemma (format: Ln).
- **WFID** — identifier of the word family to which the lemma belongs (format: Wn).
- **SGID** — identifier of the word-family subgroup to which the lemma belongs (format: SGn).
- **ROOTID** — identifier of the root surface form linked to this lemma (format: Rn).
- **PARENTID** — identifier of the lemma's parent in a parent set or multiple parent sets in the derivational hierarchy.
- **PARENTRES** — status of parent resolution (e.g., resolved, if string, unresolved if UNR).
- **CORLEMID** — identifier of the originally imported lemma (technical field; used for linking to LVK2018).
- **LEMMA** — normalized surface form of the lemma.
- **SEGMENTATION** — morphemic segmentation of the lemma.
- **POS** — part of speech.
- **FEATS** — morphological features.
- **WFTYPE** — type of word formation:  
  DERIV (derivation), COMP (compounding), MORPHON (morpho‑phonological derivation),  
  HYBR (hybrid formation), SEMANT (semantic derivation), LOANTR (loan translation),  
  OTHER (miscellaneous), NONE (primary or borrowed word).
- **PARENTFORM** — literal surface form of the parent lemma(s) before parent resolution.
- **WFCONSTR** — syntactic construction underlying compounds.
- **WFPHON** — phonological notes.
- **SOURCE** — identifier of the source in the source register.

## Root Register Fields

- **MORPHID** — unique identifier of the root surface form.
- **MORPHTYPE** — type of morpheme (e.g., ROOT_SG, a root surface form defined in a word-family subgroup).
- **FORM** — the root surface form (format: string).
- **WFID** — identifier of the word family to which the root belongs (format: Wn).
- **SGID** — identifier of the subgroup to which the root belongs (format: SGn).
- **SG_LABEL** — label of the subgroup, listing all defined allomorphs of the root.
- **STRATUM** — vocabulary stratum: BORROWED for loan roots, empty for inherited roots.
- **DEPTH** — hierarchical depth of the subgroup within the word-family structure.
- **WF_LABEL** — label of the word family; either *zero-element* (when all depth-0 subgroups are siblings) or the label of the single depth-0 subgroup.
- **PROVENANCE** — origin or justification of the root surface form (e.g., subgroup definition).
- **ROOTLEMMAS** — list of LEMIDs linked to this root surface form.

## Source Register Fields

- **SOURCE** — unique source identifier.
- **FREQUENCY** — raw count of lemmas from that source.
- **TYPE** — type of source, e.g., corpus, terminological database.
- **CITATION** — full title of the source.
- **PATH** — link to the source.

## Inter-register Relations

- Lemmas link to root surface forms via **ROOTID**.
- Lemmas link to sources via **SOURCE**.
- Lemmas link to parents via **PARENTID**.
- Lemmas link to word families via **WFID**.
- Lemmas link to word-family subgroups (sets of root allomorphs) via **SGID**.
- Root surface forms link to allomorphs (other root surface forms in the same SG) via **SGID**.
- Root surface forms link to lemmas via **SGID**.
- Root surface forms link to word family structures via **WFID** and **DEPTH**
