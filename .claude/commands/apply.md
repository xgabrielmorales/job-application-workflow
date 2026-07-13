# /apply - Drafter-Reviewer Job Application Workflow

You are orchestrating a two-agent job application workflow. The job posting is provided below as `$ARGUMENTS` (either a
URL or pasted text).

Follow these steps **exactly in order**. Do not skip steps.

**Token-efficiency rules for this workflow:**

- Never re-Read a file whose contents are already in your context from an earlier step. If you read it in Step 1, it is
  still available in Step 2.
- When dispatching the reviewer agent, pass draft content **inline in the agent prompt** rather than asking the agent to
  Read files you already have in memory.
- Run the full verification checklist exactly once, at the end (Step 7). The reviewer focuses on content critique, not
  verification.
- Step 6 (compile and inspect the PDF) is mandatory and non-skippable — LaTeX page-break decisions are unpredictable,
  and `.tex` files that look fine often produce broken PDFs (orphaned entry titles, bullet fonts mismatching).
- Company research (Step 2) only runs once the user confirms they want an application — don't burn WebSearch/WebFetch
  calls researching a company the user rejects in Step 1.

---

## Step 0: Parse Input

- If `$ARGUMENTS` looks like a URL, use `WebFetch` to retrieve the job posting content.
- If it is pasted text, use it directly.
- Extract: **company name**, **role title**, **department** (if mentioned), **location**, and **language** of the
  posting.
- Store these for use throughout the workflow.

---

## Step 1: DRAFTER - Evaluate Fit

Read the evaluation framework:

- `.claude/skills/job-application-assistant/01-candidate-profile.md`
- `.claude/skills/job-application-assistant/03-job-evaluation.md`

Using the framework from `03-job-evaluation.md`, evaluate the job posting against the candidate's profile.

Present the evaluation to the user with:

1. **Skills match** - which required/preferred skills match vs. gaps
2. **Experience match** - how work history maps to the role
3. **Behavioral/culture match** - how behavioral profile fits the role/company culture
4. **Overall fit score** and recommendation (strong fit / moderate fit / weak fit)

After presenting the evaluation, ask the user:

> "Should I proceed with drafting the CV for this role?"

**If the user says no, stop here.** If yes, continue to Step 2.

---

## Step 2: RESEARCHER - Company Research

Use the **Agent tool** to spawn a `general-purpose` researcher agent. Pass the company name, role title, department
(if known), and the job posting text **inline in the prompt** — the agent has a fresh context and cannot see
anything from earlier steps.

Replace `<COMPANY>`, `<ROLE>`, `<DEPARTMENT>`, and `<INSERT_JOB_POSTING_TEXT_HERE>` with actual values before
dispatching.

````text
You are researching a company on behalf of a job applicant, to ground a tailored CV and interview prep in verified facts.

## Company & Role

- Company: <COMPANY>
- Role: <ROLE>
- Department: <DEPARTMENT>

## Job Posting

<JOB_POSTING>
<INSERT_JOB_POSTING_TEXT_HERE>
</JOB_POSTING>

## Your Tasks

### 1. Research the Company

Use WebSearch and WebFetch to research:

- **Overview** — what the company does, industry, size/stage, HQ location, founding year
- **Mission, values & culture** — stated mission/values, culture signals from the careers page, engineering blog, or reviews
- **Products & market position** — core offerings, competitors
- **Tech stack & engineering practices** — languages, cloud providers, infra tooling, if discoverable (engineering blog, the posting itself, StackShare-type sources)
- **Recent news & strategic initiatives** — funding rounds, launches, expansions, press releases
- **Team/department context** — specifics on the team named in the posting, if any
- **Remote work & location fit** — remote policy, LATAM/timezone friendliness (the candidate is Colombia-based, remote-only, LATAM-friendly timezones required — call this out explicitly, it is a deal-breaker)
- **Reviews & reputation signals** — Glassdoor/Indeed-style feedback, red flags (disorganization, high turnover, leadership issues)
- **Web & social presence** — the official website URL, LinkedIn company page, and any other active social profiles (X/Twitter, GitHub org, Instagram, etc.) actually found during research

### 2. Write the Research File

Write `applications/<COMPANY>/<COMPANY>.md` with one heading per topic above, in that order, plus a closing
**Sources** section listing every URL consulted. Every claim must trace to a source found during this research —
no speculation, no filling gaps with assumptions.

### 3. Return a Summary

