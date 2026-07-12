---
name: job-application-assistant
description: >
  Assists with job applications: evaluating job postings, tailoring CVs,
  and preparing for interviews. Triggers on keywords like: job posting, job application, CV,
  resume, interview prep, job fit, career, application, apply, ansøgning, stilling
allowed-tools: Read, Glob, Grep, WebFetch, WebSearch, Edit, Write, AskUserQuestion
---

# Job Application Assistant

---

## Workflow

When the user provides a job posting (URL or text), follow this workflow:

### Step 1: Research & Evaluate Fit

- Fetch the job posting content (use WebFetch for URLs)
- Analyze the posting for required competencies, keywords, and priorities
- Research the company (website, LinkedIn, mission, recent news)
- Score the posting against the candidate's profile using the framework in `03-job-evaluation.md`
- Present the evaluation table and verdict
- Suggest whether the candidate should call the employer before applying (see `03-job-evaluation.md` for guidance)
- Ask the user if they want to proceed with an application

### Step 2: Tailor CV

- Read `curriculum/base-curriculum.tex` (real data) and `curriculum/base-curriculum-template.tex` (skeleton) as a
  starting point
- Follow the writing style rules in `02-writing-style.md` (critical: no em-dashes, no cliches)
- Follow the guidelines in `04-cv-templates.md`
- Create `curriculum-adapted/curriculum_<company>.tex` with tailored content
- Adjust: profile statement, skills section, experience bullet emphasis, section order

## Reference Files

| File                           | Purpose                                              |
| ------------------------------ | ---------------------------------------------------- |
| `01-candidate-profile.md`      | Education, experience, skills, publications, awards  |
| `02-writing-style.md`          | Tone, structure, do's and don'ts                     |
| `03-job-evaluation.md`         | Scoring framework for job fit                        |
| `04-cv-templates.md`           | LaTeX CV structure and tailoring rules               |

---

## Quick Commands

The user may also ask for individual steps without the full workflow:

- "Evaluate this job posting" - Step 1 only
- "Write a CV for [company]" - Step 2 only
- "What jobs should I look for?" - Career strategy discussion using profile + evaluation framework
