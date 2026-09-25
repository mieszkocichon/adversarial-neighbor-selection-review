# Review Protocol (version 1.2)

**Working title:** *Adversarial Neighbor Selection in Gossip-Based Systems: From Secure Peer Sampling to Eclipse Attacks on Blockchains and Topology Attacks on Decentralized Learning*

**Author:** Mieszko Cichoń
**Version 1.0:** 2026-09-24 (morning)
**Version 1.1 (amendment):** 2026-09-24, **before** the author's screening. Changes are described in Section 12 and in the deviation log.
**Version 1.2 (amendment):** 2026-09-24, **before** publication of the protocol and before the author's screening. Changes are described in Section 13.

**Language note:** this is the English edition of version 1.2, prepared before publication. It contains no substantive changes relative to the Polish working version; criteria, numbers, hashes and the random seed are identical. This English edition is the authoritative published version.

**Disclosure of prior data exposure:** this protocol was not written "blind". Before version 1.0 the author knew the results of the symmetric test and of the query on membership security (Section 1). Before version 1.1 a citation-analysis pilot was run on 8 papers from the D1–D3 set (Section 1). Both facts must be reported in the methodology of the article.

Any change after this date is recorded in the "Deviation log" at the end of this document, with date and justification. Recommended: publish this file before screening, e.g. on OSF or in a Git repository with a commit date, so that reviewers can verify that the criteria were fixed before the results.

---

## 1. Rationale (pilot test results, 2026-09-23/24)

- **Symmetric test of the share of attack-related papers** (identical security clause for all three domains, topically relevant records, 2021–2026):
  - distributed systems: 22% [15–31%];
  - blockchain/P2P: 37% [28–47%];
  - decentralized learning: 32% [23–43%].
- **A search on membership and peer-sampling security without a time window** revealed a mature literature from 2004–2013: secure group membership, eclipse attacks on overlays, secure peer sampling (including Brahms). After 2021 the topic shows only a limited revival.
- **Conclusion:** the claim that "the membership layer is unexplored" was rejected.
- **Citation-analysis pilot (before version 1.1),** text matching in the References field, hence coarse:
  - "Eclipse attacks on Bitcoin's peer-to-peer network" (Heilman et al., USENIX Security 2015) cites the work on eclipse attacks on overlays, Brahms and "Secure peer sampling". The work on routing-aware peering (2021) cites the work on eclipse attacks on overlays.
  - Four key D3 papers on topology attacks (adversarial node placement, DOCAttack, disrupting cross-community information flow, topology inference attack) cite none of these works.
- **Consequence:** the original hypothesis of version 1.0 ("independent discovery in three communities") is **false for D2** and was replaced by a hypothesis of asymmetric knowledge transfer (RQ4 in Section 3).

## 2. Definitions

- **Neighbor selection:** the mechanism that determines which nodes a given node exchanges information with. It includes:
  - the membership service;
  - peer sampling;
  - overlay maintenance;
  - peer selection in blockchain networks;
  - construction and modification of the communication graph in decentralized learning.
- **Attack on neighbor selection:** adversarial action whose goal or effect is to distort a node's neighborhood or the graph structure, e.g. isolating a node, over-representing adversarial nodes, or partitioning the network.
- **Domains:**
  - **D1:** distributed systems and general-purpose P2P overlays;
  - **D2:** blockchain and cryptocurrency networks;
  - **D3:** decentralized learning (gossip learning, decentralized SGD, serverless DFL).

### Unified attack classification (applies to all domains)

