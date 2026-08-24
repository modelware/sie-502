# Week 1 — Ontological Modeling for Systems Engineering

Welcome to SIE-502. This page walks you through setting up the toolchain and tells you
exactly what to submit before Module 2.

---

## Before Module 2 — Do This Now

Two things are due before the next class:

1. **[Assignment 1 — Tool Setup + Running Example](#4-assignment-1--tool-setup--running-example).**
   Install the tooling, clone the running example, run the CLI, and capture screenshots.
2. **[Project Deliverable 1 — Project Scope & Problem Statement](#5-project-deliverable-1--project-scope--problem-statement).**
   One to two pages describing the system you propose to model and the questions your
   model should answer.

> **Start the setup early.** Two steps have waiting periods outside your control: GitHub
> Copilot student verification can take a few days, and OML Code login only works after
> your GitHub email has been added to the course authorization list. Begin with
> [Step 0 — Send me your GitHub email](#step-0--send-me-your-github-email) and
> [Step 6 — Sign up for GitHub Copilot](#step-6--sign-up-for-github-copilot-student-plan)
> today.

---

## 1. The Toolchain

We use two pieces of tooling plus an AI assistant:

- **OML Code** — a VS Code extension; the IDE for working with the OML language.
- **OML CLI** — the command-line counterpart, used for lint / validate / reason / render.
- **GitHub Copilot** — the generative-AI side of the neurosymbolic workflow, used from
  inside VS Code.

And one repository:

- **`sierra-method`** — the running example we will grow all semester. It is the Fireforce
  project: a method, a vocabulary, and a system description modeled in OML.

---

## 2. Setup Instructions

Work through these in order. Steps 0 and 6 have waiting periods, so start with those today.

### Step 0 — Send me your GitHub email

**Do this first — everything else waits on it.**

OML Code requires authentication, and login only works once your identity has been added
to the course authorization list.

1. If you don't have a GitHub account, sign up: https://github.com/join
2. Go to **Settings → Emails** (https://github.com/settings/emails).
3. Find the address marked **primary** and copy it.
4. **Email me your name and that primary GitHub email** at
   **melaasar@modelware.io**. I will add you to the OML Code authorization and confirm
   back to you.

Until you get that confirmation, the login steps (5 and 7 below) will not succeed.

### Step 1 — Install Visual Studio Code

Download for your operating system: https://code.visualstudio.com/download

### Step 2 — Install the OML Code extension

1. Open VS Code.
2. Open the **Extensions** tab (the square icon in the left sidebar, or `Cmd/Ctrl+Shift+X`).
3. Search for **`OML Code`**. It should be the first result.
4. Click **Install**.
5. Leave **auto-update enabled** so you pick up new versions during the course.

The current version is `0.25.1` — just install the latest.

More about the tool and short feature demos: https://www.modelware.io/oml-code

### Step 3 — Install Node.js

The OML CLI needs Node.js **v20 or later**.

1. Open a terminal. In VS Code: **View → Terminal** (or ``Ctrl+` ``).
2. Check what you have:
   ```bash
   node --version
   ```
3. If Node is missing or older than v20, install it from https://nodejs.org/ (the site
   walks you through your operating system), then re-run the check.

A wrong Node version is the single most common cause of a failed CLI install, so confirm
this before moving on.

### Step 4 — Install the OML CLI

In the terminal:

```bash
npm install -g @modelware/oml-cli
```

This takes a few seconds. A couple of warnings are normal and can be ignored — you are
looking for a **success** message at the end. Verify:

```bash
oml --version
```

If the install fails, re-check your Node version (Step 3) first.

### Step 5 — Clone the running example

Clone the `sierra-method` repository: https://github.com/modelware/sierra-method

```bash
git clone https://github.com/modelware/sierra-method.git
```

You need `git` installed for this. If you don't have it, install it from
https://git-scm.com/downloads. If you have never cloned a repository before, GitHub's
guide covers every method (HTTPS, SSH, GitHub CLI, Desktop):
https://docs.github.com/en/repositories/creating-and-managing-repositories/cloning-a-repository

Then open it in VS Code: **File → Open Folder…** and select the folder you just cloned.
You should see the `sierra-method` project tree in the sidebar.

### Step 6 — Sign up for GitHub Copilot (student plan)

Apply for the **GitHub Student Developer Pack**, which includes Copilot:
https://github.com/education/students

**Do this early** — student verification can take a few days. The plan gives you credits
we will use for the AI component of the course.

### Step 7 — Log in to OML Code (extension and CLI)

**Extension.** The first time you open the `sierra-method` folder, OML Code detects that you
are not signed in and prompts you:

1. Click **Log in**, then **Allow**.
2. Your browser opens. Choose **Continue as `<your username>`**.
3. It asks for a code. Switch back to VS Code, **copy the code shown there**, paste it into
   the browser, and continue.
4. Click **Authorize**.

**CLI.** In the terminal, from inside the cloned repo:

```bash
oml login
```

This prints a URL and a code. Open the URL, authenticate, paste the code, and authorize.
Give it a couple of seconds and it will report that you are signed in.

> Both logins only work **after** I have confirmed your email is authorized (Step 0).

---

## 3. Smoke Tests — Verify Your Setup

Run all of these. They are also the evidence you will screenshot for Assignment 1.

### 3.1 Browse the model in OML Code

1. Open the `README` at the root of the repo. If OML Code is working, it renders as a
   rich view rather than plain text.
2. Click **Start**. **Fireforce** is the project we will model.
3. Click **Browse** → **sierra-method** → open the **steps of the method**.
4. Click into the **first step**. **If you see a rendered table there, your extension is
   working.** Browse around — this is the material we will walk through in the course.

### 3.2 Open a vocabulary diagram

1. Go to `src/method.oml` (the method OML sources) → **sierra-method**.
2. Pick an ontology — for example the **mission vocabulary**.
3. **Right-click → Open Diagram.**
4. You get a visualization of the vocabulary. Double-click elements to jump to the
   corresponding model elements.

### 3.3 Open a description diagram

1. Go to the model OML sources → **context analysis** → pick **objectives**.
2. **Right-click → Show Diagram.**
3. You should see your system description: the objectives of the mission and how they
   relate to the concerns they address or pursue.

### 3.4 Run the CLI

From the root of the cloned repo, each of these should succeed:

```bash
oml lint
oml validate
oml reason
oml render
```

- `oml lint` — checks style/well-formedness.
- `oml validate` — checks the model against the language rules.
- `oml reason` — runs the description logic reasoner. **Confirm it reports no
  inconsistencies.**
- `oml render` — generates the web views into a `build/web` folder.

Then open the rendered output: find `build/web` in Finder/Explorer and double-click the
entry point to open it in your browser. You should see the same views you were browsing in
VS Code, now as a static site.

### 3.5 Ask the AI a question

1. Open the AI panel in OML Code and go to the **Chat** tab. (You may not see every tab
   shown in the lecture — don't worry about the others; Chat is the one you need.)
2. Click the **`+`** to start a conversation.
3. Ask something answerable from the model, for example:
   > What are the stakeholders in the model?
4. Wait for the answer, then **check it against the model.** The README lists the
   stakeholders directly from the model — compare the two. In the demo, the AI reported
   five stakeholders and the model had five, so it told the truth *that time*.

That verification habit is the point: the LLM drafts, you check it against the model. Later
in the course we will use the AI not only to ask questions but to help modify the model.

---

## 4. Assignment 1 — Tool Setup + Running Example

**Due before Module 2. Graded on completion.**

### Checklist

- [ ] Sign up for the GitHub Copilot student plan
- [ ] Email your name and primary GitHub email to melaasar@modelware.io, and get confirmation
- [ ] Install VS Code
- [ ] Install the OML Code extension
- [ ] Install Node.js (v20+) and the OML CLI
- [ ] Clone the running example repository and open it in VS Code
- [ ] Log in to OML Code — both the extension and the CLI
- [ ] Run the OML CLI tools and confirm the project builds with no errors
- [ ] Confirm the reasoner reports no inconsistencies
- [ ] Open the markdown/web views and inspect that they render fine
- [ ] Open the diagrams (a vocabulary and a description)
- [ ] Open GitHub Copilot / the AI chat and ask a question about the model
- [ ] **Take screenshots as evidence of every step**

### What to submit

A single **ZIP file** containing your screenshots — one for each step above — showing that
you installed the tools successfully on your machine and that the smoke tests pass.

### Why this matters

Module 2 assumes a working environment: **you write OML in the first ten minutes.** Setup
issues are environment-specific — OS, Node version, permissions — and they take a day, not
an hour. Post on Piazza as soon as you get stuck; somebody has hit your exact error. Help
is available now, not the night before.

---

## 5. Project Deliverable 1 — Project Scope & Problem Statement

**One to two pages. Due before Module 2.**

Write a short report proposing your project: the domain you want to work in and the system
you want to model in that domain. Structure it in five sections.

| # | Section | What I am looking for |
| --- | --- | --- |
| 1 | **The system** | What it is, its boundary, and explicitly what is *outside* it |
| 2 | **Stakeholders** | Who cares about this system, and what each needs to know |
| 3 | **The questions** | 3 to 5 specific questions your model should answer |
| 4 | **Why it is hard** | Where knowledge is fragmented, ambiguous, or tacit today |
| 5 | **Available data** | Existing documents, spreadsheets, or models to draw on |

### Section 3 is the one I will push back on

Write the questions in plain English, framed as *"I am modeling this system so that I can
analyze X / derive insight Y."* Then say **who each question is for** — the stakeholder who
benefits from the answer.

| Weak question | Strong question |
| --- | --- |
| "What are the components?" | "Which components are affected if the payload interface changes?" |
| "How does it work?" | "Is every safety requirement traced to a verification activity?" |
| "What is the architecture?" | "Which subsystems exceed their mass budget allocation?" |

Strong questions are **specific, cross-cutting, and currently hard to answer**. They become
your model's acceptance criteria.

### Two more things

- **Section 4 — why is this hard without modeling?** What stops you from solving this
  another way today?
- **Section 5 — what material will you actually work with?** Documents, spreadsheets,
  examples you have seen elsewhere. Give me some background on what you are drawing from.

**Choose a domain you actually know.** You will build on this proposal all semester: each
week we take an aspect of developing a methodology with OML Code, and you do something
similar for your own project. Weekly assignments stay focused on the case study so the
concepts land first; the project is where you apply them. I will give you feedback on this
proposal before you build on it.

---

## 6. Further Reading

None of this is required before the next class — these are the references you will use
going forward.

| Ref | Source | Why |
| --- | --- | --- |
| REF01 | [OML Language Specification](https://opencaesar.io/oml/) | Skim now; you will live in it from Module 2. It is the manual for OML syntax and semantics — a reference, not a read-through |
| REF02 | [OML Tutorials](https://opencaesar.io/oml-tutorials/) | **Do tutorials 1 and 2** — enough to get a good flavor of OML. See the note below |
| REF03 | [OML overview paper (ISWC 2026)](https://www.modelware.io/assets/papers/iswc-2026-oml-semantic-web-mbse.pdf) | Rationale, overview, and design decisions behind OML. Published this year at a semantic web conference |
| REF04 | [OML Code](https://www.modelware.io/oml-code) | Home of the tool we use in class — feature pages and short demos |
| REF05 | [openCAESAR](https://opencaesar.io/) | The open source counterpart: papers, blogs, and recorded talks from conferences and workshops. Worth browsing to see the industry community behind this work |

> **Note on the tutorials (REF02):** they are written against the openCAESAR open source
> tooling, not OML Code. Install that tooling and follow along if you want — the concepts
> transfer, and nearly everything there can also be done in OML Code. We will use **OML
> Code** for the rest of the course.

---

## Next Module

**Module 2 — Modeling Languages: OML in Depth.** We stop talking about ontologies and start
writing one.
