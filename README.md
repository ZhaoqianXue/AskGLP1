# AskGLP1

AskGLP1 turns online patient narratives about GLP-1 medications into a knowledge graph (KG) and answers questions about it with source-linked evidence. It screens Reddit posts for medication experiences, extracts drug–purpose and drug–side-effect relations from them and from WebMD reviews, maps the relations to MedDRA, aligns them with FAERS reports, and lets people query the result through a KG-grounded agent that cites the posts behind every number.

Paper: *AskGLP1: A Knowledge Graph LLM Agent for Exploring GLP-1 Experience from Online Patient Narratives*. Zhaoqian Xue, Anay Somani, Siddhartha Singh, Junting Mu, Zhongyin Wang, Haozheng Liu, Ayesha Khatun, Yeaeun Kwon, Renkai Ma, Siyuan Ma, Zhe He, Danxia Yu, and Lingyao Li. Corresponding authors: Zhaoqian Xue (zhaoqianxue@arizona.edu) and Lingyao Li (lingyaoli@arizona.edu).

## How AskGLP1 Works

AskGLP1 covers four ingredients and eight brands: semaglutide (Ozempic, Wegovy, Rybelsus), tirzepatide (Mounjaro, Zepbound), liraglutide (Victoza, Saxenda) and dulaglutide (Trulicity).

1. **Data collection.** Reddit submissions and comments from 16 GLP-1 and type 2 diabetes subreddits, WebMD drug reviews, and FAERS reports from openFDA. Rule-based cleaning and a content-word threshold reduce 8,496,004 Reddit posts to 1,030,309.
2. **Medication experience detection.** A language-model classifier (Jev) keeps Reddit posts that report an identifiable drug, a personal experience and clinical detail.
3. **Relation extraction.** GPT-6 Luna extracts drugs, purposes, side effects, side-effect details and user demographics under a strict output schema. Every relation cites the sentences it comes from, and code checks that the cited sentences contain its wording.
4. **Entity normalization.** Drug names map to the ingredient and brand catalog. Purpose and side-effect wordings map to MedDRA lowest level terms (LLTs) through BioLORD-2023 retrieval of 10 candidates and LLM selection, and then to preferred terms (PTs).
5. **KG construction.** Relations from both platforms share drug and MedDRA nodes. Each FAERS report links to the same drug nodes and to its reported PTs. The KG built for the paper holds 385,790 relations from 163,135 Reddit posts and 2,506 WebMD reviews.
6. **KG-grounded agent.** The agent answers natural-language questions from the KG and the FAERS tables, and marks each answer as answered, limited or declined.

## The KG-Grounded Agent

The agent (`src/weightloss/agent`) uses five tools:

| Tool | Purpose |
| --- | --- |
| `link_terms` | Find the MedDRA PTs and groups that match a condition, goal or symptom |
| `query_graph` | Count posts by drug, platform, age group, sex/gender and concept |
| `get_evidence` | Return example relations with their source sentences |
| `compare_faers` | Compare the same concepts and drugs with FAERS reports |
| `run_python` | Run read-only SQL and pandas over the analysis tables in a sandbox |

Answers follow fixed reporting rules. A concept counts once per post, Reddit and WebMD are reported separately, and a difference is reported only when 95% Wilson intervals do not overlap. Every number points to a tool output. Before an answer is shown, code checks its numbers, quotes and comparisons against the tool outputs and source sentences. The agent gives no medical advice and reports shares of posts, not incidence or risk.

## Repository Contents

| Material | Location |
| --- | --- |
| Python pipeline, KG tools, agent and web API | [src/weightloss](src/weightloss) |
| Website frontend | [apps/web](apps/web/README.md) |
| Prompts for screening, extraction, term selection, the agent, answer checking and the baselines | [prompts](prompts/README.md) |
| The 120 evaluation questions, with a readable list and TSV | [evaluation/questions](evaluation/questions/README.md) |
| Full methods, extraction schema, extended tables and reproducibility notes | [paper-supplement](paper-supplement/README.md) |
| Evaluation runners and human-rating analysis | [results/human-eval-20261020/scripts](results/human-eval-20261020/scripts) |
| Re-extraction and FAERS processing scripts | [results](results) |
| Frozen dependency files | [requirements](requirements/README.md) |
| Synthetic reviews for the offline check | [tests/fixtures/reviews.json](tests/fixtures/reviews.json) |

The study's extraction prompt is v9-prompt-7 in `src/weightloss/extraction/reextract.py`, and the evaluated agent is agent-0.8.5. The question list keeps the original IDs, wording, evaluation groups and intended response types.

## Installation and Offline Check

AskGLP1 needs Python 3.11 or 3.12 (the frozen offline environment uses 3.11). Install with uv from the repository root:

```sh
make install
make reproduce OUTPUT=.cache/synthetic-check
```

The offline check runs the pipeline on synthetic reviews with frozen annotation responses. It calls no model and does not reproduce the paper's results. Research dependencies are declared in `pyproject.toml` and described in [requirements/README.md](requirements/README.md).

## Running the Website and Agent

The website (FastAPI backend in `src/weightloss/web`, React frontend in `apps/web`) needs Node.js 22, pnpm 10, a local Neo4j and the KG and FAERS tables. These data hold MedDRA terms and text derived from Reddit posts, so they are not part of this repository. With the data in place:

```sh
make web-install
python -m weightloss.graph.neo4j_db start
make web-dev
```

Questions to the agent need an `OPENAI_API_KEY` in the environment or in `.env` (see `.env.example`). See [apps/web/README.md](apps/web/README.md) for the development commands.

## Data and Licensing

This repository does not distribute Reddit posts, WebMD reviews, account identifiers, model credentials or licensed MedDRA files. Rebuilding the KG requires collecting the source data, a MedDRA subscription and model API access. Code availability does not grant permission to redistribute third-party data or terminology.

## License

The code and accompanying author-created materials are distributed under the [MIT License](LICENSE). Third-party dependencies and resources keep their own licenses.
