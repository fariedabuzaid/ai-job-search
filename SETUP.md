# Setup Guide

Step-by-step instructions for getting the AI Job Search framework running.

## 1. Prerequisites

### Claude Code

Install Claude Code (Anthropic's CLI for Claude):

```bash
npm install -g @anthropic-ai/claude-code
```

You'll need an Anthropic API key or a Claude Pro/Team subscription. See the [Claude Code docs](https://docs.anthropic.com/en/docs/claude-code) for details.

### Python

Python 3.10+ is required for the salary lookup tool. Check with:

```bash
python --version
```

### Node.js (for job search tools)

The German/European job-search CLIs are zero-dependency Node scripts. Node 18+ is required (for built-in `fetch`). Check with:

```bash
node --version
```

No `npm install` is needed for these tools. (The dormant Danish CLIs under `.agents/skills/` were written for [Bun](https://bun.sh) — only install Bun if you intend to revive them.)

### Adzuna API key (pan-European search — optional but recommended)

The `adzuna-search` tool covers Germany **and** Spain (and 16 more countries) through one API. It needs a free key:

1. Sign up at [developer.adzuna.com](https://developer.adzuna.com) and create an app.
2. Export the credentials so every search can see them:
   ```bash
   echo 'export ADZUNA_APP_ID=your_app_id'  >> ~/.bashrc
   echo 'export ADZUNA_APP_KEY=your_app_key' >> ~/.bashrc
   source ~/.bashrc   # or open a new terminal
   ```
   (Use `~/.zshrc` for zsh.) `/setup` can walk you through this too.

The `arbeitsagentur-search` (Germany) and `arbeitnow-search` (English-friendly tech / remote EU) tools need **no** key.

### LaTeX (for compiling CVs and cover letters)

Install a LaTeX distribution to compile the generated `.tex` files to PDF:

- **Windows:** [MiKTeX](https://miktex.org/download)
- **macOS:** [MacTeX](https://tug.org/mactex/)
- **Linux:** `sudo apt install texlive-full` or `sudo dnf install texlive-scheme-full`

The CV compiles with `lualatex` (pdflatex often fails on modern MiKTeX installs with `fontawesome5` font-expansion errors). The cover letter compiles with `xelatex` because `cover.cls` requires `fontspec` for its custom Lato/Raleway fonts.

## 2. Fork and clone

```bash
gh repo fork fariedabuzaid/ai-job-search --clone
cd ai-job-search
```

Or manually: fork on GitHub, then clone your fork.

## 3. Job search CLI tools — no install needed

The active German/European CLIs (`arbeitsagentur-search`, `adzuna-search`, `arbeitnow-search`) are zero-dependency Node scripts — there is nothing to install. Just make sure Node 18+ is available and, for Adzuna, that your API key is exported (see Prerequisites above).

A quick smoke test of the no-auth German tool:

```bash
node .agents/skills/arbeitsagentur-search/cli/src/cli.mjs search -q "Data Scientist" --where Berlin --since 14 --limit 3 --format table
```

## 4. Run the setup interview

Start Claude Code in the repository:

```bash
claude
```

Then run the onboarding:

```
/setup
```

Claude will offer two paths:

- **Path A (recommended):** Share your existing CV (mention the file with `@` or paste the text). Claude extracts your information and asks follow-up questions for anything missing.
- **Path B:** Answer structured interview questions section by section.

Both paths produce the same result: fully populated profile files.

### What gets populated

| File | Content |
|------|---------|
| `CLAUDE.md` | Your full candidate profile |
| `01-candidate-profile.md` | Structured education, experience, skills |
| `02-behavioral-profile.md` | Behavioral assessment |
| `04-job-evaluation.md` | Personalized skill match areas and career goals |
| `05-cv-templates.md` | Profile statement templates for your background |
| `07-interview-prep.md` | STAR examples from your experience |
| `cv/main_example.tex` | Your LaTeX CV with actual details |
| `search-queries.md` | Job search queries for `/scrape` |

### Re-running setup

You can update specific sections later:

```
/setup --section skills
/setup --section experience
/setup --section search
```

The `--section search` option is especially useful as your priorities evolve. It re-runs the search configuration interview and suggests role types you may not have considered based on your full profile.

## 5. Optional: Set up salary benchmarking

If you have salary data (from a union, salary survey, Glassdoor, or personal research):

1. **Option A:** Create `salary_data.json` manually in the repo root (see `tools/README_SALARY_TOOL.md` for the format)
2. **Option B:** Convert from Excel:
   ```bash
   pip install openpyxl
   python tools/convert_salary_excel.py path/to/salary-data.xlsx --source "My Salary Data 2025"
   ```

This creates `salary_data.json` which the `/apply` workflow uses for salary benchmarking. If you skip this step, salary lookup is simply omitted.

## 6. Test the workflow

Find a job posting you're interested in, then:

```
/apply https://www.arbeitsagentur.de/jobsuche/jobdetail/10000-1206543821-S
```

Or paste the job description directly:

```
/apply [paste job posting text here]
```

Claude will:
1. Evaluate the fit against your profile
2. Ask if you want to proceed
3. Draft a tailored CV and cover letter
4. Have a reviewer agent critique the drafts
5. Revise and present the final output

## 7. Compile your documents

After `/apply` creates the LaTeX files:

```bash
# Compile CV
cd cv && lualatex main_<company>.tex && cd ..

# Compile cover letter
cd cover_letters && xelatex cover_<company>_<role>.tex && cd ..
```

## Troubleshooting

### "salary_data.json not found"
This is expected if you haven't set up salary benchmarking. The `/apply` workflow skips this step automatically.

### Job search CLI tools not working
The active German/European CLIs need Node 18+ (run `node --version`) and network access — there are no dependencies to install. If `adzuna-search` returns a `MISSING_CREDENTIALS` error, export your `ADZUNA_APP_ID` / `ADZUNA_APP_KEY` (see Prerequisites). The dormant Danish CLIs require Bun and are not wired into `/scrape`.

### LaTeX compilation errors
- CV: uses `lualatex` (pdflatex often fails on modern MiKTeX with `fontawesome5` font-expansion errors; lualatex handles the same sources cleanly)
- Cover letter: uses `xelatex` (for custom fonts in `OpenFonts/fonts/`)
- Make sure your LaTeX distribution includes the `moderncv` package

### Fonts not found in cover letter
The cover letter template expects fonts in `cover_letters/OpenFonts/fonts/`. Make sure this directory exists and contains the Lato and Raleway font files.