Return a short 3-5 bullet summary of the findings most relevant to CV tailoring and interview prep (not the full
file content).
````

The researcher agent writes the file directly — you do not need to Read it back. Keep its returned summary in
working memory for Step 3 and Step 5.

---

## Step 3: DRAFTER - Draft CV

You already have `01-candidate-profile.md` and `03-job-evaluation.md` in context from Step 1. **Do not re-read them.**

Read only the reference files you do not yet have:

- `.claude/skills/job-application-assistant/02-writing-style.md`
- `.claude/skills/job-application-assistant/04-cv-templates.md`

Also read the LaTeX template files for concrete structural reference:

- `curriculum/base-curriculum.tex` — master reference with real data
- `curriculum/base-curriculum-template.tex` — skeleton with placeholder fields

### CV (`applications/<company>/curriculum.tex`)

- Always in **English**
- Follow the format from `04-cv-templates.md` (an `ACTIVE-TEMPLATE` block at the top means a custom template overrides
  the stock guidance below it - follow the override)
- Tailor the profile statement and experience bullets to the specific role
- Reframe skills and achievements to match job requirements
- Keep to the page limit stated in `04-cv-templates.md` (stock: 1 page; overridden by the active template if one is
  set)
- Any mention of agentic coding or AI tooling must reference **Claude Code** by name
- Ground the profile statement in the Step 2 research summary (mission, a recent initiative, tech stack) when it
  strengthens the pitch — only use what Step 2 verified, never fabricate a company-specific claim

Write the file to disk. Keep the exact text of the draft in working memory — you will pass it inline to the reviewer in
Step 4 and revise it in Step 5 without re-reading.

---

## Step 4: REVIEWER - Critique

Use the **Agent tool** to spawn a `general-purpose` reviewer agent. The reviewer gets a fresh context, so pass the
draft **inline in the prompt** below (do not make the reviewer Read it). Scope the reviewer's file reads to
content-critique essentials only — the reviewer does not need the LaTeX template file (`04`) to critique content,
since it governs structural/LaTeX concerns the drafter already applied.

Replace `<COMPANY>`, `<ROLE>`, `<INSERT_JOB_POSTING_TEXT_HERE>`, and `<INSERT_CV_DRAFT_HERE>` with actual values before
dispatching.

````text
You are a hiring manager proxy reviewing a job application. Your job is to make the application as targeted and compelling as possible.

## Your Tasks

### 1. Read Reference Materials (content-critique only)

Read these four files — and only these — to ground your critique:

- `.claude/skills/job-application-assistant/01-candidate-profile.md`
- `.claude/skills/job-application-assistant/02-writing-style.md`
- `.claude/skills/job-application-assistant/03-job-evaluation.md`
- `applications/<COMPANY>/<COMPANY>.md` — the company research produced earlier in this workflow. Do not
  re-research the company yourself; this file is already verified.

Do NOT read `04-cv-templates.md` — it governs LaTeX structure the drafter already applied and is not needed for content critique.

### 2. Draft to Review

The draft is provided inline below. Do NOT use the Read tool on the draft file — use this exact text.

<CV*DRAFT file="applications/<COMPANY>/curriculum.tex">
<INSERT_CV_DRAFT_HERE>
</CV_DRAFT>

### 3. Job Posting

<JOB_POSTING>
<INSERT_JOB_POSTING_TEXT_HERE>
</JOB_POSTING>

### 4. Produce Feedback

Return your feedback in **two parts**:

**Part A — Structured edits (preferred format whenever possible):**
A JSON array of concrete edits the drafter can apply directly without re-reading the file. Each edit is an object:

```json
{
  "file": "applications/<COMPANY>/curriculum.tex",
  "old_string": "<exact text currently in the draft>",
  "new_string": "<replacement text>",
  "reason": "<one-line rationale: keyword match / company angle / reframing / style>"
}
```
````

Only use this format when you can quote the exact `old_string` from the draft above. Make `old_string` unique — include
enough surrounding context so it matches exactly once.

**Part B — Narrative suggestions (for judgment calls that are not mechanical edits):** Prose suggestions grouped by
category. Produce each category even if your finding is "no issues" — silence on a category can be mistaken for skipping
it.

- **Missed keywords/requirements** — what to add and roughly where, if it cannot be expressed as a clean string
  replacement
- **Company/department-specific angles** — connections between experience and the company's strategic priorities,
  based on the company research file
