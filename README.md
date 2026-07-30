# Q-Matrix CBSE Curriculum Knowledge Graph — Dataset v1.0

> A point-in-time snapshot of a CBSE curriculum → concept/skill → prerequisite dataset,
> produced by the [q-matrix-agents](https://github.com/MaximusTitan/q-matrix-agents)
> generation pipeline. This repo is **data only** — no pipeline code, no prompts, no
> internal run history.

**This is a static release, not a live sync.** It reflects the state of the source
knowledge base at export time (see `graph/meta.json` for the exact export timestamp). It
will not update itself; a future version bump is a separate, deliberate release.

## Contributions welcome — this is the repo that takes them

Of the four Q-Matrix repos, **this is the one that is open for community contribution and
maintained collaboratively.** Corrections to the curriculum data are wanted here,
including small ones, and so are entirely new boards. Start with
**[CONTRIBUTING.md](CONTRIBUTING.md)** — it documents the real schema, the review process,
and the rights attestation every data PR needs.

The other three — [q-matrix-agents](https://github.com/MaximusTitan/q-matrix-agents),
[q-matrix-kb-template](https://github.com/MaximusTitan/q-matrix-kb-template),
[q-matrix-graph-template](https://github.com/MaximusTitan/q-matrix-graph-template) — are
open source but are **not seeking pull requests**. Issues there are welcome; PRs are not.

**Roadmap.** **Cambridge is the next planned board.** Two kinds of contribution are equally
valuable: improving the existing CBSE data (prerequisite edges especially — see
[Provenance](#provenance) for why they are the weakest layer), and adding a new board end
to end. If you want to take on a whole board, open an issue first so the work isn't
duplicated.

Participation is governed by [CODE_OF_CONDUCT.md](CODE_OF_CONDUCT.md).

## What this is

Q-Matrix is an agent pipeline that reads curriculum PDFs and board documentation for a
chapter and produces a validated CSV mapping:

```
Board → Subject → Grade → Chapter → Concept → Skill
```

plus prerequisite edges at three levels — **L1** (within a chapter), **L2** (across
chapters in the same grade+subject), **L3** (across earlier grades). This repo packages
the **confirmed output** of that pipeline: one CSV per chapter, for every chapter that
has completed the full generation-and-verification loop.

- Pipeline code: [q-matrix-agents](https://github.com/MaximusTitan/q-matrix-agents)
- Knowledge-base template (the empty scaffold this data was grown from):
  [q-matrix-kb-template](https://github.com/MaximusTitan/q-matrix-kb-template)
- Graph viewer template (renders this dataset as an interactive 3D graph):
  [q-matrix-graph-template](https://github.com/MaximusTitan/q-matrix-graph-template)

## Paper

This is the dataset released alongside *Curriculum Brain: A Semi-Automated Framework for
Q-Matrix Creation* —
[read the draft](https://prickly-gopher-95e.notion.site/Curriculum-Brain-3a3527ed7aee80cc97f7ee52e302249e).

## What's included

```
q-matrix-dataset/
├── textbooks/CBSE/<Subject>/<Grade>/<Chapter>/confirmed_curriculum.csv   ← 223 files
├── graph/
│   ├── graph-core.json          ← concept nodes + prerequisite edges
│   ├── concept-details.json     ← per-concept skills + prerequisite rationales
│   └── meta.json                ← provenance, per-grade inventory, integrity report
├── CONTRIBUTING.md              ← how to propose data corrections / add a board
├── CODE_OF_CONDUCT.md
├── LICENSE
└── README.md
```

The `Board/Subject/Grade/Chapter` directory structure mirrors the source knowledge base
exactly, so paths are stable and greppable — e.g.
`textbooks/CBSE/Maths/Grade 6/Chapter02_Lines_And_Angles/confirmed_curriculum.csv`.

Two inherited naming inconsistencies to be aware of when globbing: zero-padding of the
chapter number varies (`Chapter5_Lines_And_Angles` next to
`Chapter05_Arithmetic_Progressions`), and the 13 chapters under
`textbooks/CBSE/Maths/Grade 1/` are bare `Chapter 1` … `Chapter 13` with no titles. Match
on `confirmed_curriculum.csv` rather than on a chapter-folder pattern.

### The graph export is derived, not canonical

`graph/*.json` is a **convenience artifact**, mechanically regenerated from this exact
snapshot's CSVs by running `q-matrix-agents/scripts/export_graph.py` against this repo — not
copied from any other export. It makes no LLM or network calls: deterministic parsing,
exact string resolution, and graph algorithms (Tarjan SCC for cycle detection) over the
CSVs above. Given the same CSVs it always produces the same bytes.

**The CSVs in `textbooks/` are the canonical dataset.** If the two ever appear to
disagree, the CSVs are right and the graph export is stale — regenerate it (see
[Regenerating the graph view](#regenerating-the-graph-view) below) rather than trusting a
cached copy.

## What's explicitly excluded

- **`rulesets/`, `prompt-library/`, `escalations/`** — pipeline internals (eval rules,
  generation prompts, dated failure/retry snapshots). None of this is curriculum data,
  and `escalations/` in particular can carry human review notes, feedback text, and run
  reports — excluded in full, not filtered.
- **Textbook PDFs and board curriculum-doc source material.** Official textbook and
  syllabus material is almost always someone else's copyright even when it's freely
  downloadable — see `q-matrix-kb-template`'s rights notice. Only the derived
  concept/skill/prerequisite data is republished here, never the source documents.
- **Run history, cost/token logs, model-selection metadata.** Internal to how the data
  was produced, not part of the dataset itself.
- **Subjects outside this release's scope** (Hindi, English, Social Science, ICT, and any
  board other than CBSE) — these have zero confirmed chapters in the source KB as of this
  snapshot, so there is nothing to include for them; they are absent, not filtered out.

## Scope

**Board: CBSE only.** Three subjects, 223 confirmed chapters total:

| Subject | Grades | Chapters | Concepts (graph-resolved) |
|---|---|---|---|
| Maths | 1–10 | 128 | 1,620 |
| Environmental Science | 3–5 | 32 | 571 |
| Science | 6–10 | 63 | 1,578 |
| **Total** | | **223** | **3,769** |

(Concept counts are per `graph/meta.json`'s inventory, summed per subject; these three
figures sum exactly to the 3,769 total deduplicated node count — a concept's graph
identity is scoped by subject+grade+chapter, so there is no cross-subject collision to
account for here even where a concept name repeats verbatim across chapters.)

**Coverage is 100% within scope**: every Maths (G1–10), Environmental Science (G3–5), and
Science (G6–10) chapter present in the source KB's `textbooks/` tree has reached confirmed
status — there is no "attempted but not yet confirmed" gap inside this scope.

**Explicitly NOT covered** in this release (Cambridge is the next planned board, and
contributions of new boards are welcome — see [CONTRIBUTING.md](CONTRIBUTING.md)):
- Any subject other than Maths, Environmental Science, Science (no Hindi, English,
  Social Science, or ICT chapters exist in confirmed form in the source KB).
- Any board other than CBSE.
- Grades outside the ranges above (e.g. no Maths/Science below Grade 1/6 respectively, no
  Environmental Science outside Grades 3–5 — this mirrors CBSE's own curriculum
  structure, where Environmental Science is superseded by standalone Science from
  Grade 6 onward).

## Schema

The full header is 12 columns:

| Column | Type | Description |
|---|---|---|
| `board` | string | Always `CBSE` in this release. |
| `subject` | string | `Maths`, `Environmental Science`, or `Science`. |
| `grade` | string | e.g. `Grade 6`. |
| `chapter` | string | Chapter identifier as recorded in the row. **Authoritative identity is the directory path, not this column** — two Maths Grade 4 files carry a chapter name from before a folder rename; see the two warnings in `graph/meta.json.integrity.warnings`. Always trust the folder name over this cell if they disagree. |
| `concept` | string | A noun-phrase naming one curriculum concept. |
| `skill` | string | One verb-led, observable/measurable capability tied to that row's concept. |
| `prereq_concepts_L1_same_chapter` | JSON array (as text) | Concept-level prerequisites from **the same chapter**. Each entry is either a bare string (a concept name) or `{"item": "<concept>", "reason": "<why>"}`. Empty (`[]` or blank) means none found. |
| `prereq_skills_L1_same_chapter` | JSON array (as text) | Same shape, at skill granularity. |
| `prereq_concepts_L2_cross_chapter` | JSON array (as text) | Concept-level prerequisites from **a different chapter, same grade+subject**. Each entry: `{"chapter": "<chapter>", "concept": "<concept>", "reason": "<why>"}`. |
| `prereq_skills_L2_cross_chapter` | JSON array (as text) | Same shape, at skill granularity (`"skill"` key instead of `"concept"`). |
| `prereq_concepts_L3_prior_grade` | JSON array (as text) | Concept-level prerequisites from **an earlier grade, same subject** (Science's L3 prerequisites may resolve into Environmental Science for Grades 3–5, since CBSE only introduces Science as its own subject from Grade 6). Each entry: `{"grade": "<grade>", "chapter": "<chapter>", "concept": "<concept>", "reason": "<why>"}`. |
| `prereq_skills_L3_prior_grade` | JSON array (as text) | Same shape, at skill granularity. |

**198 of the 223 files carry all 12 columns. The remaining 25 carry only the first 10**,
omitting `prereq_concepts_L3_prior_grade` and `prereq_skills_L3_prior_grade` — these are
the earliest grade of each subject, where no prior grade exists to reference: the 13
chapters under `textbooks/CBSE/Maths/Grade 1/` and the 12 under
`textbooks/CBSE/Environmental Science/Grade 3/`. An absent L3 column and an empty L3 cell
mean the same thing to the exporter.

All six prerequisite columns are read the same way by the graph exporter: parsed as
JSON, `[]`/blank means "none found at this level for this row," and a reference is
resolved by exact name match scoped to the level's stated (grade, chapter) — there are no
IDs in the CSVs themselves; `graph/graph-core.json` mints them.

## Provenance

**Concept/skill mapping (the `concept` and `skill` columns) is LLM-generated with
human-in-the-loop verification.** Each chapter runs through a generate → evaluate → repair
loop: a Generator agent proposes concept/skill rows, an Eval agent runs two independent
checks (universal rules compliance, and coverage of the chapter's source material), and a
chapter that fails either check is routed to an automated repair pass (surgical
doctor/rules-doctor fixes for a single failing check, or a full prompt revision for
larger misses) before being retried — up to an adaptive attempt ceiling. A human can
reject a chapter's output at any point, which is recorded as a durable rule the pipeline
must satisfy on its next attempt. Only chapters that pass both checks (directly or after
repair) reach `confirmed_curriculum.csv`.

**Prerequisite edges (all six `prereq_*` columns, L1/L2/L3) are LLM-generated but are
NOT run through that same verification loop.** Each level is a single LLM call (retried
only if the model's output fails to parse as valid JSON) whose result is written straight
into the confirmed CSV — no equivalent of the Eval agent's checks, no automated repair,
no escalation, and no human-reject path specific to a prerequisite edge. The only
guardrail is structural: a proposed prerequisite must resolve to an actual concept/skill
in the stated (grade, chapter) scope, or it is silently dropped — this catches
hallucinated targets, not incorrect-but-plausible judgments. **Treat prerequisite-edge
quality as a lower confidence tier than the concept/skill mapping itself**, consistent
with how the accompanying paper's evaluation section (§7) frames this same asymmetry.
If you need edges at publication-grade confidence, treat them as a starting point for
your own review, not a verified ground truth.

`graph/concept-details.json` preserves each edge's `reason` text (the model's stated
rationale) and a `derived` flag distinguishing edges an agent asserted directly from ones
mechanically lifted in code (if skill A precedes skill B, then concept(A) precedes
concept(B) is inferred, not separately judged) — useful context when deciding how much to
trust any individual edge.

## License

**Creative Commons Attribution-ShareAlike 4.0 International (CC BY-SA 4.0).**

Full legal code: [LICENSE](./LICENSE) · Plain-language summary:
[LICENSE-SUMMARY.md](./LICENSE-SUMMARY.md) · Canonical upstream:
https://creativecommons.org/licenses/by-sa/4.0/legalcode

You are free to share and adapt this dataset for any purpose, including commercially,
provided you give appropriate attribution and license any derivative dataset under the
same terms. See [Citation](#citation) below for the attribution text.

This license covers the derived data in this repository (CSVs and the graph export)
only — it says nothing about, and does not extend to, any textbook or curriculum-doc
source material, which is deliberately not included here (see
[What's explicitly excluded](#whats-explicitly-excluded)).

## Regenerating the graph view

`graph/` in this snapshot is a pre-built convenience copy. To regenerate it yourself
(e.g. after editing a CSV in your own fork), or to browse this dataset interactively:

```bash
# 1. Clone the viewer
git clone https://github.com/MaximusTitan/q-matrix-graph-template.git
cd q-matrix-graph-template
npm install

# 2. Clone the pipeline code alongside it (only scripts/export_graph.py is needed)
git clone https://github.com/MaximusTitan/q-matrix-agents.git ../q-matrix-agents
cd ../q-matrix-agents && pip install -r requirements.txt && cd -

# 3. Point KB_ROOT at THIS repo (your local clone of q-matrix-dataset) and export
KB_ROOT=/absolute/path/to/q-matrix-dataset \
  python ../q-matrix-agents/scripts/export_graph.py --out public/graph --pretty --check

# 4. Run the viewer
npm run dev            # http://localhost:3000
```

`--check` exits non-zero if any prerequisite reference fails to resolve — it should exit
clean against this dataset (verified at export time for this snapshot; see
`graph/meta.json.integrity.unresolvedRefs`, which is `0`).

Full detail on the exporter's contract, the three JSON files' schema, and what a given
kind of CSV edit costs to re-export lives in `q-matrix-graph-template`'s own README.

## Citation

If you use this dataset, please cite both the accompanying paper and this dataset
release.

**This dataset** — this is the authoritative attribution text referenced by
[LICENSE](LICENSE), and it is machine-readable in [CITATION.cff](CITATION.cff):

```
Tamboli, Shrideep. (2026). Q-Matrix CBSE Curriculum Knowledge Graph Dataset, v1.0.
https://github.com/MaximusTitan/q-matrix-dataset. Licensed CC BY-SA 4.0.
```

**Paper** — the draft is [Curriculum Brain: A Semi-Automated Framework for Q-Matrix
Creation](https://prickly-gopher-95e.notion.site/Curriculum-Brain-3a3527ed7aee80cc97f7ee52e302249e).
It is unpublished, so there is no venue, year of record, or DOI to cite yet. Cite the
dataset above until there is; this section will be updated on publication.

## Contributing

See **[CONTRIBUTING.md](CONTRIBUTING.md)**. Short version: the CSVs under `textbooks/` are
the canonical data and the thing to edit; `graph/` is regenerated, never hand-edited; every
data PR needs a rights attestation, and "freely accessible" is not "freely
redistributable". Prerequisite edges are the least-verified layer and the highest-value
thing to correct. Issues are open for anything short of a diff.

## Related repositories

| Repo | What it is | Takes PRs? |
|---|---|---|
| [q-matrix-agents](https://github.com/MaximusTitan/q-matrix-agents) | The multi-agent pipeline that produced this data | No — issues only |
| [q-matrix-kb-template](https://github.com/MaximusTitan/q-matrix-kb-template) | Empty knowledge-base scaffold to run the pipeline against | No — issues only |
| [q-matrix-graph-template](https://github.com/MaximusTitan/q-matrix-graph-template) | Interactive 3D viewer for this dataset | No — issues only |
| **q-matrix-dataset** (this repo) | The curriculum dataset | **Yes** |

Paper draft: [Curriculum Brain](https://prickly-gopher-95e.notion.site/Curriculum-Brain-3a3527ed7aee80cc97f7ee52e302249e)

## Versioning

This is **v1.0** — a single point-in-time snapshot, generated from the source knowledge
base at export timestamp `graph/meta.json.generatedAt`. There is no live sync
between this repo and the source knowledge base; a future snapshot (v1.1, v2.0, …) would
be a new, deliberate export and release, not an automatic update.