| Code | Class | Definition |
|---|---|---|
| A1 | Eclipse / isolation | The adversary controls all or nearly all connections of the victim. |
| A2 | Sybil / infiltration | The adversary introduces many identities to increase its share in views and neighborhoods. |
| A3 | View poisoning | The adversary distorts exchanged peer lists (e.g. hub attacks, promoting its own nodes). |
| A4a | Partitioning – overlay level | The adversary disconnects the graph by manipulating connections between peers (e.g. selective connection dropping, forcing restarts). |
| A4b | Partitioning – infrastructure level | The adversary disconnects the graph by hijacking network paths (e.g. BGP). It does not manipulate neighbor selection, only access to neighbors. |
| A5a | Influence on topology in learning | The adversary **changes** the communication graph or the victims' neighborhoods (e.g. joining at chosen positions, impersonating a desirable neighbor). |
| A5b | Exploitation of position in learning | The graph is given; the adversary **exploits** its position (centrality, bridges between communities) to amplify an attack. |
| A6 | Topology inference | The adversary infers the graph structure or a node's neighborhood as an attack step or as a privacy violation. |

A paper may belong to more than one class. Unlike the previous approach, a single label is not enforced, because enforcing one produced the artifact of "disjoint classes".

**Threat-model distinction [v1.1]:** each paper is additionally labeled **W** (the adversary influences neighbor selection: A1, A2, A3, A4a, A5a) or **P** (the adversary exploits an existing topology or infrastructure: A4b, A5b, A6). Comparing the share of W and P across domains is part of RQ4.

### Scope boundaries [v1.1]

| Case | Decision | Justification |
|---|---|---|
| Byzantine-robust aggregation in DFL without a topology dimension | Exclude (X1) | Does not concern neighbor selection. |
| Aggregation that **cuts off or permanently excludes** neighbors (soft selection via near-zero weights, rewiring) | Include, label "soft selection" | Actually changes the effective neighborhood. |
| BGP and routing attacks on P2P networks | Include as A4b | Relevant to P2P connectivity security, but a distinct threat model (P). |
| Sybil at the identity layer (PoS, airdrops, on-chain addresses, reputation) | Exclude (X1) | Does not concern network neighborhoods. |
| Reputation-based peer selection | Include if it concerns choosing neighbors in an overlay or communication graph; exclude (X1) if it concerns choosing a service provider or file source | The former defends neighbor selection, the latter does not. |
| Relay selection in Tor and anonymity networks | Exclude (X1) | Different domain and threat model (path selection, not neighbors in a gossip/P2P overlay). |
| Payment channels (routing in PCNs) | Exclude (X1) | Payment routing, not neighbor selection for dissemination. |
| ML-based detection of eclipse or Sybil attacks in blockchain networks | Include | A defense of neighbor selection (detection). |

## 3. Research questions

- **RQ1:** Which neighbor-selection mechanisms are used in D1–D3, and under which assumptions (churn, network size, synchrony)?
- **RQ2:** Which attacks on neighbor selection have been described in D1–D3? Which adversary capabilities are assumed (share of nodes or identities, network position, resources)?
- **RQ3:** Which defenses have been proposed (randomization, trust and reputation, TEEs, resource testing, latency and geographic diversity, verifiable randomness, detection)? With which guarantees (formal or empirical) and which evaluation?
- **RQ4 (knowledge transfer) [v1.1]:** Whether and to what extent have D2 and D3 adopted knowledge from D1 about attacks on neighbor selection and defenses against them, measured by citations and by the content of proposed defenses?
  - **H1:** D2 adopted knowledge from D1 (citations starting from the foundational work on eclipse attacks on Bitcoin).
  - **H2:** D3 did not adopt this knowledge: the share of D3 papers citing D1 is significantly lower than in D2, also after accounting for the reference model (Section 8).
  - **H3 (exploratory):** P-type threat models (given graph) dominate in D3, whereas W-type dominates in D1 and D2.
- **RQ5:** Which open problems follow from RQ1–RQ4?

## 4. Sources and queries

**Primary database:** Scopus (TITLE-ABS-KEY, articles and conference papers, English, **no lower year bound**, up to the date of execution).

**Supplements:**
- one round of backward and forward snowballing on the included set;
- DBLP for leading security and distributed-systems conferences, if snowballing reveals gaps in Scopus coverage.

