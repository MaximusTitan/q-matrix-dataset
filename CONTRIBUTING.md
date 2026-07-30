# Contributing to q-matrix-dataset

**This is the contribution-friendly repo of the Q-Matrix family.** Curriculum data
benefits enormously from people who actually teach a subject looking at it, and a
prerequisite graph produced by a model is exactly the kind of artifact that gets better
when a domain expert disagrees with it in public. Corrections are wanted here, including
small ones.

The three sibling repos —
[q-matrix-agents](https://github.com/MaximusTitan/q-matrix-agents),
[q-matrix-kb-template](https://github.com/MaximusTitan/q-matrix-kb-template),
[q-matrix-graph-template](https://github.com/MaximusTitan/q-matrix-graph-template) — are
open source but are **not** seeking pull requests. If your change is to pipeline code,
prompts, or the viewer, please open an issue in the relevant repo rather than a PR.

Contents:

- [Before you start: rights attestation](#before-you-start-rights-attestation)
- [What the data actually is](#what-the-data-actually-is)
- [Correcting existing curriculum data](#correcting-existing-curriculum-data)
- [Adding a new board end to end](#adding-a-new-board-end-to-end)
- [What a good contribution looks like](#what-a-good-contribution-looks-like)
- [How review works](#how-review-works)

---

## Before you start: rights attestation

Every pull request that adds or modifies curriculum-derived data must include this
statement in the PR description:

> I attest that I hold, or have been explicitly granted, the right to redistribute the
> curriculum-derived data in this pull request under this repository's license
> (CC BY-SA 4.0), and that redistributing it here does not breach any copyright,
> license, or terms of use governing the material it was derived from.

PRs that add or change curriculum data without this attestation are closed unmerged. This
is not a formality. This repository is public and CC BY-SA 4.0 licensed, so merging your
PR is an act of redistribution, and it grants everyone downstream the right to
redistribute again.

If you cannot make that statement truthfully, do not open the PR. Keep the data in a
private fork — nothing about Q-Matrix requires your curriculum data to be public.

### "Freely accessible" is not "freely redistributable"

These two are constantly confused, and the confusion is what gets projects like this into
trouble. They are different things:

**Freely accessible** means you can *obtain* a copy at no cost. No paywall, no login, a
public download link.

**Freely redistributable** means the rights holder has *granted you permission to
republish* that copy somewhere else. That requires an actual grant: an explicit open
license (Creative Commons, an Open Government Licence, or similar), written permission
from the rights holder, or confirmed public-domain status.

Official education-board material is very often the first and almost never the second.

**A concrete example.** NCERT publishes the prescribed CBSE textbooks as free PDF
downloads — no account, no payment, one click. That is *freely accessible*. It is not
*freely redistributable*: the PDFs carry an explicit copyright notice reserving
reproduction rights, and nothing on the download page grants you a license to republish
them. So you may read the textbook, teach from it, and use it to inform a contribution
here — and you may **not** commit the PDF, or a verbatim transcription of its text, to
this repo. The same pattern holds for most boards worldwide: syllabus documents,
learning-outcome frameworks, and prescribed textbooks are usually free to download and
still fully copyrighted.

"It was on the internet for free" is not a license. Neither is "it's for education" — fair
use and fair dealing are fact-specific defences, and they do not clear a blanket
republication in a public repository. If you cannot find an actual license or permission,
treat the material as **not** redistributable. An absent license is a "no", not a "maybe".

### Describe and structure; do not copy

What this dataset holds is a *description* of a curriculum: concept names, the skills
attached to them, and prerequisite relationships between them. That is the kind of
contribution to make.

Contributable:

- Concept names and skill statements **written in your own words** to describe what a
  chapter teaches.
- Prerequisite relationships — which concept a learner needs before another. Relationships
  are your analysis, not the source's text.
- Corrections, reorderings, merges, and splits based on your subject expertise.
- Material under an explicit open license that covers redistribution (check the license
  actually permits redistribution, not merely access), material you authored, or material
  you have written permission to redistribute.

Not contributable:

- Textbook PDFs, chapter scans, or page images. The `.gitignore`-level rule here is
  simpler: this repo contains no source documents at all, by design.
- Verbatim or lightly-paraphrased extracts of textbook or syllabus prose, including in the
  `reason` text of a prerequisite edge.
- Rows so closely tracking a copyrighted document's own headings and wording that they
  function as a substitute for it. Being machine-generated does not launder the source's
  rights.
- Exercise questions, worked examples, figures, or assessment items.

A useful test: if someone could reconstruct a readable version of the chapter from your
rows, the rows are too close to the source.

---

## What the data actually is

Two things, in a specific relationship.

### `textbooks/` — the canonical data

```
textbooks/<Board>/<Subject>/<Grade>/<Chapter>/confirmed_curriculum.csv
```

One CSV per chapter. Currently 223 files, all under `textbooks/CBSE/`, covering
`Maths` (Grades 1–10), `Science` (Grades 6–10), and `Environmental Science`
(Grades 3–5). A real path, to copy the shape from:

```
textbooks/CBSE/Maths/Grade 6/Chapter02_Lines_And_Angles/confirmed_curriculum.csv
```

Directory naming conventions, which are **not** validated by any code in this repo, so
follow them by hand:

- Grade folders are `Grade N`, with a space. Not `Grade-8`, not `grade8`.
- Chapter folders are `Chapter{NN}_{Title_With_Underscores}` — underscores for spaces, no
  characters that are illegal on Windows (`: / \ * ? " < > |`), no trailing dot or space.
- Existing inconsistencies you will notice and should **not** replicate: zero-padding of
  the chapter number varies (`Chapter5_Lines_And_Angles` alongside
  `Chapter05_Arithmetic_Progressions`), and the 13 chapters under
  `textbooks/CBSE/Maths/Grade 1/` are bare `Chapter 1` … `Chapter 13` with no titles. New
  work should use zero-padded, titled folders.

### `graph/` — derived, regenerated, never hand-edited

`graph/graph-core.json`, `graph/concept-details.json`, and `graph/meta.json` are
mechanically produced from the CSVs above. **Do not edit them by hand and do not
hand-patch them in a PR.** Either regenerate them (see
[Regenerating the graph view](README.md#regenerating-the-graph-view) in the README) or
leave them alone and say so in your PR — a reviewer can regenerate. A PR whose `graph/`
diff is inconsistent with its `textbooks/` diff will be sent back.

If the CSVs and the graph export ever disagree, the CSVs are right.

### The CSV schema

The full header is 12 columns:

```
board,subject,grade,chapter,concept,skill,prereq_concepts_L1_same_chapter,prereq_skills_L1_same_chapter,prereq_concepts_L2_cross_chapter,prereq_skills_L2_cross_chapter,prereq_concepts_L3_prior_grade,prereq_skills_L3_prior_grade
```

| Column | Contents |
|---|---|
| `board` | Currently always `CBSE`. |
| `subject` | `Maths`, `Science`, or `Environmental Science`. |
| `grade` | e.g. `Grade 6` — matches the folder name. |
| `chapter` | Chapter identifier. **The folder name is the authoritative identity, not this cell.** Two files disagree today; see `graph/meta.json` → `integrity.warnings`. |
| `concept` | A noun phrase naming one curriculum concept. |
| `skill` | One verb-led, observable capability tied to that row's concept. |
| `prereq_concepts_L1_same_chapter` | JSON array. Prerequisite concepts **in the same chapter**. |
| `prereq_skills_L1_same_chapter` | JSON array. Same, at skill granularity. |
| `prereq_concepts_L2_cross_chapter` | JSON array. Prerequisites in **a different chapter, same grade and subject**. |
| `prereq_skills_L2_cross_chapter` | JSON array. Same, at skill granularity. |
| `prereq_concepts_L3_prior_grade` | JSON array. Prerequisites in **an earlier grade, same subject**. |
| `prereq_skills_L3_prior_grade` | JSON array. Same, at skill granularity. |

**A concept spans multiple rows.** One row is one (concept, skill) pair, so a concept with
four skills is four rows sharing the same `concept` value. 223 files hold 7,145 rows and
3,769 distinct concepts.

**The two `L3` columns are omitted entirely in the earliest grade of a subject**, because
there is no prior grade to point at. The 13 files under `textbooks/CBSE/Maths/Grade 1/` and
the 12 under `textbooks/CBSE/Environmental Science/Grade 3/` therefore have a 10-column
header; the other 198 files have all 12. Match whatever the sibling files in that
grade do.

#### Prerequisite JSON shapes

Each prerequisite cell is a JSON array serialised into the CSV field (so inner double
quotes are CSV-escaped by doubling — let your CSV writer handle it). `[]` or an empty cell
means "no prerequisite found at this level for this row".

L1, same chapter — a bare concept/skill string, or an object with a reason:

```json
["Place value of two-digit numbers"]
[{"item": "Place value of two-digit numbers", "reason": "Regrouping requires reading tens and ones separately."}]
```

L2, cross-chapter within the same grade and subject — the chapter must be named:

```json
[{"chapter": "Chapter02_Going_to_the_Mela", "concept": "Reading a simple map", "reason": "..."}]
```

Use `"skill"` in place of `"concept"` in the `prereq_skills_L2_cross_chapter` column.

L3, earlier grade, same subject — grade and chapter must both be named:

```json
[{"grade": "Grade 5", "chapter": "Chapter02_Shapes_And_Angles", "concept": "Angle as a turn", "reason": "..."}]
```

Again, `"skill"` instead of `"concept"` for the skills column.

**References resolve by exact string match, scoped to the level's stated (grade,
chapter).** There are no IDs in the CSVs — `graph/graph-core.json` mints those at export
time. A prerequisite that does not match an existing `concept` or `skill` string in the
scope it names is silently dropped by the exporter, which is the single most common way a
well-intentioned edit becomes a no-op. Copy the target string exactly, including
capitalisation and punctuation.

One documented exception to "same subject": `Science` may take L3 prerequisites from
`Environmental Science`, because CBSE only introduces Science as its own subject from
Grade 6. This is the only such alias.

`Grade 10` sorts after `Grade 9`, not between `Grade 1` and `Grade 2` — the exporter
handles this numerically, but be careful if you sort paths in a shell.

---

## Correcting existing curriculum data

Nearly all corrections are an edit to one or more `confirmed_curriculum.csv` files.

**Prerequisite edges are the highest-value target.** As the README's Provenance section
explains, the concept/skill mapping went through an automated evaluate-and-repair loop with
a human reject path, while the prerequisite edges did not — they are single-pass LLM output
whose only guardrail was that the target had to exist. Treat them as the least-trustworthy
layer. If a prerequisite looks wrong to you, it may well be.

Concrete things worth fixing:

- **A wrong prerequisite.** Remove the entry from the array, and say in the PR why it is
  not a prerequisite. "Grade 7 Rational Numbers does not require X; X is introduced
  alongside it, not before it" is a complete argument.
- **A missing prerequisite.** Add an entry at the correct level, with a `reason`. Verify
  the target string exists verbatim in the scope you name.
- **A prerequisite filed at the wrong level.** L1/L2/L3 are determined by where the target
  lives (same chapter / same grade different chapter / earlier grade), not by how
  important it is. Moving an entry between columns is a legitimate fix.
- **A circular prerequisite chain.** `graph/meta.json` → `integrity.cycles` reports 88
  cyclic components today, covering 210 concepts. Some are genuine modelling errors. If
  you can identify which edge in a cycle is the wrong one, that is a very good PR.
- **An isolated concept.** `integrity.isolatedNodes` is 214 — concepts with no
  prerequisites in either direction. Some legitimately have none; others are missing
  edges.
- **A mis-stated skill.** Skills should be verb-led and observable. "Understand
  fractions" is not a skill; "Compare two fractions with unlike denominators" is.
- **A concept that is really two concepts, or two that are really one.** Splits and merges
  are welcome, but they are structural: every prerequisite anywhere in the dataset that
  names the old string by exact match must be updated too. Grep before you PR:
  `grep -rF "Old concept name" textbooks/`
- **The `chapter` column disagreeing with the folder name.** Two known cases in
  `textbooks/CBSE/Maths/Grade 4/`, listed in `graph/meta.json` →
  `integrity.warnings`. Fixing the cell to match the folder is a clean, easily-reviewed PR.

After editing, sanity-check that every reference still resolves. The exporter's `--check`
flag exits non-zero if any prerequisite reference fails to resolve; see the README's
[Regenerating the graph view](README.md#regenerating-the-graph-view) section for the
invocation.

---

## Adding a new board end to end

New boards are explicitly welcome — this is the main way the dataset is meant to grow.
**Cambridge is the next planned addition**; if you want to work on it, open an issue first
so two people don't build it twice.

Read [Before you start: rights attestation](#before-you-start-rights-attestation) before
anything else. A new board is where the accessible-vs-redistributable question bites
hardest, because you will be working straight from board documents.

### Talk to us first

For a whole board, open an issue before writing data. A board is hundreds of files; the
schema and naming questions are much cheaper to settle up front than in review. Say which
board, which subjects and grades you plan to cover, which source documents you are working
from, and what rights you have in them.

### Know the one code-level constraint

The graph exporter in q-matrix-agents currently hardcodes `BOARD = "CBSE"` and only reads
`textbooks/CBSE/`. So a new board's CSVs will sit in this repo correctly and be completely
usable on their own, but `graph/` will not include them until that exporter is
generalised — and that change belongs in
[q-matrix-agents](https://github.com/MaximusTitan/q-matrix-agents), which does not accept
PRs. Open an issue there. **Do not let this block your data contribution**; the CSVs are
the canonical dataset, and the graph export catching up later is fine.

### Suggested sequence

1. **Issue first**, as above.
2. **Establish the subject and grade vocabulary.** Board folder name, subject folder names,
   grade labels. Boards do not all say "Grade" — if yours uses stages, years, or forms,
   raise it in the issue; the `Grade N` convention may need a documented per-board mapping
   rather than a silent reinterpretation.
3. **Land one chapter first, as its own PR.** One `confirmed_curriculum.csv` for one
   chapter, with all 12 columns (or 10, if it is the earliest grade in its subject) and
   L1 prerequisites populated. This settles the conventions with almost no review cost.
4. **Then land grade by grade.** One PR per grade per subject is a good unit. Within a
   grade, fill L1 and L2. Leave L3 empty until the earlier grade exists — a prerequisite
   pointing at a grade that is not in the repo yet will not resolve.
5. **Add L3 in a following pass**, once the earlier grades are present.
6. **Do not regenerate `graph/`** as part of a new-board PR while the exporter is
   CBSE-only. It will not include your board, and a spurious `graph/` diff makes the PR
   harder to review.

A partial board is a genuinely useful contribution. Two complete grades of one subject
beats a thin sketch of twelve.

---

## What a good contribution looks like

**One coherent change per PR.** A PR is reviewable when a reviewer can hold its whole
claim in their head. Good units: one chapter's prerequisites; one subject's skill wording
in one grade; one cycle broken; one concept split with all its references updated. Bad
units: "fixes across Maths Grades 1–10", or an unrelated typo fix bundled with a
prerequisite change.

Do not go the other way either — do not split one logical fix across six PRs because it
touches six files. Related edits belong together.

**Cite your source for every curriculum claim.** Any assertion about what a board
prescribes — that a topic is in Grade 8 and not Grade 7, that concept A precedes concept B
in the syllabus — needs to be checkable. Acceptable citations:

- A named board document with a locator: *"CBSE Science syllabus 2024–25, Unit III"*, or
  *"NCERT Class 8 Science, Chapter 4, section 4.2"*. Chapter and section beat a bare
  document name.
- A public URL, plus enough of a locator to find the claim on the page.
- Your own reasoning, labelled as such: *"pedagogical judgement: solving linear equations
  requires transposition, which this chapter introduces"*. This is a legitimate and often
  the right basis — just say that is what it is, rather than implying a document.

"I'm a teacher, this is wrong" is a welcome start, but a reviewer needs something to check
against. Add the locator.

**A PR description that a reviewer can act on**, covering: the rights attestation; what
you changed and why; your sources; whether you regenerated `graph/`; and anything you were
unsure about. Flagging your own uncertainty speeds review up, it does not weaken your PR.

**Mechanics.** Keep CSV formatting as you found it: existing quoting style, no reordered
or added columns, no changed line endings, no re-sorted rows. A diff that shows only your
intended change is much more likely to be merged quickly. Do not reformat a file you are
editing one cell of.

---

## How review works

A reviewer checks, roughly in this order:

1. **Rights attestation present**, if the PR touches curriculum data. Missing attestation
   stops review immediately.
2. **Rights posture plausible.** Does the diff look like *described* curriculum structure,
   or like transcribed source text? Long, prose-like `concept` values or `reason` text that
   reads as if lifted from a textbook will be questioned.
3. **Schema conformance.** Correct column count for that grade, valid JSON in the
   prerequisite cells, correct object shape for the level, `board`/`subject`/`grade`
   matching the path.
4. **References resolve.** Every prerequisite target must exist verbatim in the scope it
   names. This is mechanically checkable with the exporter's `--check` flag and it is the
   most common reason a PR needs another round.
5. **The curriculum claim itself.** Is the correction right? This is where your citations
   are read, and where a reviewer may bring in someone who teaches the subject.
6. **Structural consistency.** For splits, merges, and renames: were all other references
   to the old string updated? For prerequisite additions: does it create a cycle?
7. **Scope and diff hygiene.** One coherent change, no unrelated churn, `graph/` consistent
   with `textbooks/` (or untouched and declared as such).

Expect a conversation. Curriculum sequencing is a domain where reasonable experts
disagree, and "the model was wrong" and "you and the model model this differently" look
similar at first. A reviewer asking for your reasoning is not a rejection.

If a disagreement is genuinely unresolvable, the fallback is to keep the edge and record
the disagreement in its `reason` text rather than silently pick a side.

Issues are open, and are the right place for anything short of a diff: a suspected error
you haven't the time to fix, a schema question, a new-board proposal, or a question about
whether some material is contributable at all. Ask before you build.

## Code of Conduct

Participation in this project is governed by [CODE_OF_CONDUCT.md](CODE_OF_CONDUCT.md).
