# CV Templates and Tailoring Guide

## Template

- **Skeleton:** `curriculum/base-curriculum-template.tex` — structural reference with placeholder fields
  (`[YOUR_NAME]`, `[COMPANY]`, etc.)
- **Master reference with real data:** `curriculum/base-curriculum.tex` — use as the source when building targeted
  CVs
- **Output file:** `curriculum-adapted/curriculum_<company>.tex`
- **Compile with:** `tools/build-cv/run` — builds a Docker image with `pdflatex` and compiles inside it, so no host
  LaTeX install is required
- **Extract text layer with:** `tools/pdftotext/run` — same pattern, a dedicated Docker image with `poppler-utils`
- **Fonts:** Latin Modern (`lmodern`), a standard TeX Live package — no bundled font files
- **Page limit:** exactly 1 page

### Custom commands (defined in the template preamble, reuse as-is)

- `\resumeSubheading{Employer}{Location}{Title}{Dates}` - new employer block: bolded employer name + location on one
  line, italic title + dates on the next.
- `\resumeSubSubheading{Title}{Dates}` - a second title at the _same_ employer (e.g. a promotion), no repeated
  employer/location line.
- `\resumeItemListStart` / `\resumeItemPlain{...}` / `\resumeItemListEnd` - bullet list under a role.
- `\resumeSubHeadingListStart` / `\resumeSubHeadingListEnd` - wraps a whole section's entries (Experience, Projects,
  Skills, Languages all use this).

Keep bullets as `\resumeItemPlain`, not nested `itemize`, to preserve the tight `-2pt`/`-5pt` vspace tuning that keeps
this template dense and single-page. Do not touch the `\vspace` values in the custom commands to force a fit — cut
content instead (see Relevance-weighted cutting below).

### Section order (as used in `curriculum/base-curriculum.tex`)

1. Header (name, title, email, LinkedIn, GitHub) via `tabular*`
2. Professional Profile (2-4 sentence summary, tailored per role)
3. Experience (`\resumeSubheading` per employer, `\resumeSubSubheading` per additional title at the same employer)
4. Projects (optional - include only when directly relevant to the posting)
5. Skills (single flat comma-separated line, reordered so the posting's top keywords come first)
6. Languages
7. Education - omit the section entirely if there's nothing to put in it, rather than leaving it empty (commented out
   by default in the skeleton)

The section order is fixed by the skeleton file — don't reorder sections per role type.

### Spacing inside itemize lists (important)

**Do not place `\vspace{...}` between `\item` entries in an `itemize` list.** Even though the source looks symmetric,
this pattern occasionally produces a noticeably oversized gap before a single item: the inter-item `\vspace` creates a
paragraph break that interacts unpredictably with the list's internal `\itemsep`, so LaTeX renders one of the gaps wider
than the rest. Remove the inter-item `\vspace` and let `itemize` use its native uniform spacing.

```latex
% WRONG - intermittently produces an oversized gap before one bullet
\begin{itemize}
\item \textbf{Foo}: ...
\vspace{1pt}
\item \textbf{Bar}: ...
\vspace{1pt}
\item \textbf{Baz}: ...
\end{itemize}

% RIGHT - uniform spacing using the list's native itemsep
\begin{itemize}
\item \textbf{Foo}: ...
\item \textbf{Bar}: ...
\item \textbf{Baz}: ...
\end{itemize}
```

## Section-by-Section Tailoring

### Profile Statement / Elevator Pitch (Best Practice)

This is the most important section to customize. It appears right after the header.

Write 2-4 lines that function as an "elevator pitch": a concise, compelling introduction explaining why you're
qualified for _this specific role_. Focus on what the employer gains from hiring you.

### Skills Section (Best Practice)

Reorder based on the role: the posting's top keywords come first. Keep it a single flat comma-separated line, not a
bulleted breakdown — that is how `curriculum/base-curriculum.tex` renders it.

### Education

