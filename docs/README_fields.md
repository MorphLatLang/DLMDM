# Field Descriptions for DLMDM Registers

This document describes the fields used across the four registers and how they relate to each other. All numeric identifiers (e.g., `L[0-9]+`, `WF[0-9]+`, `SG[0-9]+`, `R[0-9]+`) are **version-dependent** and guaranteed to be consistent and unique only within a given database release; `CORLEMID` values are stable across versions.

Where possible, the notation in this document follows standard regular expression conventions. For brevity, the following placeholders are used:

| Placeholder | Meaning |
| ----------- | --------- |
| WFTYPE | `(DERIV\|MORPHON\|COMP\|HYBR\|SEMANT\|LOANTR\|OTHER\|NONE)` |
| LatvianLetter | `[AaĀāBbCcČčDdEeĒēFfGgĢģHhIiĪīJjKkĶķLlĻļMmNnŅņOoPpRrSsŠšTtUuŪūVvZzŽž]` |
| LatvianLower | `[aābcčdeēfgģhiījkķlļmnņoprssštuūvzž]` |
| PREFIX, SUFFIX, WORDMEDIAL | `LatvianLetter+` |
| ROOT | `\(LatvianLetter+\)` |
| POS | `(NOUN\|PROPN\|ADJ\|ADV\|VERB\|INTJ\|PRON\|NUM\|ADP\|PART\|CCONJ\|SCONJ\|OTHER)` |
| FEATS | `(NounClass\|NumeralClass\|AdjectiveClass\|OtherClass\|PropnClass\|PartClass\|VerbForm)=(Gen\|Indecl\|PlTantum\|Part\|Propn)` |
| WFCONSTR | `[AaĀāBbCcČčDdEeĒēFfGgĢģHhIiĪīJjKkĶķLlĻļMmNnŅņOoPpRrSsŠšTtUuŪūVvZzŽž .,;:!?]+` |
| POSITION | For PREFIX: `WORD_INITIAL\|NON_INITIAL`, for SUFFIX: `AFTER_ROOT\|MIDDLE\|WORD_FINAL`, for WORDMEDIAL: `ONLY\|AFTER_ROOT\|MIDDLE\|BEFORE_ROOT` |

Operators such as `*`, `+`, `?`, `|`, `( )`, `[ ]`, retain their usual regex meanings unless escaped; all other characters are treated as literals unless otherwise noted, e.g.:

| Symbol        | Meaning                                   | Example                     | Explanation                                                                 |
|---------------|--------------------------------------------|-----------------------------|-----------------------------------------------------------------------------|
| `[...]`     | Character class (allowed characters)    | `[0-9]`                     | Matches one digit. |
| `(...)`     | Grouping                                   | `(PREFIX\-)`            | Groups multiple characters or tokens into a single unit, in this case, a token corresponding to the contents of the placeholder `PREFIX` followed by a hyphen.                      |
| `?`           | Optional (zero or one)                     | `LatvianLetter?` | One character corresponding to the placeholder `LatvianLetter` is allowed but is not obligatory.                                              |
| `*`           | Zero or more                               | `LatvianLetter*`                   | Matches any number of characters corresponding to the placeholder `LatvianLetter`, including none.                             |
| `+`           | One or more                                | `[0-9]+`                    | Matches one or more digits; at least one is required.           |
| Literal separators used in database columns | Escaped </br></br> Not escaped                         | `\+`, `\*`, `\(`, `\)`, `\-` </br></br> `;`, `:`, `_`, `=`   | Used literally; not operators.    |

When reading the raw .md source of this guide, keep in mind that, in some cases, Markdown syntax requires certain characters to be escaped. For example, the pipe character (`|`) must be written as `\|` inside table cells so the table renders correctly. In the rendered Markdown, however, this appears as an unescaped `|` and is intended to represent the logical OR operator, not a literal pipe.

## Lemma Register Fields

`PARENTID` and `PARENTRES` values are automatically generated from the manually validated contents of `WFTYPE` and `PARENTFORM`.