- **Action-oriented reframing** — identify passive, generic, or low-energy statements and suggest action-oriented
  rewrites. Use this category especially for structural weakness that doesn't fit a single-sentence swap (e.g., "the
  whole profile statement reads as passive — restructure around your single strongest match to the posting").
- **Tone and style issues** — check against `02-writing-style.md`. Flag any issues with tone, formality, or voice
  (cliches, hedging, over-humility, inconsistent register).

**CRITICAL RULE:** All suggestions must be grounded in actual profile data. Do NOT suggest fabricating skills,
experience, or achievements. If a requirement is a gap, say so honestly and suggest how to frame adjacent experience
instead.

Do **not** run a verification checklist — the drafter will do that in the final step. Focus on content critique.

Return Part A and Part B together as a single structured message.

---

## Step 5: DRAFTER - Revise Based on Feedback

Once the reviewer agent returns its feedback:

1. **Apply Part A (structured edits) directly with the Edit tool.** Do NOT re-read the draft file — you already have it
   in context from Step 3, and the reviewer's `old_string` values were quoted from that same text. For each edit in the
   JSON array, call `Edit` with the given `file`, `old_string`, and `new_string`. Skip any whose rationale would require
   fabricating content.
2. **Apply Part B (narrative suggestions)** using judgment. These need interpretation, not mechanical replacement. Walk
   through every Part B category the reviewer returned and address it:
   - **Missed keywords/requirements:** add the keyword or capability where it fits naturally in the CV. Prefer the
     experience bullets (concrete evidence) over the profile statement (abstract claim).
   - **Company/department-specific angles:** weave findings from `applications/<company>/<company>.md` (produced in
     Step 2) into the profile statement where it fits naturally — Step 2 already verified these via
     WebFetch/WebSearch, no need to re-verify.
   - **Action-oriented reframing:** rewrite passive or generic phrasing (profile statement, bullet leads). Structural
     weakness that the reviewer flagged without a clean JSON edit lives here.
   - **Tone and style issues:** apply the writing-style-guide fixes (no em-dashes, no cliches, no apologetic hedging,
     consistent first-person active voice).
     Use Edit for targeted changes; only re-read the file if an edit fails because the surrounding text has shifted.
3. Do NOT incorporate any suggestion that would fabricate skills or experience. If a posting requirement is a genuine
   gap, acknowledge it honestly and frame adjacent experience instead.

After all edits are applied, the file on disk is the final draft.

---

## Step 6: DRAFTER - Compile & Inspect PDF (MANDATORY)

**Never skip this step.** The `.tex` file looking fine is not sufficient — LaTeX page-break decisions are unpredictable
and commonly produce broken layouts (orphaned job titles separated from their bullets, bullet fonts not matching body
text). Compile the document and visually verify the PDF before presenting.

### 6a. Compile

Read `04-cv-templates.md` first — if an `ACTIVE-TEMPLATE` block is present at the top, use the compile command, engine,
and page limit it (and its linked manifest) specify instead of the defaults below.

**Stock default** (no active template override), using the `tools/build-cv/run` script:

```bash
tools/build-cv/run applications/<company>/curriculum.tex applications/<company>/curriculum.pdf
```

- The script builds a Docker image and compiles the `.tex` file inside it, copying the resulting PDF to the given
  output path.

If the compile fails, fix the error and re-compile until clean.

### 6b. Inspect layout

Read the PDF via the Read tool and verify:

**CV (`applications/<company>/curriculum.pdf`):**

