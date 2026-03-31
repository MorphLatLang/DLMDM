``` mermaid
erDiagram

  %% ============================
  %% TSV TABLES (DATA)
  %% ============================

  LEMMA {
    string LEMID        "PK"
    string WFID         "list FK→WF_STRUCT.WFID (inherited via SG_STRUCT)"
    string SGID         "list FK→SG_STRUCT.SGID"
    string ROOTID       "list FK→ROOT.ROOTID"
    string CORLEMID
    string LEMMA
    string SEGMENTATION
    string POS          "FK→POS_LK.POS"
    string FEATS        "list FK→FEATS_LK.FEATS"
    string WFTYPE       "list FK→WFTYPE_LK.WFTYPE"
    string PARENTID     "encoded parent sets"
    string PARENTRES    "encoded resolved parents"
    string PARENTFORM   "literal parents"
    string WFCONSTR
    string WFPHON
    string SOURCE       "FK→SOURCE.SOURCE"
  }

  ROOT {
    string ROOTID       "PK"
    string SGID         "FK→SG_STRUCT.SGID"
    string WFID         "FK→WF_STRUCT.WFID (inherited via SG_STRUCT)"
    string MORPHTYPE    "FK→MORPHTYPE_LK.MORPHTYPE"
    string FORM
    string WF_LABEL
    string SG_LABEL
    int    DEPTH
    string STRATUM      "FK→STRATUM_LK.STRATUM"
    string PROVENANCE   "FK→PROVENANCE_LK.PROVENANCE"
    string ROOTLEMMAS   "list FK→LEMMA.LEMID"
  }

  SOURCE {
    string SOURCE       "PK"
    int    FREQUENCY
    string TYPE         "FK→SOURCE_TYPE_LK.TYPE"
    string CITATION
    string PATH
  }

  AFFIX {
    string MORPHID      "PK"
    string MORPHTYPE    "FK→MORPHTYPE_LK.MORPHTYPE"
    string FORM
    string POSITION     "list FK→POSITION_LK.POSITION"
    string PROVENANCE   "list FK→PROVENANCE_LK.PROVENANCE"
    string AFFIXLEMMA   "list FK→LEMMA.LEMID"
  }

  %% ============================
  %% STRUCTURAL TABLES
  %% ============================

  WF_STRUCT {
    string WFID         "PK"
    string WF_LABEL
  }

  SG_STRUCT {
    string SGID         "PK"
    string WFID         "FK→WF_STRUCT.WFID"
    string ParentSGID   "FK→SG_STRUCT.SGID (nullable if depth=0)"
    int    DEPTH
    string SG_LABEL
  }
  
  ParentSet_STRUCT {
    string ParentSetID   "PK (conceptual)"
    string LEMID         "FK→LEMMA.LEMID (child)"
    string WFTYPE        "FK→WFTYPE_LK.WFTYPE"
    int    NumSlots      "from WFTYPE_LK or LEMMA.PARENTID"
    string RawValue      "original encoded string from LEMMA.PARENTID"
  }
  
  Parent_STRUCT {
    string ParentID       "PK (conceptual)"
    string ParentSetID    "FK→ParentSet_STRUCT.ParentSetID"
    int    SlotNumber     "1..NumSlots"
    string ParentType     "FK→ParentType_LK.ParentType"
    string ParentLemmaID  "FK→LEMMA.LEMID (nullable) (parent)"
    string ParentLiteral  "nullable"
    bool   IsStarred
  }

  %% ============================
  %% LOOKUP TABLES
  %% ============================

  ParentType_LK {
    string ParentType   "PK"
  }

  POS_LK {
    string POS          "PK"
  }

  WFTYPE_LK {
    string WFTYPE       "PK"
    int    NumSlots
  }

  FEATS_LK {
    string FEATS        "PK"
  }

  STRATUM_LK {
    string STRATUM      "PK"
  }

  SOURCE_TYPE_LK {
    string TYPE         "PK"
  }

  MORPHTYPE_LK {
    string MORPHTYPE    "PK"
  }
  
  POSITION_LK {
    string PROVENANCE   "PK"
  }

  PROVENANCE_LK {
    string PROVENANCE   "PK"
  }

  %% ============================
  %% RELATIONSHIPS
  %% ============================

  %% WF → SG (1-to-many)
  WF_STRUCT ||--o{ SG_STRUCT : "has subgroups"

  %% SG → SG (self-referential hierarchy)
  SG_STRUCT ||--o{ SG_STRUCT : "parent subgroup"

  %% SG → ROOT (1-to-many)
  SG_STRUCT ||--o{ ROOT : "contains roots"

  %% SG → LEMMA (many-to-many via list)
  SG_STRUCT ||--o{ LEMMA : "lemma members"

  %% ROOT → LEMMA (via ROOTID list)
  ROOT }o--o{ LEMMA : "root membership"

  %% AFFIX → LEMMA (via LEMID list)
  AFFIX ||--o{ LEMMA : "affix occurrences"

  %% Parent structures
  LEMMA ||--o{ ParentSet_STRUCT : "child in"
  LEMMA ||--o{ Parent_STRUCT : "parent in"
  WFTYPE_LK ||--o{ ParentSet_STRUCT : "frame type"
  ParentSet_STRUCT ||--o{ Parent_STRUCT : "contains parents"
  ParentType_LK ||--o{ Parent_STRUCT : "parent type"

  %% Other FK relationships
  SOURCE ||--o{ LEMMA : "SOURCE"
  SOURCE_TYPE_LK ||--o{ SOURCE : "TYPE"

  POS_LK ||--o{ LEMMA : "POS"
  WFTYPE_LK ||--o{ LEMMA : "WFTYPE"
  FEATS_LK ||--o{ LEMMA  : "FEATS"

  MORPHTYPE_LK ||--o{ ROOT : "MORPHTYPE"
  PROVENANCE_LK ||--o{ ROOT : "PROVENANCE"
  STRATUM_LK ||--o{ ROOT : "STRATUM"

  MORPHTYPE_LK ||--o{ AFFIX : "MORPHTYPE"
  POSITION_LK ||--o{ AFFIX: "POSITION"
  PROVENANCE_LK ||--o{ AFFIX : "PROVENANCE"
```
<sub>
<b>Notation:</b><br>
<b>TSV tables</b> — real data tables exactly as provided in the `.tsv` files.<br> 
<b>\*_LK tables</b> — lookup tables representing controlled vocabularies used in the `.tsv` files (not provided as separate tables, but derivable and documented in `README_fields.md` and `OVERVIEW.md`).<br>   
<b>\*_STRUCT tables</b> — conceptual/structural entities representing relationships encoded implicitly in the `.tsv` files (reconstructable from the data).<br> 
</sub>
