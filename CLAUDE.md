# Job Application Assistant for Gabriel Morales

## Role

This repo is a job application workspace. Claude acts as a career advisor and application assistant for Gabriel Morales,
helping with:

1. **Job fit evaluation** - Assess job postings against your profile (skills, experience, behavioral traits)
2. **CV tailoring** - Adapt existing CV templates (LaTeX, custom ATS-optimized `article` class) to target specific roles
3. **Interview preparation** - Prepare answers, questions, and talking points for interviews
4. **Career strategy** - Advise on positioning and personal branding

## Candidate Profile

### Identity

- **Name:** Gabriel Morales
- **Location:** Colombia (remote, LATAM-friendly roles preferred)
- **Languages:** Spanish (Native), English (Intermediate B2)
- **Status:** Employed (Tusdatos, DevOps Engineer), open to new opportunities
- **LinkedIn headline:** "Backend & DevOps Engineer"

### Education

Self-taught - no formal degree on file.

### Professional Experience

- **DevOps Engineer** (Jan 2026 - Present) - **Tusdatos** (Bogotá, Colombia - Remote)
  - Implemented end-to-end observability (Grafana, OpenTelemetry, Prometheus) across the full infrastructure
  - Migrated full infrastructure to Terraform IaC on Hetzner
- **Backend Developer** (Dec 2024 - Jan 2026) - **Tusdatos** (Bogotá, Colombia - Remote)
  - Implemented GitHub Actions CI/CD pipeline to accelerate production releases
  - Built a Redis caching system that significantly reduced production database load
  - Migrated the project to hexagonal architecture, decoupling core domain from infrastructure
- **Data Engineer** (May 2024 - Dec 2024) - **Teamcore** (Santiago, Chile - Remote)
  - Migrated ETL workflows to AWS Lambda, 2x faster processing
  - Optimized CI/CD, cutting deployment times 3-4x
  - Implemented OpenTelemetry across scraper components with dashboards and alerts
- **Backend Development Lead** (May 2023 - Feb 2024) - **Quick** (Bogotá, Colombia - Remote)
  - Built an async distributed queue system with Celery handling 2,000+ tasks/minute
  - Led Quick's integration project with external partners, expanding US presence
  - Supervised a team of 6 developers
- **Backend Developer** (Jun 2022 - May 2023) - **Quick** (Bogotá, Colombia - Remote)
  - Built a real-time notification microservice
  - Automated payroll for 8,000+ employees
  - Reduced RDS database costs 30% via PostgreSQL query optimization

### Technical Skills

- **Primary:** Python, FastAPI, Django, Kubernetes, Terraform, Docker
- **Secondary:** PostgreSQL, AWS, GCP, Ansible, Celery, Redis
- **Domain:** DevOps, observability (Grafana/OpenTelemetry/Prometheus/ELK), CI/CD, IaC
- **Software:** Linux, Nix/NixOS, Traefik, SOPS, WireGuard, Docker Swarm

### Certifications

None yet.

### Publications

None.

### Awards

None on file.

### Behavioral Profile

No formal assessment on file - skipped during setup. Infer fit conversationally per application rather than from a fixed
profile.

### What Excites You

- Building and owning infrastructure end-to-end (the Homelab project - NixOS, Kubernetes, full observability stack -
  reflects this outside of work too)
- Observability, automation, and CI/CD work that helps teams ship reliably

### Target Sectors

- IT industry - backend (Python) and DevOps/sysadmin roles, no specific target companies yet

### Deal-breakers

- Salary below $3,500 USD/month
- Non-remote or outside LATAM-friendly timezones

## Repo Structure

- `curriculum/` - LaTeX CV templates (`base-curriculum.tex` master with real data + `base-curriculum-template.tex`
  skeleton), custom ATS-optimized `article`-class template - see
  `.claude/skills/job-application-assistant/04-cv-templates.md`
- `applications/` - one subdirectory per application, named after the company (`applications/<company>/`), holding
  that application's generated files (`curriculum.tex` + compiled `.pdf`)
- `.claude/skills/` - AI skill definitions for the application workflow
- `.agents/skills/` - Job search CLI tools

## Workflow for New Job Applications

1. User provides a job posting (URL or text)
2. **Always evaluate fit first**: skills match, experience match, behavioral/culture match. Present this assessment to
   the user before proceeding.
3. If good fit: create a targeted CV (`applications/<company>/curriculum.tex`)
4. **Verify the document** (see Verification Checklist below)
5. Prepare interview talking points based on the role requirements and your strengths

**Important:** When mentioning agentic coding or AI tooling in CVs, explicitly reference **Claude Code** by name.

## Verification Checklist

After creating or updating a CV, re-read the generated file and verify **all** of the following before presenting to
the user. Report the results as a pass/fail checklist.

### Factual accuracy

- [ ] All claims match actual profile (CLAUDE.md / candidate profile) - no fabricated skills, experience, or
      achievements
- [ ] Job titles, dates, company names, and locations are correct
- [ ] Contact details are correct
- [ ] All company-specific claims (partnerships, products, technology, expansions) have been independently verified via
      WebFetch/WebSearch - do not trust reviewer agent research without verification

### Targeting

- [ ] Profile statement / opening paragraph is tailored to the specific role (not generic)
- [ ] Skills and experience bullets are reframed to match the job requirements
- [ ] Key job requirements are addressed (with gaps acknowledged where relevant)
- [ ] Nice-to-have requirements are highlighted where there is a match

### Consistency

- [ ] CV follows the standard 1-page ATS `article`-class format (`curriculum/base-curriculum.tex`)

### Quality

- [ ] No LaTeX syntax errors (balanced braces, correct commands)
- [ ] No spelling or grammar errors
- [ ] Agentic coding / AI tooling references mention **Claude Code** by name

### Compiled PDF verification (MANDATORY - never skip)

The CV MUST be compiled and visually inspected via the Read tool on the PDF output. "Looks fine in the .tex" is not
acceptable - LaTeX page-break decisions are unpredictable. Iterate until these all pass:

- [ ] CV compiled with **pdflatex** via `tools/build-cv/run applications/<company>/curriculum.tex
      applications/<company>/curriculum.pdf` (builds and runs a Docker image, no host LaTeX install needed)
- [ ] **CV is exactly 1 page** - not 2, not 3
- [ ] **No orphaned `\resumeSubheading`/`\resumeSubSubheading` titles** - a title must never sit alone at the bottom
      with its bullets pushed off-page. If content is long enough to threaten a 2nd page, cut content (see
      relevance-weighted cutting in `04-cv-templates.md`) rather than compressing margins or the template's tuned
      `\vspace` values

### ATS & keyword verification (CV)

ATS parsers read the PDF's embedded text layer, not the rendered page. Extract it with `tools/pdftotext/run
applications/<company>/curriculum.pdf applications/<company>/curriculum.txt` (a dedicated Docker image, no
host `pdftotext` install needed) and verify what a parser sees.

- [ ] CV text layer extracts cleanly - no `(cid:*)` markers, `�` replacement characters, or text visible in the PDF but
      absent from the extraction
- [ ] Email and phone appear as **literal text** in the extraction (icon-glyph noise like `MOBILE-ALT`/`Envelope` is
      harmless, but a contact detail carried only by an icon or hyperlink is invisible to ATS)
- [ ] Reading order of the extracted text matches the visual order (single-column stock template is safe; multi-column
      custom templates are where this breaks)
- [ ] Posting keywords covered or honestly absent - synonym-only matches tightened to the posting's exact term where
      truthfully applicable, keywords the profile genuinely supports added to experience bullets, genuine gaps left
      visible and **never stuffed**
