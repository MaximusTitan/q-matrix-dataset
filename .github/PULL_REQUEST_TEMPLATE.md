<!--
Thanks for contributing. This repo is the one open for contribution across the four
Q-Matrix repos, and corrections are genuinely wanted — including small ones.

Read CONTRIBUTING.md before your first data PR. The rights attestation below is
required and is not a formality.
-->

## What this changes

<!-- One or two sentences. If it's a data correction, say what was wrong and what's right. -->

## Type of change

- [ ] Correction to existing curriculum data (concept, skill, or prerequisite edge)
- [ ] New board's curriculum
- [ ] Documentation only
- [ ] Something else (explain above)

## Files touched

<!--
List the confirmed_curriculum.csv paths you edited, e.g.
textbooks/CBSE/Maths/Grade 6/Chapter02_Lines_And_Angles/confirmed_curriculum.csv
-->

## Source for the curriculum claim

<!--
How do you know this is correct? Name the syllabus document, textbook chapter, or
board publication. "It seems right" is not reviewable; a citation is.
-->

## Checklist

- [ ] I edited the CSVs under `textbooks/`, which are the canonical data
- [ ] I did **not** hand-edit anything under `graph/` (it is regenerated, never edited)
- [ ] Prerequisite references I added resolve by exact string match within the stated
      grade and chapter (unresolved references are silently dropped by the exporter)
- [ ] This PR is scoped to one coherent change, not several unrelated ones

## Rights attestation — required for any PR touching curriculum data

Delete this section only if your PR changes no curriculum data at all.

By checking the box below I make this statement:

> I attest that I hold, or have been explicitly granted, the right to redistribute the
> curriculum-derived data in this pull request under this repository's license
> (CC BY-SA 4.0), and that redistributing it here does not breach any copyright,
> license, or terms of use governing the material it was derived from.

- [ ] I make the attestation above.

Note that **"freely accessible" is not "freely redistributable"** — official board
material is very often the former and not the latter. If you cannot make this statement
truthfully, please don't open the PR; see CONTRIBUTING.md for what to do instead.