- [ ] Exactly the page count required by the active template (or the stock default's 1 page if none is active) — not
      more, not fewer
- [ ] No orphaned entry titles — a job/education title line must never sit alone at the bottom of a page with its
      bullets pushed to the next page. This is the most common failure.
- [ ] Section headings are not isolated at the top of a page with only 1-2 lines below
- [ ] No awkward whitespace gaps

### 6c. Iterate until clean

If the layout has problems, edit the `.tex` file and recompile. Common fixes (see `04-cv-templates.md` — the active
template's own tailoring notes if one is set, otherwise the stock guidance below — for full details):

- **Substantial content overflowing the page limit:** cut content using **relevance-weighted cutting** (see
  `04-cv-templates.md` → "Relevance-weighted cutting"). Score each candidate line by (a) relevance to THIS posting's
  keywords and responsibilities, (b) uniqueness (is it duplicated elsewhere?), (c) narrative load. Cut the
  lowest-total-score line first, regardless of section. Do NOT mechanically apply a static section-based priority
  order — an older-role bullet that hits posting keywords is worth more than a recent-role bullet that does not.
- **Never** compress margins, geometry scale, or the template's tuned `\vspace` values to force a fit — cut content
  instead.
- **Stock moderncv only** (skip if a custom template is active — its own manifest names its pitfalls instead):
  - Orphaned `\cventry` title: `\usepackage{needspace}` in preamble, then `\needspace{5\baselineskip}` immediately
    before the problematic `\cventry`
  - CV spills to page 3 with only a trailing section: `\enlargethispage{2-3\baselineskip}` before a late section

Do not proceed to Step 7 until the PDF passes inspection.

### 6d. ATS & keyword verification (CV)

An ATS parser reads the PDF's embedded **text layer**, not the rendered page — a CV that passed visual inspection can
still extract as garbage (icon glyphs where the contact details should be, scrambled reading order in multi-column
layouts). This step verifies what a parser actually sees.

`pdftotext` (poppler) is not assumed to be on the host — use the dedicated `tools/pdftotext/run` script, which builds
its own Docker image and runs `pdftotext` inside it, same pattern as `tools/build-cv/run`.

**1. Extract the text layer:**

```bash
tools/pdftotext/run applications/<company>/curriculum.pdf applications/<company>/curriculum.txt
```

Read the `.txt` file.

**2. Parseability checks** on the extracted text:

- [ ] **Text extracted at all**, with no garbage runs: no `(cid:NNN)` markers, no `�` replacement characters, no
      stretches of missing text that are visible in the PDF
- [ ] **Email and phone survive as literal text.** Icon fonts extract as glyph names (the stock template's contact line
      extracts as `MOBILE-ALT [+XX ...] • Envelope [your.email@...]`) — that noise is harmless, but the actual address
      and digits must be present. A contact detail carried only by an icon or a hyperlink target (like the `LinkedIn`
      link text) is invisible to an ATS; the email must be printed as text.
- [ ] **Reading order matches the visual order** — section headings appear in the same sequence as on the page, and
      lines from different sections are not interleaved. The stock banking template is single-column and safe; custom
      templates with sidebars or multi-column layouts are where this breaks.
- [ ] **Dates recognizable** — each role and degree has its years present in the extraction.

Failures here are template-level problems: fix them in the `.tex` (e.g. print the email as text rather than icon-only),
then re-run 6a–6c and re-extract. If a custom template's layout fundamentally scrambles extraction order, tell the user
prominently — they may be trading ATS compatibility for looks.

**3. Keyword coverage.** Reuse the required/preferred keyword list you extracted in Step 1 — do not re-derive it. Match
each keyword against the extracted text, **in the posting's language** (a Danish posting's keywords are matched in
Danish even though the CV is in English — where the CV legitimately covers the concept in English, count it as
synonym-only and note the language difference). Report a table:

| Keyword | Priority           | Status                                                     | Note                            |
| ------- | ------------------ | ---------------------------------------------------------- | ------------------------------- |
| ...     | required/preferred | covered / synonym-only / missing (have it) / missing (gap) | where it appears, or why absent |

- **covered** — the term appears (verbatim or trivial inflection).
- **synonym-only** — the concept is present under a different term. If the posting's exact term is truthfully applicable
  per the profile, prefer the posting's term (ATS keyword matches are often literal).
- **missing (have it)** — the profile shows the candidate genuinely has this skill but the CV never says it: add it
  where it fits naturally, preferring experience bullets (concrete evidence) over the profile statement, then re-run
  6a–6c.
- **missing (gap)** — a genuine gap: leave it missing. **Never stuff keywords.**

**4. Clean up:** delete the extracted `.txt` file.

---

## Step 7: Present Final Output

Run the full verification checklist from `CLAUDE.md` now — this is the **only** verification pass in the workflow.
Re-read the file once here to verify final state on disk matches your mental model after the Step 5 and Step 6 edits.

### Verification Checklist

Report pass/fail for each item in the CLAUDE.md verification checklist (factual accuracy, targeting, consistency,
quality).

### Key Tailoring Decisions

Summarize 3-5 key decisions made to tailor the application:

- What was emphasized and why
- What company-specific angles were incorporated
- What the reviewer suggested that was most impactful
- Any gaps that were acknowledged or reframed

### Files Created

List the files written:

- `applications/<company>/<company>.md`
- `applications/<company>/curriculum.tex`

Tell the user: "Your CV is ready for your review. Open it to check the final output before compiling."
