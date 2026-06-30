# /apply - Drafter-Reviewer Job Application Workflow

You are orchestrating a two-agent job application workflow. The job posting is provided below as `$ARGUMENTS` (either a URL or pasted text).

Follow these steps **exactly in order**. Do not skip steps.

**Token-efficiency rules for this workflow:**
- Never re-Read a file whose contents are already in your context from an earlier step. If you read it in Step 1, it is still available in Step 2.
- When dispatching the reviewer agent, pass draft content **inline in the agent prompt** rather than asking the agent to Read files you already have in memory.
- Run the full verification checklist exactly once, at the end (Step 6). The reviewer focuses on content critique, not verification.
- Step 5 (compile and inspect PDFs) is mandatory and non-skippable — LaTeX page-break decisions are unpredictable, and `.tex` files that look fine often produce broken PDFs (orphaned entry titles, cover letters spilling to page 2, bullet fonts mismatching).

---

## Output location (folder-per-application schema)

Each application lives in its own folder:

```
applications/<Company>/<YYYY>_<MM>_<position_slug>/
    cv.tex            cv.pdf
    cover_letter.tex  cover_letter.pdf
    job_description.md   (posting text + link to the original advertisement)
```

- `<Company>` is the readable company name (e.g. `NVIDIA`, `Autodesk`).
- `<YYYY>_<MM>` is the current year and month.
- `<position_slug>` is the role in lowercase with underscores (e.g. `senior_research_scientist`).
- The CV master reference is `cv/main_example.tex`. The cover letter shares `cover_letters/cover.cls` and `cover_letters/OpenFonts/` (the build script links these in automatically — do not copy them per folder).

---

## Step 0: Parse Input

- If `$ARGUMENTS` looks like a URL, use `WebFetch` to retrieve the job posting content, and keep the URL as the **original advertisement link**.
- If it is pasted text, use it directly (ask the user for the original URL if not provided).
- Extract: **company name**, **role title**, **department** (if mentioned), **location**, and **language** of the posting (German, Spanish, or English).
- Decide the application folder path `applications/<Company>/<YYYY>_<MM>_<position_slug>/` and create it.
- Write `job_description.md` into that folder now: the posting text (or a faithful summary), the original advertisement URL, the source it was surfaced from, and the capture date.
- Store these for use throughout the workflow.

---

## Step 1: DRAFTER - Evaluate Fit

Read the evaluation framework:
- `.claude/skills/job-application-assistant/04-job-evaluation.md`
- `.claude/skills/job-application-assistant/01-candidate-profile.md`

Using the framework from `04-job-evaluation.md`, evaluate the job posting against the candidate's profile. If the salary lookup tool is configured, run:

```bash
python salary_lookup.py "<Company Name>" --json
```

If the posting specifies a city, add `--city "<City>"` to narrow results. Parse the JSON output and include the salary benchmark in the evaluation. If the tool is not configured or returns an error, skip the salary benchmark.

Present the evaluation to the user with:

1. **Skills match** - which required/preferred skills match vs. gaps
2. **Experience match** - how work history maps to the role
3. **Behavioral/culture match** - how behavioral profile fits the role/company culture
4. **Salary benchmark** - salary index for the company (if available)
5. **Overall fit score** and recommendation (strong fit / moderate fit / weak fit)

After presenting the evaluation, ask the user:
> "Should I proceed with drafting the CV and cover letter for this role?"

**If the user says no, stop here.** If yes, continue to Step 2.

---

## Step 2: DRAFTER - Draft CV + Cover Letter

You already have `01-candidate-profile.md` and `04-job-evaluation.md` in context from Step 1. **Do not re-read them.**

Read only the reference files you do not yet have:
- `.claude/skills/job-application-assistant/03-writing-style.md`
- `.claude/skills/job-application-assistant/05-cv-templates.md`
- `.claude/skills/job-application-assistant/06-cover-letter-templates.md`

Also read concrete structural references (one of each is enough):
- `cv/main_example.tex` — the master CV (already tailored with real profile data and the preferred formatting: icon header, no filler References section, individual award bullets)
- A prior `applications/*/*/cover_letter.tex` if one exists, otherwise the structure in `06-cover-letter-templates.md`

### CV (`applications/<Company>/<YYYY>_<MM>_<position_slug>/cv.tex`)
- Always in **English**
- Follow the moderncv/banking format from `05-cv-templates.md` and `cv/main_example.tex`
- Tailor the profile statement and experience bullets to the specific role
- Reframe skills and achievements to match job requirements
- Keep to 2 pages

### Cover Letter (`applications/<Company>/<YYYY>_<MM>_<position_slug>/cover_letter.tex`)
- **Match the language of the job posting** (German posting -> German cover letter, Spanish -> Spanish, English -> English)
- Follow the structure from `06-cover-letter-templates.md`
- Use the `cover.cls` template (the build script links it in; reference fonts as `Path = OpenFonts/fonts/...` exactly as in the template)
- Tailor the opening paragraph to the specific role and company
- Address to a named person if available in the posting, otherwise "Dear Hiring Manager" (or equivalent in posting language)
- Keep to approximately one page
- Any mention of agentic coding or AI tooling must reference **Claude Code** by name

