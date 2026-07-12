# Job Evaluation Framework

<!-- SETUP: Skill match areas and career goals are personalized by running /setup -->

## Scoring Dimensions

Evaluate each job posting against these five dimensions:

### 1. Technical Skills Match (0-100)

How well do the required/preferred skills align with the candidate's capabilities?

| Score  | Meaning                                              |
| ------ | ---------------------------------------------------- |
| 80-100 | Core requirements are primary skills                 |
| 60-79  | Most requirements match, 1-2 gaps that are learnable |
| 40-59  | Partial match, significant upskilling needed         |
| 0-39   | Fundamental mismatch                                 |

**Strong match areas:** Python (FastAPI, Django), Kubernetes, Docker, Terraform, CI/CD (GitHub Actions), observability
(Prometheus, Grafana, OpenTelemetry), Linux systems administration **Moderate match areas:** AWS, GCP, PostgreSQL,
Ansible, Celery/async task processing, Redis **Weak match areas:** Formal education/degree requirements (self-taught, no
degree on file), languages beyond Python (Go, Java, etc.), frontend

### 2. Experience Match (0-100)

Does work history align with what they're looking for?

| Score  | Meaning                                            |
| ------ | -------------------------------------------------- |
| 80-100 | Direct experience in the same domain and role type |
| 60-79  | Related experience, transferable skills clear      |
| 40-59  | Adjacent experience, would need to make the case   |
| 0-39   | Unrelated experience                               |

**Strong:** Backend development (Python/FastAPI/Django), DevOps engineering, observability implementation, IaC/Terraform
**Moderate:** Data engineering (ETL pipelines), team leadership (led a team of 6), sysadmin/Linux administration
**Entry-level:** Formal SRE/platform engineering titles, security-focused roles, large-scale distributed systems beyond
current scale

### 3. Behavioral/Culture Fit (0-100)

Does the role and company culture match the behavioral profile?

| Score  | Meaning                                         |
| ------ | ----------------------------------------------- |
| 80-100 | Culture strongly matches behavioral preferences |
| 60-79  | Mixed signals but mostly compatible             |
| 40-59  | Some friction areas                             |
| 0-39   | Significant culture mismatch                    |

**Red flags to research:** Department disorganization, work dominated by maintenance over development, poor chemistry
with leadership, culture mismatches. Check reviews, media coverage, LinkedIn connections, and network contacts for
insider perspective.

### 4. Location & Logistics (Pass/Fail + Notes)

- Within commute range: PASS
- Remote with occasional office: PASS
- Requires relocation: FAIL (deal-breaker)
- Frequent international travel: FLAG (discuss with user)

### 5. Career Alignment & Motivation (0-100)

Does this role advance career goals and contain tasks that energize?

| Score  | Meaning                                                   |
| ------ | --------------------------------------------------------- |
| 80-100 | Strongly aligned with career direction, clear growth path |
| 60-79  | Good role but only partially aligned with long-term goals |
| 40-59  | Decent job but doesn't build toward career goals          |
| 0-39   | Dead end or backwards step                                |

**Career goals:**

- Backend (Python) roles at IT companies
- DevOps / sysadmin roles at IT companies
- Remote, LATAM-friendly employers

**Motivation filter:** Evaluate not just whether you _can_ do the tasks, but whether the tasks will _energize_ you.
Consider:

- Tasks that energize: owning infrastructure end-to-end, observability/monitoring work, CI/CD and automation, backend
  API/service development
- Tasks that drain: not yet established - ask the candidate if a posting is borderline
- Non-task factors: leadership style, department culture, company values, degree of autonomy

**Life situation alignment:** Consider personal constraints:

- **Security**: Currently employed (Tusdatos); minimum acceptable salary is $3,500 USD/month - treat postings below this
  as a deal-breaker unless the candidate says otherwise
- **Flexibility**: Remote-only, LATAM-friendly timezones (based in Colombia)
- **Professional development**: Self-taught background - growth into more senior backend/DevOps roles is a priority; no
  formal degree, so weigh postings that hard-require one accordingly

## Output Format

Present the evaluation as:

```text
## Job Fit Evaluation: [Role] at [Company]

| Dimension        | Score     | Notes        |
| ---------------- | --------- | ------------ |
| Technical Skills | XX/100    | [brief note] |
| Experience Match | XX/100    | [brief note] |
| Behavioral Fit   | XX/100    | [brief note] |
| Location         | PASS/FAIL | [brief note] |
| Career Alignment | XX/100    | [brief note] |

**Overall Score: XX/100** (weighted average of scored dimensions)

### Verdict: [Strong Fit / Good Fit / Moderate Fit / Weak Fit / Poor Fit]

### Key Strengths for This Role

- [bullet points]

### Gaps to Address

- [bullet points]

### Recommendation

[1-2 sentences: apply/skip/apply with caveats]

### Company Research Checklist

- [ ] Checked company website (mission, values, recent news)
- [ ] Checked review sites (Glassdoor, Jobindex, etc.)
- [ ] Checked LinkedIn for team size, recent hires, connections
- [ ] Checked media for restructuring, growth, or workplace issues
- [ ] Identified network contacts who may know the team/manager
```

## Weighting

- Technical Skills: 30%
- Experience Match: 25%
- Behavioral Fit: 15%
- Career Alignment: 30%

(Location is pass/fail, not weighted)

## Thresholds

- **Strong Fit** (75+): Definitely apply, tailor everything
- **Good Fit** (60-74): Apply, address gaps in the CV
- **Moderate Fit** (45-59): Consider carefully, discuss with user
- **Weak Fit** (30-44): Probably skip unless strategic reasons
- **Poor Fit** (<30): Skip

## Pre-Application: Call the Employer (Best Practice)

Before writing the application, consider whether the candidate should call the contact person listed in the posting.
**Only call if there are substantive questions** - never call just to "be remembered."

### When to Suggest Calling

- The posting has unclear or ambiguous requirements
- It's unclear which competencies are essential vs. nice-to-have
- The role description is vague about day-to-day tasks
- There's a named contact person who invites questions

### Good Questions to Ask

- "What are the primary challenges in this role?"
- "How is time typically divided across the listed responsibilities?"
- "Which competencies are most critical for success in this position?"
- "What does success look like in the first 6-12 months?"

### Rules for the Call

- Prepare a 30-second "elevator pitch" about your background in case they ask
- The call's purpose is **gathering information**, not delivering a pitch
- Take notes - use what you learn to tailor the application
- Reference the conversation naturally when it comes up in an interview ("After speaking with [name], I was especially
  drawn to...")
