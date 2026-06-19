# 🧠 StressCheck Ontology

**StressCheck** is an ontology and knowledge graph designed to support journalistic fact-checking of scientific claims regarding stress-reduction treatments.

The project goes beyond simple data modeling (both journalistic and medical); it leverages **Automated Reasoning** to independently deduce the reliability of a news claim by comparing it against available scientific evidence, thereby minimizing human bias.

**Website Project:** https://finalprojectkrae.netlify.app/

---

## 🎯 Project Goal
This repository contains the conceptual and logical foundation of the StressCheck framework. The core objective is to link a **NewsClaim** (extracted from a news article) to the **ScientificStudy** that either supports or refutes it. The system then automatically generates a **Verdict** (Supported, Exaggerated, Unsupported, or Unclear) based on the rigor and context of the scientific evidence.

---

## 🏛️ Ontology Architecture

The ontology is written in Turtle (`.ttl`) format and maps three primary domains:
1. **Journalistic Domain:** `NewsArticle`, `NewsClaim`, `ClaimReview` (Schema.org compatible).
2. **Medical/Scientific Domain:** `ScientificStudy`, `Treatment`, `Condition` (e.g., Stress), `Effect`, `Population`, `EvidenceType`.
3. **Provenance Domain:** Source tracking via integration with the standard **PROV-O** vocabulary (`prov:used`, `prov:Activity`).

---

## ⚙️ Automated Reasoning

The true engine of StressCheck is its capacity to infer new knowledge. Three levels of formal OWL logic have been implemented:

### 1. SWRL Rules for Automatic Verdicts
The ontology utilizes SWRL rules to automatically assign a verdict to a journalistic review (`sc:hasVerdict`) by cross-referencing the **evidence type** and the **population** analyzed in the connected scientific study.

* **Supported:** If the claim is based on a Clinical Trial (`sc:ClinicalTrial`).
* **Exaggerated:** If a claim meant for humans is based on a preclinical animal study (`sc:PreclinicalStudy` + `sc:Animals`).
* **Unsupported:** If the claim is based solely on an observational study conducted on adults (`sc:ObservationalStudy` + `sc:Adults`).
* **Unclear:** If the claim is based on an observational study on a limited or alternative sample, such as students (`sc:ObservationalStudy` + `sc:Students`).

### 2. Property Chains (*SuperProperty Of*)
To optimize the graph structure and minimize manual data entry, two logical property chains have been defined:
* `sc:evaluatesTreatment`: Automatically inferred by linking the review to the claim, and the claim to the treatment (`sc:reviewsClaim o sc:mentionsTreatment`).
* `sc:usedEvidence`: Automatically inferred by tracing from the review to the claim, and from the claim to the scientific study (`sc:reviewsClaim o sc:supportedByStudy`).

### 3. Property Hierarchy and PROV-O
The local property `sc:usedEvidence` is declared as an `rdfs:subPropertyOf` of `prov:used`. Consequently, whenever the system deduces which study was used as evidence, it automatically inherits the W3C standard provenance markup.

---

## 🚀 How to Test in Protégé

To test StressCheck's reasoning capabilities locally:

1. Download the `stresscheck.ttl` file and open it in **Protégé** (version 5.x or higher).
2. From the top menu, ensure **Pellet** (or Openllet) is selected as the active Reasoner. *(Note: HermiT may not process the SWRL rules correctly).*
3. Navigate to the *Individuals by class* tab and examine the `ClaimReview` individuals (e.g., `ClaimReview1`).
4. **Important:** Notice that in the *Object property assertions* panel, there are no manually assigned verdicts.
5. Click on **Reasoner > Start reasoner**.
6. Return to the `ClaimReview` individuals: you will now see the properties `sc:hasVerdict`, `sc:usedEvidence`, `sc:evaluatesTreatment`, and `prov:used` appear highlighted with a **yellow background**. This visual cue indicates that the reasoner has successfully calculated and applied the logical inferences.

---

## 🔮 Future Developments
This knowledge base represents the "logical engine" of the project. Planned future development steps include:
* **LLM Integration (Generative AI):** Developing a Python script where a Large Language Model analyzes the journalistic article text, extracts key entities (Treatment, Population, Study Type), and dynamically populates the ontology to trigger the SWRL reasoning.
* **Editorial Web Interface:** Exporting the inferred graph data via **JSON-LD** to feed an interactive dashboard built with modern web components. This will provide journalists with immediate, scannable visual feedback (e.g., semantic cards) on the analyzed news.

---
*Project created by the DHDK group.*
