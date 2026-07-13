# /setup - Profile Onboarding

You are running the onboarding setup for this job application workspace. Your goal is to populate the candidate's
profile data so the `/apply` workflow works with real information instead of placeholders.

Follow these steps **in order**. Step 1 (CV import) feeds Step 2 (file generation) and Step 3 (confirmation).

---

## Step 0: Welcome

Welcome the user:

> **Welcome to the job application workspace setup!**
>
> I'll help you build your candidate profile so Claude can evaluate job postings, tailor CVs, and prepare you for
> interviews.
>
> Paste or @-mention your CV/resume and I'll take it from there, asking follow-up questions for anything it doesn't
> cover.

Wait for the user to provide a CV/resume.

---

## Step 1: CV Import

Once the user provides a CV/resume (pasted or @-mentioned):

1. Read the document thoroughly.
2. Extract all structured information: name, contact, education, experience, skills, certifications, publications,
   awards.
3. Present a summary of what was extracted.
4. Ask follow-up questions for what a CV never covers: career goals and target sectors, deal-breakers and
   must-haves (salary floor, remote/location, timezone).
5. Proceed to Step 2 (file generation).

---

## Step 2: Generate Profile Files

Once data collection is complete, write the following files. Read each existing one first - if it already has
content the user didn't just provide, merge in the new answers rather than blindly overwriting.
`curriculum/base-curriculum.tex` (file 4 below) is the exception: it does not exist yet, so it is created fresh.

### 1. Update `CLAUDE.md`

Replace the `## Candidate Profile` section (from `### Identity` through `### Deal-breakers`) with the collected
data, keeping the existing subsection structure. Leave the rest of the file (Role, Repo Structure, Workflow,
Verification Checklist) untouched.

### 2. Update `.claude/skills/job-application-assistant/01-candidate-profile.md`

Write the full structured profile, keeping the file's existing section structure: Identity (with contact details and
constraints), Education, Professional Experience (per-role bullets), Independent Projects, Technical Skills (by
category), Publications, Awards.

### 3. Update `.claude/skills/job-application-assistant/03-job-evaluation.md`

Replace only the personalized calibration content; keep the scoring framework, weights, and thresholds intact:

- **Strong/Moderate/Weak match areas** (Technical Skills Match section) - from the candidate's actual skills
- **Strong/Moderate/Entry-level** (Experience Match section) - from their actual work history
- **Career goals** (Career Alignment section) - from their target roles/sectors
- **Life situation alignment** (Security/Flexibility/Professional development) - from their deal-breakers and
  constraints

### 4. Create `curriculum/base-curriculum.tex`

This file does not exist until setup creates it. Read `curriculum/base-curriculum-template.tex` for the LaTeX
structure and custom commands, then write `curriculum/base-curriculum.tex` as a copy of it with the candidate's
actual name, contact info, a profile statement, and their experience/projects/skills/languages entries - this file
becomes the master reference `/apply` draws from for every tailored CV.

`.claude/skills/job-application-assistant/02-writing-style.md` and `04-cv-templates.md` are static reference guides,
not candidate data - setup never modifies them.

---

## Step 3: Confirm & Next Steps

Present a summary:

> **Setup complete!** Here's what was generated:
>
> - `CLAUDE.md` - Your candidate profile
> - `.claude/skills/job-application-assistant/01-candidate-profile.md` - Structured profile detail
> - `.claude/skills/job-application-assistant/03-job-evaluation.md` - Personalized evaluation calibration
> - `curriculum/base-curriculum.tex` - Your master CV
>
> **Try it out:**
>
> - Run `/apply` with a job posting URL or pasted text to see the full application workflow
> - Run `/setup` again anytime (with an updated CV) to refresh your profile

---

## Design Principles

- One flow: CV import, then follow-up questions for what a CV can't cover.
- Synthesize answers into structured formats - the user does not need to know markdown or LaTeX.
- `02-writing-style.md` and `04-cv-templates.md` are never touched - they are static guidance, not per-candidate
  data.
- At the end, suggest running `/apply` with a test job posting.
