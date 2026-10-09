# Week 5: Methodology II

**Due before Module 6:**

1. [Assignment 5: Build a Small Analysis Layer Over Fire Force](#assignment-5-build-a-small-analysis-layer-over-fire-force)
2. [Project 3 (Weeks 4-5), Week 5 component: Add an Analysis Layer to Your Methodology](#project-3-weeks-4-5-week-5-component-add-an-analysis-layer-to-your-methodology)

> **Project numbering:** This is the final submission for **Project 3**, which combines the
> methodology work from **Week 4** with the analysis layer from **Week 5**. Submit the latest
> cumulative Week 5 commit containing both components; a Week 4-only commit is incomplete.

---

## Setup

### Step 1: Update OML Code and the OML CLI to the latest version

In VS Code, open **Extensions** (`Cmd/Ctrl+Shift+X`), find **OML Code**, and click **Update**
if offered. Then **reload the window** when prompted.

```bash
npm install -g @oml/cli@latest
oml -v
```

### Step 2: Sync your `sierra-method` fork

On **your fork's** GitHub page: click **Sync fork** -> **Update branch**. Then pull it
locally:

```bash
git pull
git status
```

Then **File -> Open Folder...** on the repo and run every command from VS Code's **integrated
terminal**.

### Step 3: Study the existing dashboards

Sierra already contains an analysis layer. Read these before you write your own:

| Page | Shows |
| --- | --- |
| `src/method/md/www.modelware.io/sierra/context-analysis/dashboard.md` | A `graph` fed by `CONSTRUCT`, and a `matrix` where `COALESCE(?n, 0)` produces explicit zeros |
| `src/method/md/www.modelware.io/sierra/operational-analysis/dashboard.md` | A `diagram` and two `chart` views |
| `src/method/md/www.modelware.io/sierra/system-analysis/dashboard.md` | A `tree` view (mass rollup) |
| `src/model/md/Fire Force VI/Mission Control/Operational Analysis/Dashboard2.md` | Scripted `python` and `r` blocks: query, compute, render |
| `src/model/md/Fire Force VI/Mission Control/Context Analysis/Dashboard.md` | A page that invokes a dashboard `compose` template defined in the method |

---

## Assignment 5: Build a Small Analysis Layer Over Fire Force

Study with running example: Sierra Method + Fire Force system description.

### Queries: write five

| Query | What it does |
| --- | --- |
| Conformance | Test whether the model follows a method rule |
| Near miss | Find elements close to satisfying a condition |
| Orphan | Find missing relationships using `FILTER NOT EXISTS` or `!BOUND` |
| Coverage | Build a matrix where explicit `0`s reveal gaps |
| View graph | Use `CONSTRUCT` to produce a graph shaped for analysis |

### Views and analysis: build from your query results

1. Render query results using **at least three different view types**
2. Take one query result into a **scripted block**: query > compute > render
3. Package one analysis or view as a reusable **`compose` template**
4. Turn one query into a **dashboard section**: state the engineering question, show the
   query result as evidence, and explain what the result means.
   **Question -> Evidence -> Interpretation**

### Two things to demonstrate

- **Absence must be meaningful.** Your orphan and coverage queries should reveal missing
  relationships or explicit zeros. If the model has no gaps, show the clean result and
  explain what gap the query would detect.
- **Reuse must be real.** Define the `compose` template once and invoke it from a second
  page. The point is to demonstrate reuse, not simply place the analysis in a template.

### Submitting

Push your work to your `sierra-method` fork, then submit a document on the **UofA learning
platform** with your **fork URL**, the **commit hash**, the **path to your analysis
notebook**, and a **short reflection**.

---

## Project 3 (Weeks 4-5), Week 5 Component: Add an Analysis Layer to Your Methodology

Every methodology question should end in either an **answer** or a **diagnosed gap**.

| Deliverable | Minimum evidence |
| --- | --- |
| Methodology questions | One query or one diagnosed gap for each Module 1 question |
| Gap detection | One orphan / missing-relationship query for each relevant pattern |
| Dashboard | Narrative + three view types |
| Computed analysis | One analysis that requires script-based computation |
| One view template | One `compose`, `navigation`, or `call` template |
| ANALYSIS.md | Question -> evidence -> finding |

### If a question cannot be answered

| Source of the gap | What to do |
| --- | --- |
| Vocabulary | Extend the methodology vocabulary (Module 2) |
| Pattern | Revise the modeling pattern (Module 4) |
| Process | Address it in team practice |
| Outside the model | State the limitation; do not force it into the model |

**A diagnosed gap is a finding.** For example: *"We cannot report verification coverage
because the methodology does not require a Requirement to reference its Verification
Activity."*

### Submitting

Push the cumulative Week 4-5 work, then submit **Project 3** on the **UofA learning platform**
with your **project repo URL** and the **latest commit hash that contains both the Week 4
methodology and Week 5 analysis work**.
