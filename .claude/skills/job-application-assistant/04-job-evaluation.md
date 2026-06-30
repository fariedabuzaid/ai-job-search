# Job Evaluation Framework

<!-- SETUP: Skill match areas and career goals are personalized by running /setup -->

## Scoring Dimensions

Evaluate each job posting against these five dimensions:

### 1. Technical Skills Match (0-100)
How well do the required/preferred skills align with the candidate's capabilities?

| Score | Meaning |
|-------|---------|
| 80-100 | Core requirements are primary skills |
| 60-79 | Most requirements match, 1-2 gaps that are learnable |
| 40-59 | Partial match, significant upskilling needed |
| 0-39 | Fundamental mismatch |

**Strong match areas:** scientific ML (simulation-based inference, Bayesian methods, uncertainty quantification, anomaly detection); generative modeling (flow matching, normalizing flows, diffusion); trustworthy/responsible AI (XAI, NN verification); Python/PyTorch/JAX; research design and publication
**Moderate match areas:** large-scale/distributed training of 10B+ param models (recent, via DeepL); LLM/foundation-model engineering; MLOps/deployment infra (Docker, OpenShift, AWS, GCP); leadership/mentoring and team lead
**Weak match areas:** domain-specialized 3D/computer-vision generative modeling; long-tenure people-management; pure DevOps/platform-only roles

### 2. Experience Match (0-100)
Does work history align with what they're looking for?

| Score | Meaning |
|-------|---------|
| 80-100 | Direct experience in the same domain and role type |
| 60-79 | Related experience, transferable skills clear |
| 40-59 | Adjacent experience, would need to make the case |
| 0-39 | Unrelated experience |

**Strong:** AI/ML research scientist, applied/senior ML researcher, scientific ML in industrial settings, research group lead, university teaching/mentoring
**Moderate:** ML engineer / research engineer (production-leaning), foundation-model / LLM research, research manager / computational science lead
**Entry-level:** pure 3D/CV product roles, long-horizon people-management-only roles

### 3. Behavioral/Culture Fit (0-100)
Does the role and company culture match the behavioral profile?

| Score | Meaning |
|-------|---------|
| 80-100 | Culture strongly matches behavioral preferences |
| 60-79 | Mixed signals but mostly compatible |
| 40-59 | Some friction areas |
| 0-39 | Significant culture mismatch |

**Red flags to research:** Department disorganization, work dominated by maintenance over development, poor chemistry with leadership, culture mismatches. Check reviews, media coverage, LinkedIn connections, and network contacts for insider perspective.

### 4. Location & Logistics (Pass/Fail + Notes)
Location is **flexible** for this candidate - it is not a deal-breaker. Treat as:
- Munich / Germany (on-site or hybrid): PASS (home base, comfortable)
- Spain (Madrid/Barcelona) on-site or relocation: PASS (a strong plus)
- Remote (EU-wide): PASS
- Relocation elsewhere in Europe: PASS for a strong/research-substantive role (note relocation effort)
- Outside Europe / requires non-EU relocation: FLAG (discuss with user)
- Frequent international travel: FLAG (discuss with user)

### 5. Career Alignment & Motivation (0-100)
Does this role advance career goals and contain tasks that energize?

| Score | Meaning |
|-------|---------|
| 80-100 | Strongly aligned with career direction, clear growth path |
| 60-79 | Good role but only partially aligned with long-term goals |
| 40-59 | Decent job but doesn't build toward career goals |
| 0-39 | Dead end or backwards step |

**Career goals:**
- Research Scientist (AI/ML) at a strong research lab or R&D org - generative modeling, scientific ML, foundation models
- Applied / Senior ML Researcher bridging rigorous methods and real-world impact (trustworthy AI, uncertainty, evaluation)
- Growth toward Lead / Research Manager / computational-science-lead roles over time

**Motivation filter:** Evaluate not just whether he *can* do the tasks, but whether the tasks will *energize* him. Consider:
- Tasks that energize: framing open research questions and designing careful experiments; cross-disciplinary / domain ML with domain experts (simulation, sensor, engineering, scientific data); teaching, mentoring, and technical communication
- Tasks that drain: pure maintenance/ops with no research; rigid process-heavy work with no room to frame problems; purely managerial roles with no technical substance
- Non-task factors: leadership style, department culture, company values, degree of autonomy

**Hard filter:** The role **must be research-substantive**. Down-score or skip pure MLOps/maintenance roles with little novel modeling or research, regardless of other scores.

**Life situation alignment:**
- **Availability**: between roles as of June 2026 (most recently DeepL, Jan-Jun 2026); available to start soon and actively seeking. Still weights fit and research substance heavily, but the prior "no urgency, skip lateral moves" bias no longer applies
- **Flexibility**: location-flexible - Munich comfortable, Spain (Madrid/Barcelona) a strong plus, remote (EU-wide) fine, relocation elsewhere in Europe acceptable for the right role
- **Professional development**: values scientific quality, publication opportunities, and room to grow into research leadership

### 6. Salary Benchmark (Optional)

If the salary lookup tool is configured (`salary_data.json` exists), look up the company:
```
python salary_lookup.py "<Company Name>" --json
```

If a city is known from the posting, add `--city "<City>"` to narrow results.

Present findings as:
```
### Salary Benchmark
| Metric | Value |
|--------|-------|
| [Category] index | XX.X (+/-X.X% vs baseline) |
| Overall index | XX.X (+/-X.X% vs baseline) |
```

Interpret results relative to the baseline defined in the data file's metadata. For index-based data, higher typically means above-market compensation.

If the salary tool is not configured, skip this section.

## Output Format

Present the evaluation as:

```
## Job Fit Evaluation: [Role] at [Company]

| Dimension | Score | Notes |
|-----------|-------|-------|
| Technical Skills | XX/100 | [brief note] |
| Experience Match | XX/100 | [brief note] |
| Behavioral Fit | XX/100 | [brief note] |
| Location | PASS/FAIL | [brief note] |
| Career Alignment | XX/100 | [brief note] |

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
- **Good Fit** (60-74): Apply, address gaps in cover letter
- **Moderate Fit** (45-59): Consider carefully, discuss with user
- **Weak Fit** (30-44): Probably skip unless strategic reasons
- **Poor Fit** (<30): Skip

## Pre-Application: Call the Employer (Best Practice)

Before writing the application, consider whether the candidate should call the contact person listed in the posting. **Only call if there are substantive questions** - never call just to "be remembered."

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
- Reference the conversation naturally in the cover letter ("After speaking with [name], I was especially drawn to...")
