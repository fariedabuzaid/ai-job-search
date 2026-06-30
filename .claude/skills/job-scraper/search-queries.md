# Search Queries for Job Scraper

This workspace targets the **German** job market (home base: Munich) and **European** roles,
**especially Spain** (Madrid / Barcelona). Remote (EU-wide) is fine and relocation elsewhere in
Europe is acceptable for a strong, research-substantive role. Prefer the dedicated CLI search
skills for covered markets; use the `site:` WebSearch queries below for portals without an API.

**Hard filter:** roles must be **research-substantive**. Skip pure MLOps/maintenance roles with
little novel modeling or research.

## Preferred: CLI search skills (live APIs, structured data)

| Skill | Market | Auth | Best for |
|-------|--------|------|----------|
| `arbeitsagentur-search` | 🇩🇪 Germany | none | Broadest German coverage (official federal agency) |
| `adzuna-search` | 🇩🇪🇪🇸 + 🇫🇷🇮🇹🇳🇱🇦🇹🇵🇱… | free key | Spain + pan-European in one API; salary data |
| `arbeitnow-search` | 🇩🇪🇦🇹🇨🇭 + remote EU | none | English-friendly tech/AI/data; visa-sponsor roles |
| `linkedin-search` | 🌍 any country | none | Broad professional coverage; roles posted only on LinkedIn |

Run, for example:
```bash
node .agents/skills/arbeitsagentur-search/cli/src/cli.mjs search -q "Research Scientist" --where "München" --radius 30 --since 14 --format table
node .agents/skills/arbeitsagentur-search/cli/src/cli.mjs search -q "Machine Learning Researcher" --where "München" --radius 30 --since 14 --format table
node .agents/skills/adzuna-search/cli/src/cli.mjs search --country es -q "Research Scientist machine learning" --since 14 --format table
node .agents/skills/adzuna-search/cli/src/cli.mjs search --country de -q "Applied Scientist generative AI" --since 14 --format table
node .agents/skills/arbeitnow-search/cli/src/cli.mjs search -q "machine learning research" --remote --format table
node .agents/skills/linkedin-search/cli/src/cli.mjs search --what "Research Scientist" --where "Germany" --since 14 --format table
node .agents/skills/linkedin-search/cli/src/cli.mjs search --what "Machine Learning" --where "Spain" --since 14 --format table
```
For a Germany-vs-Spain sweep with Adzuna, run the same query once per `--country de` and `--country es`.
LinkedIn takes a free-text `--where` (e.g. `Germany`, `Munich`, `Madrid`) and `--since <days>`; add `--remote` for remote-only.

## Fallback: WebSearch `site:` portals (no API)

Germany:
- **stepstone.de** — largest German commercial board
- **xing.com/jobs** — German professional network (the local LinkedIn)
- **stellenanzeigen.de**, **jobware.de**, **monster.de**
- **indeed.de** — aggregator

Spain:
- **infojobs.net** — largest Spanish board
- **tecnoempleo.com** — Spanish tech/IT jobs
- **indeed.es**, **infoempleo.com**

Pan-European / cross-border:
- **linkedin.com/jobs** — prefer the `linkedin-search` CLI (structured cards, no auth); use `site:linkedin.com/jobs` WebSearch only to widen further
- **glassdoor.com** / **indeed.de** / **indeed.es** — **search-engine discovery only.** Both 403-block automated requests, so there is no CLI; find postings via `site:` queries, then WebFetch the individual posting page (which may itself be blocked — fall back to the search snippet)
- **eures.europa.eu** — official EU job-mobility portal
- Company career pages (Mistral, DeepL, SpAItial, SAP, NielsenIQ, Sanofi, …) via Google `site:` filters

## Query Categories

Queries are grouped by priority. Combine each with location terms where the site supports it.
German keywords often work better on `.de` sites; Spanish on `.es` sites.

### Priority 1: Research Scientist (AI/ML)

Strongest and most desired direction - generative modeling, scientific ML, foundation models.

```
site:stepstone.de "Research Scientist" (AI OR "machine learning") München
site:xing.com/jobs "Research Scientist" "machine learning" Deutschland
site:linkedin.com/jobs "Research Scientist" "generative AI" Deutschland
site:linkedin.com/jobs "Research Scientist" "machine learning" Spain
site:infojobs.net "Research Scientist" "machine learning" España
```

### Priority 2: Scientific ML / generative modeling / trustworthy AI (domain expertise)

```
site:stepstone.de ("generative AI" OR "Bayesian" OR "uncertainty quantification") München OR remote
site:linkedin.com/jobs ("normalizing flows" OR "diffusion models" OR "simulation-based inference") Europe
site:linkedin.com/jobs ("trustworthy AI" OR "responsible AI" OR "explainable AI") Deutschland
site:tecnoempleo.com "machine learning" investigación
site:infojobs.net "deep learning" "research" Madrid OR Barcelona
```

### Priority 3: Applied / Senior ML Researcher & ML/Research Engineer (adjacent)

```
site:stepstone.de ("Applied Scientist" OR "Senior Machine Learning") München
site:linkedin.com/jobs "Applied Research Scientist" "deep learning" Europe
site:linkedin.com/jobs ("Machine Learning Engineer" OR "Research Engineer") "LLM" Deutschland
site:linkedin.com/jobs ("Research Manager" OR "Lead Research Scientist") "machine learning" Europe
```

### Priority 4: Remote / English-friendly / foundation-model labs (wider net)

```
site:linkedin.com/jobs "Research Scientist" "machine learning" remote Europe
"Research Scientist" "foundation models" "visa sponsorship" Germany
("Research Scientist" OR "Applied Scientist") "machine learning" English speaking Munich OR Berlin
"machine learning researcher" remote ("EU" OR "Europe")
```

## Location Filter

Verify each job sits in an acceptable area.

**Germany (home base):**
- Munich and surrounding areas — ideal (home base)
- Anywhere in Germany (on-site or hybrid) — acceptable
- Hybrid roles requiring occasional Munich presence — ideal
- On-site-only outside Germany within the EU — acceptable for a strong role (note relocation)

**Spain / wider Europe:**
- Madrid, Barcelona — strong plus (relocation acceptable)
- Remote-from-Germany / remote EU-wide roles — acceptable
- Relocation elsewhere in Europe — acceptable for a research-substantive role; note work-permit/language constraints
- Non-EU relocation — flag for discussion, not auto-accept

## Date Filter

Only include jobs posted within the last 14 days, or with an application deadline that has not yet
passed. If a posting date cannot be determined, include it but flag as "date unknown".

## Adapting Queries

If the user specifies a focus area, select queries from the matching category and also generate
2-3 custom queries for that focus. For example:
- "/scrape spain" -> bias toward `adzuna-search --country es`, infojobs.net, tecnoempleo.com
- "/scrape generative" -> Priority 2 queries + custom flow/diffusion-specific queries
- "/scrape remote" -> Priority 4 queries + `arbeitnow-search --remote`