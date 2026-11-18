Generator issues (SHACL rejects data that LinkML accepts)

sh:class ignores subclasses. Some classes share a class_uri (Entity, Activity, AgenticEntity). For their ranges the shapes use sh:class <parent class_uri>. Subclasses with their own class_uri, such as ChemicalReaction, Reactor, DissolvingSubstance and SubstanceSample, fail that check. This explains all 3 SubstanceSampleCharacterization violations and 14 in ReactionMonitoring.
realized_plan false positive. Slots that share prov:used are merged. The “max 1 plan” count then treats every prov:used value as a plan, because the Plan shape has no required properties. That’s the AnalysisDataset qualifiedMaxCount error.
Specialised shapes are never applied. ReactionMonitoringDataset, ReactionMonitoring, AnalysisDataset and the other specialised shapes have no target. Only the generic Dataset/Activity shapes get checked, so “conforms” says nothing about the chemistry constraints.
Recursive shapes. With pyshacl’s default depth of 15, the AnalysisDataset run aborts with “Validation path too deep”.

Data errors (in tests/data/valid/*.yaml)
5. Same IRI with two different titles:

pubchem/71583 (AnalysisDataset)
step-1_Solution-1, step-2_Output-1, step-3_ReactionMixture-1 and step-4_ReactionMixture-2 (ReactionMonitoring)
Same IRI with two rdf_type values: step-4_Output-1 is both CHEBI:59999 and CHEBI:60004.
Typo nothing catches: the step-2 activity has the titles “Step 1: …” and “Step 2: …”.

Data and schema disagree
8. Solvents in two roles. Solvent-1, 3, 4, 5 and 6 are agents (used_solvent) and also input entities or solution parts. In RDF this gives one node with conflicting types, which no shape accepts. Solvent-2 is missing its rdf_type, which is also inconsistent.

Hidden schema issue
9. Chemical parts of materials. StartingMaterial and Reagent have ChemicalEntity parts, but MaterialEntity expects MaterialEntity parts. This only shows up once subclasses are taken into account; I haven’t traced it fully.

Most of the remaining ReactionMonitoring violations are knock-on effects of 5, 6 and 8.