- Only include if relevant to the target role — omit the section entirely otherwise (it's commented out by default)
- Keep brief: dates and titles only

### Professional Experience

- Rewrite bullet points to emphasize aspects most relevant to the target role
- Use 2-3 bullets per role, more for the most recent/relevant one
- **Emphasize measurable results** where possible: "Reduced processing time by X%", "Migrated infrastructure to Y"

### Handling Employment Gaps (Best Practice)

If there is a gap in your employment history:

- The gap should be explained matter-of-factly if needed
- Describe how professional development continued during the gap
- Frame as deliberate skill-building and career repositioning

## Compile-and-Inspect Loop (MANDATORY)

After writing the CV and before presenting to the user, always compile and visually inspect the PDF. Iterate until the
layout is clean. Workflow:

1. Run `tools/build-cv/run curriculum-adapted/curriculum_<company>.tex curriculum-adapted/curriculum_<company>.pdf`
2. Read the PDF via the Read tool and visually inspect it
3. Check the page count: must be exactly 1
4. Check for **orphaned entries**: a `\resumeSubheading`/`\resumeSubSubheading` title line must never sit alone with
   its bullets pushed off the page

### Fixing common page-break problems

**Problem: content barely overflows onto a trailing second page (a few lines)**
Add `\enlargethispage{2-3\baselineskip}` before the last section to stretch page 1 by a few lines. This is the standard
LaTeX rescue for near-miss overflows — reserve it for genuine near-misses, not as a substitute for cutting content.

**Problem: 2 pages with significant content on page 2**
Cut content — do not compress geometry or `\vspace`. See "Relevance-weighted cutting" below for the rule.

**Problem: content finishes with noticeable empty space at the bottom**
Restore the highest-relevance item that was previously cut — a CV that ends with a large empty gap looks incomplete.

## ATS Parseability

Most employers run CVs through an ATS before a human sees them, and the ATS reads the PDF's embedded **text layer**,
not the rendered page. A CV can pass visual inspection and still extract as garbage. After the layout passes the
compile-and-inspect loop, verify the text layer:

```bash
tools/pdftotext/run curriculum-adapted/curriculum_<company>.pdf curriculum-adapted/curriculum_<company>.txt
```

What to check in the extraction:

- **Contact details as literal text.** The header prints email, LinkedIn, and GitHub as plain labelled text
  (`Email: ...`, `LinkedIn: ...`), which extracts cleanly. The failure mode to watch for is a contact detail carried
  _only_ by a hyperlink target (e.g. link text reading just "LinkedIn" with the URL not printed) — invisible to an
  ATS. The email address and handles must always appear as printed text.
- **No garbled output.** `(cid:NNN)` markers or `�` characters mean a font is embedded without a Unicode mapping — an
  ATS sees the same garbage.
- **Reading order.** This template is single-column, so extraction order should match visual order. If it doesn't,
  something in the `.tex` is wrong and needs fixing before presenting the CV.
- **Keyword coverage.** Match the posting's required/preferred terms against the extracted text, in the posting's
  language. Prefer the posting's exact term over a synonym when it is truthfully applicable — ATS matching is often
  literal. Never add a keyword the profile does not support.

## Page Budget - Hard 1-Page Limit

The CV **must** fit on exactly 1 page when compiled. Use these content limits as a guide:

| Section           | Max budget                                                           |
| ----------------- | -------------------------------------------------------------------- |
| Profile statement | 2-4 lines                                                            |
| Experience        | 3-4 employers, 2-3 bullets per role                                  |
| Projects          | 0-2 entries, 1 line each (omit if none relevant)                     |
| Skills            | 1 line, comma-separated                                              |
| Languages         | 2-3 entries                                                          |
| Education         | 1-2 entries if included, dates + degree only (omit section if empty) |

**If in doubt, cut rather than squeeze.** Reducing `\vspace` or geometry scale to force-fit content makes the CV look
cramped.

## Relevance-weighted cutting (the right way to shrink a CV)

**Cut by signal, not by section.** Static priority lists ("remove oldest role first, then shorten the earliest
role...") are wrong when a relevant "lower-priority" item is competing with an irrelevant "higher-priority" item. An
older-role bullet that speaks directly to the posting is worth more than a recent-role bullet that does not.

For every candidate line, score two things:

1. **Relevance to THIS posting** — does the line hit a named tool, keyword, or stated responsibility in the job ad?
2. **Uniqueness** — is it the only place this claim appears, or is it duplicated elsewhere in the CV?

Cut the lowest-total-score line first, regardless of which section it sits in.

### Practical order of cuts (easiest → last resort)

1. **Redundancy.** If a skill appears in both the Skills line AND a role bullet, the Skills line is usually the
   cleaner cut — the experience bullet is more concrete evidence.
2. **Profile-statement fluff.** A sentence that just restates what Skills or Experience will already show.
3. **Low-relevance experience bullets.** A bullet about work that does not touch posting keywords, wherever it sits.
4. **Projects section**, if the posting doesn't touch what it demonstrates.
5. **Low-relevance Skills line entries** with zero relevance to the posting.
6. **Older-role bullets**, tightened to 2 per role.
7. **Last-resort structural cut.** Drop a `\resumeSubSubheading` entirely if it's an earlier same-employer title
   already implied by the promotion.

### Pitfalls to avoid

- Do not mechanically cut from the bottom of a static section list without checking relevance. "Cut the oldest role
  first" is wrong if that role is literally about the skill the posting asks for.
- Do not cut to fit if the fit is borderline (a few lines over). Prefer `\enlargethispage{2-3\baselineskip}` on the
  last section for near-misses; reserve content cuts for genuine overflow (content spilling well into page 2).