**D1:**
```
TITLE-ABS-KEY(("peer sampling" OR "membership protocol*" OR "membership service" OR "membership management" OR "overlay maintenance" OR "gossip-based membership" OR "random membership" OR "neighbor selection" OR "peer selection") AND (byzantine OR sybil OR eclipse OR adversar* OR malicious OR attack* OR poisoning)) AND (LIMIT-TO(DOCTYPE,"ar") OR LIMIT-TO(DOCTYPE,"cp")) AND LIMIT-TO(LANGUAGE,"English")
```

**D2:**
```
TITLE-ABS-KEY((blockchain OR bitcoin OR ethereum OR cryptocurrenc* OR libp2p) AND ("eclipse attack*" OR "sybil attack*" OR "peer selection" OR "neighbor selection" OR "network partition*" OR "partitioning attack*" OR "routing attack*" OR "topology inference")) AND (LIMIT-TO(DOCTYPE,"ar") OR LIMIT-TO(DOCTYPE,"cp")) AND LIMIT-TO(LANGUAGE,"English")
```

**D3:**
```
TITLE-ABS-KEY(("decentralized learning" OR "decentralized federated learning" OR "gossip learning" OR "peer-to-peer learning" OR "decentralized SGD" OR "decentralized optimization") AND (topolog* OR "communication graph" OR neighbor* OR "peer selection" OR "peer sampling") AND (byzantine OR sybil OR eclipse OR adversar* OR malicious OR attack* OR poisoning)) AND (LIMIT-TO(DOCTYPE,"ar") OR LIMIT-TO(DOCTYPE,"cp")) AND LIMIT-TO(LANGUAGE,"English")
```

**Export:** CSV with fields: authors, title, year, source, DOI, abstract, keywords and **References** (required for RQ4).

## 5. Eligibility criteria (fixed in advance)

**Inclusion (all conditions):**
- **I1:** The main contribution concerns an attack on neighbor selection or a defense of neighbor selection (classes A1–A6) in a P2P, gossip, blockchain or decentralized-learning system.
- **I2:** Peer-reviewed work (journal or conference).
- **I3:** English language.

**Exclusion** (boundary cases are resolved by the "Scope boundaries" table in Section 2):
- **X1:** The attack or defense does not concern neighbor selection, e.g. pure model poisoning without a topology dimension, or an attack at the consensus or mining level.
- **X2:** Architecture with a central server (classical FL) or without P2P exchange.
- **X3:** Sybil attacks and isolation at the radio, MAC or routing layer of sensor networks, MANETs and VANETs, unless they concern a P2P overlay.
- **X4:** Non-research material: posters, demos, extended abstracts shorter than 4 pages.
- **X5:** Duplicate or earlier version of the same work. The most complete version is kept, usually the journal version.

**No limit on the number of papers.** No "within-budget" selection is applied after seeing the results.

**Quality:** venue ranking is **not an exclusion criterion**. Each included paper receives a quality assessment according to a checklist:
- explicit adversary model;
- formal guarantees or an evaluation with a described environment;
- evaluation scale;
- code availability.

SJR/ICORE rankings are reported only in a **sensitivity analysis**, i.e. we check whether conclusions change when the set is restricted to Q1–Q2 and A*/A/B.

## 6. Screening procedure

1. **Automatic deduplication** by EID. Records with different EIDs but the same normalized title or DOI are flagged as possible duplicates and resolved by the reviewer (X5).
2. **Stage 1, title and abstract:**
   - **The author is the primary reviewer** and assesses all records.
   - AI (Claude) assesses independently as the second reviewer, without access to the author's decisions. **[v1.1, clarified in v1.2]** Each record receives an individual assessment without keyword rules. Key v2.1 was based on the title and the **opening part of the abstract (approx. 220–480 characters)**; this limitation is reported in the methodology, and its effects are mitigated by the joint-exclusion check (item 7). The AI key is produced before the author's screening and is secured by a SHA-256 hash published with an independent timestamp (OSF or a public commit).
   - Agreement is measured with Cohen's kappa.
   - Disagreements are resolved by the author on the basis of the full text.
