---
name: ontology-generator-branch-is-unrelated
description: The origin/ontology_generator branch (projects/onto_generator, Edvard Lomo) is a throwaway test, not the Ontology Generator feature
metadata:
  type: project
---

The `origin/ontology_generator` branch in devops-monorepo (`projects/onto_generator/*.py`, last commit 2026-07-09 by Edvard Lomo, Pellet reasoner + owlready2 TTL compiler) is an unrelated one-off test. It is NOT the "Ontology Generator" (Curator/Ontologist loop over documentation) tracked in the ontology feature status list. Ignore it when investigating ontology work.

**Why:** The name is a false positive that makes it look like the feature has a POC in this repo when it does not.

**How to apply:** When asked where ontology features live, exclude that branch. As of 2026-08-24 the only real ontology work in devops-monorepo is the Ontology Extractor (`libs/ardoq_ai/ardoq_ai/{api,tools}/ontology/`, merged to main) plus unmerged base-ontology tools on `origin/ontology-base-tools`. See [[ontology-features-not-in-devops-monorepo]].
