# Paper supplementary materials

These documents give the appendix material of the AskGLP1 paper in readable form. They follow the full version of the paper, and their table numbers match its appendix. The shorter conference submission points here for the material it leaves out. The research code, the nine prompt files and the 120-question set are the versions used in the paper.

| Material | Readable document | Machine-readable files |
| --- | --- | --- |
| Full paper prompt descriptions | [Prompts](prompts.md) and [frozen prompt index](../prompts/README.md) | Existing prompt files |
| Extraction fields, profile rules and grounding checks | [Extraction schema](extraction-schema.md) | [Reddit schema](schema-reddit.json), [WebMD schema](schema-webmd.json) |
| Complete validation, error diagnostics and rating sensitivity | [Validation details](validation-details.md) | [Reference correction](reference-correction.json) |
| Extended ingredient, brand and year comparisons (Tables 5 to 8) | [RQ1 extended results](rq1-extended.md) | [Ingredient comparisons](ingredient-comparisons.tsv), [Year subsets](year-subsets.tsv) |
| Collection, cleaning, length checks, FAERS processing and coding diagnostics | [Reproducibility](reproducibility.md) | [16 subreddits, Table 11](subreddits.tsv) |
| Corrected text-only WebMD purpose reference | [Reference correction](reference-correction.md) | [Aggregate audit](reference-correction.json) |
| Complete 120 evaluation questions | [Question list and field definitions](../evaluation/questions/README.md) | [TSV](../evaluation/questions/questions.tsv) |

The paper holds the core methods, primary results, scoring rules and ethics statement, and these documents do not replace its evidence. Annual/subset Wilson intervals remain pointwise; the reported p values use their stated Bonferroni families. Supplemental semaglutide alopecia results are not adjusted for baseline selection. Stopping/switching markers remain unvalidated automatic output. Length checks within retained Reddit posts do not validate excluded short posts.

The repository does not distribute source user posts/reviews, account identifiers, model credentials or licensed MedDRA vocabulary files. The synthetic offline check described in the root README does not reproduce the paper’s empirical results.