3. **Stage 2, full text:** author only. The decision and exclusion code are recorded for each paper.
4. **Snowballing** with the same criteria.
5. **All decisions and classifications are keyed by EID** (not by row number) and have no default values: an unassigned field stays empty. Implementation and self-tests: `narzedzia_v2/screening_lib.py`. All numbers in the text and tables are generated from the data.
6. **All decisions are recorded in the spreadsheet.** The article publishes a flow diagram and a list of exclusions with codes.
7. **Joint-exclusion check [v1.2].** After stage 1, a sample is drawn from the records excluded by **both** reviewers, of size max(10% rounded up; 73), but not larger than the total number of joint exclusions (drawn from the sorted list of EIDs, seed `20260924`, `random.Random(seed).sample`). The minimum of 73 is the smallest sample for which zero errors yields an upper bound of the 95% Wilson interval below 5%; with a smaller sample the decision rule would force a full review regardless of the outcome. The author re-assesses these records on the basis of the **full** abstract.
   - We report the share of records that should be included after re-assessment, with a 95% Wilson confidence interval.
   - **Decision rule fixed in advance:** if the upper bound of the interval exceeds 5%, the author reviews **all** joint exclusions.
   - Records included as a result of the check proceed to stage 2 and are shown separately in the flow diagram.
   - Implementation: `narzedzia_v2/po_screeningu.py`.

**AI disclosure:** the methodology and acknowledgments describe the role of AI (second reviewer in stage 1, support in extraction and editing). All claims about the content of papers are verified by the author on the full texts. The current policy of the chosen publisher must be checked before submission.

## 7. Data extraction form

For each included paper:
- domain (D1, D2, D3);
- neighbor-selection mechanism;
- **[v1.2] attacked or defended element of the common abstraction:** view, sampling, peer-information exchange, eviction from the view, bootstrapping, communication graph (multiple choice);
- **[v1.2] adversary goal:** isolation, sample distortion, partitioning, topology inference, amplification of another attack (multiple choice);
- attack classes (A1–A6, multiple choice);
- adversary capabilities: share of nodes or identities, network position, resources;
- defense type;
- guarantees (formal, empirical, none);
- evaluation environment: theory, simulation, testbed, production network;
- scale: number of nodes;
- code availability;
- quality assessment according to the checklist in Section 5;
- **[v1.2] counterpart of the defense in another domain** (EID of the paper or "none"), filled in during the qualitative analysis for RQ4;
- **[v1.2] location in the full text** (section/page) for each recorded claim about the content of the paper;
- references, used for RQ4.

## 8. Knowledge-flow analysis (RQ4)

- **Cross-domain citation:** a paper from domain X cites at least one included paper from domain Y.
- **Measures:**
  - share of papers with at least one cross-domain citation, separately for each direction (e.g. D2→D1, D3→D1);
  - share of papers in D2 and D3 that cite at least one D1 paper concerning a defense;
  - time lag in years between the first description of an attack class in one domain and its first description in another.
- **Hypothesis (exploratory, recorded before the analysis):** fewer than half of the papers in D2 and D3 cite any D1 work on secure membership or peer sampling.
- **Limitation:** absence of a citation does not prove absence of knowledge. Results are interpreted as a measure of visible knowledge flow, not of actual knowledge.
- **Reference model [v1.1]:** for each domain we also compute the share of papers citing *other* older systems literature (classical gossip and peer sampling without a security aspect, e.g. works on epidemic algorithms, the peer sampling service, SWIM). This distinguishes "they do not cite D1 defenses" from "they do not cite older systems literature at all".
- **Matching [v1.1]:** by DOI, or, if missing, by normalized title; manual check of a random 10% sample of matches.
- **Qualitative analysis [v1.1]:** for D3 papers with a defense, we check whether the defense mechanism re-creates ideas from D1 (view randomization and refreshing, limiting the influence of a single source, neighbor diversity, resource testing) despite the absence of a citation.
- **Test:** difference in shares D2 vs D3 – Fisher's exact test; confidence intervals are reported because the D3 sample will be small.