| Field        | Description | Requirement | Format | Example |
|--------------|-------------|----------|---------------|----------|
| **LEMID**    | Lemma identifier | Required | `L[0-9]+` | `L42` |
| **WFID**     | Word-family identifier or multiple identifiers (for compounds) separated by a comma; defines the word-family membership of the lemma | Required | `WF[0-9]+(,WF[0-9]+)*` | `WF202`, `WF202,WF50` |
| **SGID**     | Subgroup identifier or multiple identifiers (for compounds) separated by a comma; identifies the subgroup(s) to which the lemma belongs. | Required | `SG[0-9]+(,SG[0-9]+)*` | `SG12`, `SG138,SG33` |
| **ROOTID**   | Root surface form identifier or multiple identifiers separated by a comma; references the root surface form(s) linked to the lemma. | Required | `R[0-9]+(,R[0-9]+)*` | `R57`, `R30,R366` |
| **PARENTID** | Any number of parent sets, each defined by a `WFTYPE`. Within a set, each parent is either a `LEMID` (if resolved), a literal string extracted from `PARENTFORM` (if unresolved), or a literal string preceded by * (if a combining form). Parent sets represent alternative paths of word formation. | Conditional: empty if `WFTYPE` = `NONE` | `((WFTYPE:PARENT(\+PARENT)*)(;WFTYPE:PARENT(\+PARENT)*)*)?` </br></br> PARENT = `LEMID` or `\*?LatvianLetter+` | `COMP:L23+L6138` </br> `DERIV:biezbārdas;COMP:L2665+L1342`|
| **PARENTRES**| Resolution status of each parent in each parent set; a parent is either a literal string from the parent lemma’s `LEMMA` field (if resolved), a literal string preceded by \* (if a combining form), or `UNR` (if unresolved). | Conditional: empty if `WFTYPE` = `NONE` |  `((WFTYPE:PARENT(\+PARENT)*)(;WFTYPE:PARENT(\+PARENT)*)*)?` </br></br> PARENT = `\*?LatvianLetter+` or `UNR` | `COMP:ābols+diena` </br> `DERIV:UNR;COMP:biezs+bārda` |
| **CORLEMID** | Original lemma imported from the corpus; may include an additional identifier; **case-sensitive**. | Required | `LatvianLetter+(_(LatvianLetter+))?` | `dižbrūkleņa` (normalized surface form: `dižbrūklene`) </br> `sarkanacains_adj` (normalized surface form: `sarkanacains` |
| **LEMMA**    | Normalized surface form; **case-sensitive** | Required | `LatvianLetter+` | `darīt` |
| **SEGMENTATION** | Morphemic segmentation; **case-sensitive** | Required | `(PREFIX-)*ROOT(-SUFFIX)*` for single-root words or `(PREFIX-)*ROOT((-WORDMEDIAL)*-ROOT)+(-SUFFIX)*` for multi-root words | `(dar)-ī-t`, `sa-(run)-(biedr)-s` |
| **POS**      | Part-of-speech tag according to the UD format, the extra tag `OTHER` is reserved for special cases | Required | `POS` | `NOUN`, `PROPN`, `ADJ`, `OTHER` |
| **FEATS**    | Morphological features | Optional | `((FEATS)(,FEATS)*)?` | `NounClass=Gen`, `NumeralClass=Gen`, `AdjeciveClass=Indecl`, `VerbForm=Part,PartClass=Propn` |
| **WFTYPE**   | Database-specific tag defining means of word formation; several delimited tags represent alternative paths of word formation | Required | `WFTYPE(;WFTYPE)*` | `DERIV`, `MORPHON`, `COMP`, `HYBR`, `SEMANT`, `LOANTR`, `OTHER`, `NONE`, `DERIV;COMP`, `SEMANT;COMP` |
| **PARENTFORM** | Manually defined parent sets; each parent is a literal string corresponding to the surface (base) form of the source word or to a combining form (preceded by \*) | Conditional: empty if `WFTYPE` = `NONE` | `((PARENT(\+PARENT)*)(;PARENT(\+PARENT)*)*)?` </br></br> PARENT = `\*?LatvianLetter+` | `darīt`, `darbabiedrs;darbs+biedrene` |
| **WFCONSTR** | Syntactic construction or constructions underlying the given compound | Conditional: filled only if `WFTYPE` = `COMP` | `((WFCONSTR)(;WFCONSTR)*)?` | `"baltu ziedu";"baltiem ziediem"`, `"autora tehnika"` |
| **WFPHON**   | Comments on phonological aspects of the word-formation process  | Optional | Free form | `prombrauc`, `brūc` |
| **SOURCE**   | Lemma source identifier | Required | Value from the first column of `source_register.tsv` | `LVK2018` |

## Root Register Fields

| Field        | Description | Requirement | Format | Example |
|--------------|-------------|-------------|---------------|----------|
| **ROOTID**   | Root surface form identifier; forms with different ROOTIDs but the same SGID are allomorphs of one root (e.g., *bārd, bārž, bārzd, borž*) | Required | `R[0-9]+` | `R57` |
| **MORPHTYPE** | Morpheme type; for roots defined in subgroup specifications, the type is `ROOT_SG` | Required | `ROOT_SG` | `ROOT_SG` |
| **FORM** | Root surface form | Required | `LatvianLower+` | `ābec`, `ābeč`, `ābic` |
| **WFID**     | Word-family identifier; identifies the larger hierarchical word family to which a root belongs via its SG membership | Required | `WF[0-9]+` | `WF202`, `WF50` |
| **SGID**     | Subgroup identifier; identifies the subgroup to which a root surface form belongs. Multiple root surface forms may share the same SGID | Required | `SG[0-9]+` | `SG12`, `SG138`, `SG33` |
| **SG_LABEL** | Subgroup label listing all allomorphs of the root; no invariant form is assumed. | Required | `LatvianLower+(, LatvianLower+)*` | `ēk`, `dārg, dārdz` |
| **STRATUM** | Vocabulary stratum of the root; for known borrowings the value is `BORROWED`, otherwise the field is empty | Optional | `BORROWED` | `BORROWED` |
| **DEPTH** | Hierarchical depth of the subgroup within the word-family structure | Required | `[0-9]` | `0`, `1` |
| **WF_LABEL** | Word-family label; either `zero-element` (when all depth-0 subgroups are siblings that have a common ancestor) or the label of the single depth-0 subgroup. | Required | `(zero-element\|LatvianLower+(, LatvianLower+)*)` | `zero-element`, `dzintar, dzītar, zītar, dzintr` |
| **PROVENANCE** | Origin or justification of the root surface form, e.g., subgroup definition. | Required | `SG_ROOT` | `SG_ROOT` |
| **ROOTLEMMAS** | List of lemmas linked to this root surface form | Required | `L[0-9]+(;L[0-9]+)*` | `L62;L63;L64;L65;L66;L67;L68` |

## Affix Register Fields

The affix register is automatically generated from `lemma_register.tsv`. Affixes are extracted and listed by their type and position. No manual resolution of affix homonymy or homography has been applied. PREFIXES and WORDMEDIALS are deduplicated by `FORM+MORPHTYPE`, SUFFIXES are deduplicated by `FORM+MORPHTYPE+POSITION`

| Field        | Description | Requirement | Format | Example |
|--------------|-------------|-------------|---------------|----------|
| **MORPHID** | Affix identifier; `P` for PREFIX, `S` for SUFFIX, `M` for WORDMEDIAL | Required | `[PSM][0-9]+` | `P10`, `S1` |
| **MORPHTYPE** | The type of affix. The affix register distinguishes three types of affixes defined by their global position relative to the root(s): PREFIX, SUFFIX and WORDMEDIAL. Prefixes occur before the first root in a word; suffixes occur after the last root; wordmedials occur between two roots. | Required | `PREFIX\|SUFFIX\|WORDMEDIAL` | `PREFIX`|
| **FORM** | The surface form of the affix, as extracted from `SEGMENTATION` | Required | `LatvianLower+` | `aiz`, `šan`, `a` |
| **POSITION** | The specific attested position(s) of the affix in lemmas, within the bounds defined by `MORPHTYPE`. Positions are MORPHTYPE-specific. | Required | `POSITION(;POSITION)*` | `WORD_INITIAL;NON_INITIAL` |
| **PROVENANCE** | Origin or justification of the affix surface form. For affixes: morphemic segmentation of lemmas with a single lexical root (primary words, derivatives, etc.) or segmentation of lemmas with multiple lexical roots (compounds, hybrids, etc.). | Required | `(SINGLE_ROOT_SEG\|MULTI_ROOT_SEG)(;(?!\1)(SINGLE_ROOT_SEG\|MULTI_ROOT_SEG))?` | `SINGLE_ROOT_SEG;MULTI_ROOT_SEG)` |
| **AFFIXLEMMA** | List of LEMIDs where the affix occurs. | Required | `L[0-9]+(;L[0-9]+)*` | `L1450;L1459;L1460;L1461;L1462;L1473` |

`POSITION` is a categorical value describing a morpheme’s position within a segmented lemma. The set of possible values depends on `MORPHTYPE`.

**PREFIX positions**

* `WORD_INITIAL` — the first morpheme in a lemma. Example: `pār-` in `pār-ap-(dzī)-v-o-t-s`
* `NON_INITIAL` — any prefix after the first. Example: `ap-` in `pār-ap-(dzī)-v-o-t-s`

**SUFFIX positions**

* `AFTER_ROOT` — a suffix immediately following a root. Example: `-ī` in `(dar)-ī-tāj-s`
* `MIDDLE` — a suffix occurring between two other suffixes. Example: `-tāj` in `(dar)-ī-tāj-s`
* `WORD_FINAL` — the final affixal morpheme in a lemma. Example: `-s` in `(dar)-ī-tāj-s`

**WORDMEDIAL positions**

WORDMEDIALS occur only in multi-root lemmas, e.g., compounds, hybrid formations (`MULTI_ROOT_SEG`). Their position is relative to the two roots that define their MORPHTYPE:

* `ONLY` — the only wordmedial between two roots. Example: `a` in `(darb)-a-(laik)-s`
* `AFTER_ROOT` — the first wordmedial immediately following the preceding root, when there are ≥2 wordmedials between the two roots. Example: `āk` in `(zem)-āk-uz-(skait)-ī-t-s`
* `MIDDLE` — a wordmedial occurring between two other wordmedials. Example: `o` in `(dzī)-v-o-t-(grib)-a`
* `BEFORE_ROOT` — the last wordmedial immediately preceding the following root, when there are ≥2 wordmedials between the two roots. Example: `uz` in `(zem)-āk-uz-(skait)-ī-t-s`

## Source Register Fields

| Field        | Description | Requirement | Example |
|--------------|-------------|-------------|----------|
| **SOURCE**  | Unique identifier of the source | Required | `LVK2018`, `LVK2022`, `LatSenRom` |
| **FREQUENCY** | Raw count of lemmas from this source in the database | Required | `20`, `111` |
| **TYPE** | Source type (e.g., corpus, terminological database) | Required | `corpus`, `termbase` |
| **CITATION** | Full citation title of the source | Required | `The Balanced Corpus of Modern Latvian LVK2022` |
| **PATH** | Stable link to the source | Required | `http://hdl.handle.net/20.500.12574/125` |

## Inter-register Relations

- Lemmas link to root surface forms via **ROOTID**.
- Lemmas link to sources via **SOURCE**.
- Lemmas link to parents via **PARENTID**.
- Lemmas link to word families via **WFID**.
- Lemmas link to word-family subgroups (sets of root allomorphs) via **SGID**.
- Root surface forms link to allomorphs (other root surface forms in the same SG) via **SGID**.
- Root surface forms link to lemmas via **SGID**.
- Root surface forms link to word family structures via **WFID** and **DEPTH**

See `docs/data_model.md` for a conceptual relational diagram.