Write both files to disk. Keep the exact text of both drafts in working memory — you will pass them inline to the reviewer in Step 3 and revise them in Step 4 without re-reading.

---

## Step 3: REVIEWER - Research & Critique

Use the **Agent tool** to spawn a `general-purpose` reviewer agent. The reviewer gets a fresh context, so pass the drafts **inline in the prompt** below (do not make the reviewer Read them). Scope the reviewer's file reads to content-critique essentials only — the reviewer does not need the LaTeX template files (`05`, `06`) to critique content, since those govern structural/LaTeX concerns the drafter already applied.

Replace `<COMPANY>`, `<ROLE>`, `<APPDIR>`, `<INSERT_JOB_POSTING_TEXT_HERE>`, `<INSERT_CV_DRAFT_HERE>`, and `<INSERT_COVER_LETTER_DRAFT_HERE>` with actual values before dispatching (`<APPDIR>` is the application folder path).

```
You are a hiring manager proxy reviewing a job application. Your job is to make the application as targeted and compelling as possible.

## Your Tasks

### 1. Research the Company
Use WebSearch and WebFetch to research:
- The company's website, mission, and recent news
- The specific department or team (if mentioned in the posting)
- Any recent projects, press releases, or strategic initiatives relevant to the role
- Company culture and values

### 2. Read Reference Materials (content-critique only)
Read these four files — and only these — to ground your critique:
- `.claude/skills/job-application-assistant/01-candidate-profile.md`
- `.claude/skills/job-application-assistant/02-behavioral-profile.md` — use this specifically to check whether the cover letter's voice matches the candidate's natural register.
- `.claude/skills/job-application-assistant/03-writing-style.md`
- `.claude/skills/job-application-assistant/04-job-evaluation.md`

Do NOT read `05-cv-templates.md` or `06-cover-letter-templates.md` — those govern LaTeX structure the drafter already applied and are not needed for content critique.

### 3. Drafts to Review
Both drafts are provided inline below. Do NOT use the Read tool on the draft files — use these exact texts.

<CV_DRAFT file="<APPDIR>/cv.tex">
<INSERT_CV_DRAFT_HERE>
</CV_DRAFT>

<COVER_LETTER_DRAFT file="<APPDIR>/cover_letter.tex">
<INSERT_COVER_LETTER_DRAFT_HERE>
</COVER_LETTER_DRAFT>

### 4. Job Posting
<JOB_POSTING>
<INSERT_JOB_POSTING_TEXT_HERE>
</JOB_POSTING>

### 5. Produce Feedback

Return your feedback in **two parts**:

**Part A — Structured edits (preferred format whenever possible):**
A JSON array of concrete edits the drafter can apply directly without re-reading the files. Each edit is an object:
```json
{
  "file": "<APPDIR>/cv.tex" | "<APPDIR>/cover_letter.tex",
  "old_string": "<exact text currently in the draft>",
  "new_string": "<replacement text>",
  "reason": "<one-line rationale: keyword match / company angle / reframing / style>"
}
```
Only use this format when you can quote the exact `old_string` from the drafts above. Make `old_string` unique — include enough surrounding context so it matches exactly once per file.

**Part B — Narrative suggestions (for judgment calls that are not mechanical edits):**
Prose suggestions grouped by category. Produce each category even if your finding is "no issues" — silence on a category can be mistaken for skipping it.
- **Missed keywords/requirements** — what to add and roughly where, if it cannot be expressed as a clean string replacement
- **Company/department-specific angles** — connections between experience and the company's strategic priorities, based on your research
- **Action-oriented reframing** — identify passive, generic, or low-energy statements and suggest action-oriented rewrites.
- **Tone and style issues** — check against `03-writing-style.md` AND `02-behavioral-profile.md`. Flag any issues with tone, formality, or voice, and specifically flag any mismatch between the letter's voice and the candidate's natural register.

**CRITICAL RULE:** All suggestions must be grounded in actual profile data. Do NOT suggest fabricating skills, experience, or achievements. If a requirement is a gap, say so honestly and suggest how to frame adjacent experience instead.

Do **not** run a verification checklist — the drafter will do that in the final step. Focus on content critique.

Return Part A and Part B together as a single structured message.
```

---

## Step 4: DRAFTER - Revise Based on Feedback

Once the reviewer agent returns its feedback:

1. **Apply Part A (structured edits) directly with the Edit tool.** Do NOT re-read the draft files — you already have them in context from Step 2, and the reviewer's `old_string` values were quoted from that same text. For each edit in the JSON array, call `Edit` with the given `file`, `old_string`, and `new_string`. Skip any whose rationale would require fabricating content.
2. **Apply Part B (narrative suggestions)** using judgment. These need interpretation, not mechanical replacement. Walk through every Part B category the reviewer returned and address it:
   - **Missed keywords/requirements:** add the keyword or capability where it fits naturally. Prefer the experience bullets (concrete evidence) over the profile statement (abstract claim).
   - **Company/department-specific angles:** weave the reviewer's research into the cover letter opening or motivation paragraph. Verify every company claim via WebFetch/WebSearch before including it — do not trust reviewer research at face value.
   - **Action-oriented reframing:** rewrite passive or generic phrasing (CV profile statement, cover letter opening, bullet leads).
   - **Tone and style issues:** apply the writing-style-guide fixes (no em-dashes, no cliches, no apologetic hedging, consistent first-person active voice).
   Use Edit for targeted changes; only re-read a file if an edit fails because the surrounding text has shifted.