## 9. Control set

Papers that the search **must** find. They are used to check query sensitivity; if any is missing, the query must be corrected and the change recorded in the deviation log.

- "Brahms: Byzantine resilient random membership sampling" (PODC 2008 and Computer Networks 2009)
- "Eclipse attacks on overlay networks: Threats and defenses" (IEEE INFOCOM 2006)
- "Secure peer sampling" (Computer Networks 2010)
- "RAPTEE" (ICDCS 2022), "SecureCyclon" (ICDCS 2023), "Basalt" (Middleware 2023), "Honeybee" (MobiHoc 2025)
- "Eclipse Attacks on Ethereum's Peer-to-Peer Network" (WWW 2026)
- "Routing-aware peering" against network-eclipse attacks (USENIX Security 2021)
- papers on topology attacks in decentralized learning from the SEC screening (e.g. "Adversarial node placement", "User isolation poisoning")
  - **[v1.1]** "User isolation poisoning" remains in the control set and is reported as **missing** (sensitivity 11/12); its relevance will be decided in stage 2 or by snowballing.
- "Eclipse attacks on Bitcoin's peer-to-peer network" (Heilman, Kendler, Zohar, Goldberg; USENIX Security 2015) – metadata confirmed in Scopus.

**Note on related work [v1.1]:** according to its abstract, the survey by Martínez Beltrán et al. (IEEE COMST 2023, DOI 10.1109/COMST.2023.3315746) analyzes, among other things, topologies and security approaches in DFL. Before writing, the full text must be checked for coverage of attacks on neighbor selection or topology – a potential threat to the novelty of RQ4/H2.

## 10. Effort estimate (one person, part-time)

| Stage | Time |
|---|---|
| Search, deduplication, stage-1 screening | 1–2 weeks |
| Full texts and extraction (estimated 60–120 papers) | 6–10 weeks |
| Citation analysis (RQ4) | 1–2 weeks |
| Writing | 6–8 weeks |

## 11. Deviation log