3. Do NOT incorporate any suggestion that would fabricate skills or experience. If a posting requirement is a genuine gap, acknowledge it honestly and frame adjacent experience instead.

After all edits are applied, the two files on disk are the final drafts.

---

## Step 5: DRAFTER - Compile & Inspect PDFs (MANDATORY)

**Never skip this step.** The `.tex` files looking fine is not sufficient — LaTeX page-break decisions are unpredictable and commonly produce broken layouts (orphaned job titles separated from their bullets, cover letters spilling to 2 pages, bullet fonts not matching body text). Compile both documents and visually verify the PDFs before presenting.

### 5a. Compile

Use the build helper, which compiles `cv.tex` with lualatex and `cover_letter.tex` with xelatex (linking in the shared `cover.cls` + `OpenFonts` for the cover letter, then cleaning up):

```bash
scripts/build_application.sh applications/<Company>/<YYYY>_<MM>_<position_slug>
```

- CV uses **lualatex** (moderncv; needs `fontawesome5` and `academicons`). If lualatex is unavailable, `pdflatex` also works on TeX Live.
- Cover letter uses **xelatex** — `cover.cls` requires fontspec/xetex.

If either compile fails, fix the error and re-run the script until clean.

### 5b. Inspect layout

Read both PDFs via the Read tool and verify:

**CV (`applications/<Company>/<YYYY>_<MM>_<position_slug>/cv.pdf`):**
- [ ] Exactly 2 pages (not 1, not 3)
- [ ] No orphaned `\cventry` titles — a job/education title line must never sit alone at the bottom of page 1 with its bullets on page 2. This is the most common failure.
- [ ] Section headings are not isolated at the top of page 2 with only 1-2 lines below
- [ ] No awkward whitespace gaps

**Cover letter (`applications/<Company>/<YYYY>_<MM>_<position_slug>/cover_letter.pdf`):**
- [ ] Exactly 1 page
- [ ] Signature block visible, not cut off or pushed to a second page
- [ ] Bullet list font matches surrounding body text (both should be Raleway-Medium)

### 5c. Iterate until clean

If the layout has problems, edit the `.tex` files and re-run the build script. Common fixes (see `05-cv-templates.md` and `06-cover-letter-templates.md` for full details):

- **Orphaned CV entry title:** `\usepackage{needspace}` in preamble, then `\needspace{5\baselineskip}` immediately before the problematic `\cventry`
- **CV spills to page 3 with only a trailing section:** `\enlargethispage{2-3\baselineskip}` before a late section
- **Substantial content on page 3:** cut content using **relevance-weighted cutting** (see `05-cv-templates.md`). Score each candidate line by (a) relevance to THIS posting's keywords, (b) uniqueness, (c) narrative load. Cut the lowest-total-score line first, regardless of section.
- **Cover letter itemize breaks compile or uses wrong font:** close `\lettercontent{}` before the list, wrap the list in `{\raggedright\fontspec[Path = OpenFonts/fonts/raleway/]{Raleway-Medium}\fontsize{11pt}{13pt}\selectfont \begin{itemize}...\end{itemize}\par}`
- **Cover letter `\closing{...\\}` errors with "no line here to end":** drop the trailing `\\` — use `\closing{Kind regards,}` (the command appends its own line break).
- **Cover letter spills to 2 pages:** trim using the same relevance-weighted logic. Never reduce geometry or line spacing.

Do not proceed to Step 6 until both PDFs pass inspection.

### 5d. Build artifacts

The build script already deletes `.aux`, `.log`, `.out` files. The folder should contain only `cv.tex`, `cv.pdf`, `cover_letter.tex`, `cover_letter.pdf`, and `job_description.md`.

---

## Step 6: Present Final Output

Run the full verification checklist from `CLAUDE.md` now — this is the **only** verification pass in the workflow. Re-read both files once here to verify final state on disk matches your mental model after the Step 4 and Step 5 edits.

### Verification Checklist
Report pass/fail for each item in the CLAUDE.md verification checklist (factual accuracy, targeting, consistency, quality).

### Key Tailoring Decisions
Summarize 3-5 key decisions made to tailor the application:
- What was emphasized and why
- What company-specific angles were incorporated
- What the reviewer suggested that was most impactful
- Any gaps that were acknowledged or reframed

### Files Created
List the files written:
- `applications/<Company>/<YYYY>_<MM>_<position_slug>/cv.tex` (+ `cv.pdf`)
- `applications/<Company>/<YYYY>_<MM>_<position_slug>/cover_letter.tex` (+ `cover_letter.pdf`)
- `applications/<Company>/<YYYY>_<MM>_<position_slug>/job_description.md`

Tell the user: "Both files are ready for your review. Open them to check the final output before compiling."