| Date | Change | Justification |
|---|---|---|
| 2026-09-24 | D1–D3 executed: D1 = 219, D2 = 697, D3 = 146 records (1062 rows in total); after deduplication by EID, 1034 records. **[correction]** 27 records appeared in more than one query (one in three), hence 28 removed rows. In addition, 8 records form 4 pairs with identical titles and different EIDs – possible duplicates in Scopus, resolved as X5. | Record of search results (not a deviation). |
| 2026-09-24 | Control set: 11 of 12 items found. Missing: "User Isolation Poisoning on Decentralized Federated Learning". Queries were **not** changed. | The paper contains no terms related to topology or neighborhood. The abstract indicates that the attack manipulates model updates rather than the neighbor set, so listing it in the control set was probably a mistake. Final assessment: stage 2 or snowballing. |
| 2026-09-24 | AI screening (second reviewer) performed **before** the author's screening. The key file was secured with SHA-256 hash: `c64b6ab40ca836f886fdbdb89c220c0e0e7423b2ff322793fbbbb839aff4df6e`. | Proof of independence: the author can verify that the key did not change after his assessments (`sha256sum klucz_AI_etap1.csv`). |
| 2026-09-24 | **Correction of the control-set entry:** "User isolation poisoning" was restored to the control set as **missing** (sensitivity 11/12). The previous justification was withdrawn. | Removing an item after seeing the result would look like fitting the criterion to the result. |
| 2026-09-24 | **AI key v1 withdrawn** (hash `c64b6ab4…4df6e`). The author had not opened it and had not started screening. It will be replaced by key v2 with an individual assessment of every record. | Key v1 was partly based on keyword rules, so kappa would measure agreement with a rule rather than with a reviewer. |
| 2026-09-24 | **Amendment v1.1:** new wording of RQ4 (H1–H3), split of A4 and A5, W/P labels, scope boundaries, reference model and citation-matching procedure. | The citation pilot showed that the v1.0 hypothesis is false for D2. Changes made before the author's screening. |
| 2026-09-24 | Disclosure: citation pilot on 8 papers before v1.1. | Influenced the wording of RQ4; to be reported in the methodology. |
| 2026-09-24 | Audit of the code and article of stage 1: decisions keyed by EID, no default classification values, numbers generated from data; EID column in the screening spreadsheet; corrected author names in the bibliography. | A result produced by a default value in code (RQ1 of stage 1) and decisions dependent on row order were detected (ERRATA.md, items 1–2). |
| 2026-09-24 | **AI key v2 completed:** 1034 records, each assessed individually on the basis of title and abstract (10 batches), decisions keyed by EID and validated with `screening_lib.py`. File `klucz_AI_v2.csv` (sorted by EID), SHA-256: `6b770171ad49b35d14ef2a443110e375ad1febbb8e17f1c7412dc2018f14de34`. | Commitment before the author's screening; hash to be published with an independent timestamp. |
| 2026-09-24 | **Correction of the description of AI key v2:** decisions were made on the basis of the title and the **opening part of the abstract (approx. 220–480 characters)**, not the full abstract (median abstract length: approx. 1400 characters). The previous entry ("on the basis of title and abstract") was imprecise. | Audit; limitation reported in the methodology. The "when in doubt, include" rule partly mitigates it; its impact will be assessed by kappa and the review of disagreements. |
| 2026-09-24 | **AI key v2 replaced by key v2.1** (`klucz_AI_v2_1.csv`, SHA-256: `02ba138ff84387732d847c91648ad0da5082b7f7ffe63a563d58ac9ebd0b8072`). Decisions and codes identical to v2; only 14 notes were changed, in which references to position numbers were replaced by EIDs. Key v2 (`6b770171…4de34`) archived. | References to position numbers were not reproducible (the same error as in stage 1). Change made before publication of the protocol and before the author's screening. |
| 2026-09-24 | Author's screening spreadsheet: full abstracts restored in 3 records (previously truncated to 3000 characters) and full author lists; column N added with possible duplicates (same title, different EIDs); rule for reputation-based peer selection clarified. | Audit. The author had not started screening. |
| 2026-09-24 | Library `screening_lib.py`: blocking of silent decision overwrites, validation of a code on inclusion, reporting of missing values in kappa, duplicate detection by title/DOI, portable temporary files in the self-test. | Code audit. |
| 2026-09-24 | **Amendment v1.2** (before publication of the protocol and the author's screening): joint-exclusion check with a decision rule; three new extraction fields; clarification of deduplication and of the AI-key description in Section 6. | Remaining risk after the audit: an AI key based on abstract fragments may contribute to undetected joint false exclusions. Extraction fields needed for the common abstraction and RQ4. Changes do not affect the eligibility criteria. |

## 12. Changes in version 1.1 (summary)

1. RQ4: the hypothesis of "independent discovery in three domains" replaced by a hypothesis of asymmetric knowledge transfer (H1: D2 adopted knowledge from D1; H2: D3 did not).
2. Attack classes: A4 → A4a/A4b, A5 → A5a/A5b, W/P threat-model label.
3. Scope-boundaries table (aggregation, BGP, identity-level Sybil, Tor, PCNs, ML detection).
4. Citation analysis: reference model, matching by DOI/title, qualitative analysis, Fisher's exact test.
5. Control set: a missing item is reported as missing (11/12).
6. AI screening: individual assessment of records, key v2, publication of the hash with an independent timestamp.
7. Explicit disclosure of data exposure before v1.0 and v1.1.

## 13. Changes in version 1.2 (summary)

1. Joint-exclusion check: sample max(10%, 73), seed 20260924, Wilson interval, full review if the upper bound exceeds 5% (Section 6, item 7).
2. New extraction fields: element of the common abstraction, adversary goal, counterpart of the defense in another domain, location in the full text (Section 7).
3. Clarification of deduplication (EID + flagging duplicates by title/DOI) and of the basis of AI-key decisions (Section 6).
4. Eligibility criteria **unchanged** relative to v1.1.
