# Week 8: The Capstone Release

**Due at the end of Week 8:**

1. [Project 4 (Weeks 6-8), Week 8 component: The Capstone Release](#project-4-weeks-6-8-week-8-component-the-capstone-release)

There is **no assignment** this week. All of your time goes to the project.

> **Project numbering:** **Weeks 6, 7, and 8 together form Project 4.** This page completes
> the project begun in the
> [Week 6 component](Week6.md#project-4-weeks-6-8-week-6-component-put-an-agent-to-work-on-your-method)
> and continued in the
> [Week 7 component](Week7.md#project-4-weeks-6-8-week-7-component-generate-publish-and-reuse-your-methodology).
> Submit one cumulative **Project 4** covering all three weeks.

---

## Setup

### Step 1: Update OML Code and the OML CLI

In VS Code, open **Extensions** (`Cmd/Ctrl+Shift+X`), find **OML Code**, and click **Update**
if offered. Then **reload the window** when prompted.

```bash
npm install -g @oml/cli@latest
oml -v
```

### Step 2: Sync Sierra and open your project repo

1. On **your `sierra-method` fork's** GitHub page, click **Sync fork** -> **Update branch**,
   then pull it locally. You will copy two files from it.
2. **File -> Open Folder...** on your **project repo** (the methodology repo from Week 7).
   Work in it for the rest of the week.

Run `oml` commands from VS Code's **integrated terminal** (**Terminal -> New Terminal**). The
OML Code extension already runs the OML server for your repo, so you do not need `oml start`
or `oml stop` locally.

### Step 3: Know the two workflows

Sierra ships both workflows you need. You will copy them into your repo:

| File | Runs on | What it does |
| --- | --- | --- |
| `.github/workflows/ci.yml` | Every pull request into `main` (and every push to it) | Runs three **gates**: lint, validate, reason |
| `.github/workflows/cd.yml` | Every pushed tag like `v1.0.0` | Renders your Markdown to a website and deploys it to GitHub Pages |

Each gate catches a different kind of problem:

| Gate | Command | Fails when |
| --- | --- | --- |
| Lint | `oml lint` | An OML file has an error (for example an unresolved reference) |
| Validate | `oml validate --only` | A SHACL shape in a Markdown table editor is violated (for example a required field is empty) |
| Reason | `oml reason --only` | The model is logically inconsistent (for example an instance typed by two disjoint concepts) |

`--only` skips the lint that `validate` and `reason` would otherwise repeat first.

### Step 4: Git from VS Code

Every git step this week can be done from VS Code's UI. Each step gives the clicks, with the
terminal commands in a collapsed **Terminal** block if you prefer them.

| To do this | In VS Code |
| --- | --- |
| See your changes | **Source Control** view: the branch icon in the Activity Bar, or `Ctrl+Shift+G` |
| Stage and commit | Click **+** next to a file (or the **Changes** header), type a message, click **Commit** |
| Push or pull | **Sync Changes** (or **Publish Branch** for a new branch), or **...** -> **Push** / **Pull** |
| Switch or create a branch | Click the branch name in the **status bar** (bottom left) |
| Find a commit hash | The **Graph** section of Source Control: hover over a commit |
| Read a git error | Click **Show Command Output** when VS Code reports one |

---

## Project 4 (Weeks 6-8), Week 8 Component: The Capstone Release

Publish and defend a reproducible, verified, and approved version **1.0.0** of your
engineering model.

| # | Deliverable | Minimum evidence |
| --- | --- | --- |
| 1 | Repository secrets setup | Add `OML_PLATFORM_API_KEY` as an **Actions secret** in your repository. Its value will be provided to you separately. |
| 2 | Continuous integration (CI) | Add a `ci.yml` workflow that **lints, validates, and reasons** on each pull request as gates. |
| 3 | Continuous delivery (CD) | Add a `cd.yml` workflow that **renders the workspace** and publishes it to the repo's GitHub Pages. |
| 4 | Protected baseline | Protect the `main` branch so commits are accepted **only through a reviewed pull request**. |
| 5 | Feature branch | Create a new feature branch in your repository to receive your commits. |
| 6 | Commit pushing | Push **3 commits that each trip one of the 3 gates**, then a **4th commit that passes**. Record all four commit hashes. |
| 7 | v1.0.0 release | Push a `v1.0.0` tag on your good commit and create a GitHub release with it. Watch it deploy to GitHub Pages. |
| 8 | GitHub Pages | Update your repo's `README.md` to link to your GitHub Pages site and show a release badge. |
| 9 | Final report | Each Module 1 question answered with evidence tied to release `1.0.0` and its commit, or an explicitly documented gap. Plus your reflection. |

The work falls into four parts. Parts A-C are mostly clicking through GitHub and take one
sitting; most of your thinking goes into Part D.

| Part | Deliverables | What you end up with |
| --- | --- | --- |
| [A. Automate](#part-a-automate-deliverables-1-3) | 1-3 | GitHub checks every change and can publish your site |
| [B. Protect](#part-b-protect-main-deliverable-4) | 4 | `main` accepts changes only through a pull request that passes the gates |
| [C. Release](#part-c-release-100-deliverables-5-7) | 5-7 | A reviewed, gated v1.0.0 release with a live site |
| [D. Show and defend](#part-d-show-and-defend-deliverables-8-9) | 8-9 | README badges, and a final report traced to v1.0.0 |

> **Worked example:** Sierra has done every step. See its
> [pull request #3](https://github.com/modelware/sierra-method/pull/3) (one commit per gate),
> its [v1.0.0 release](https://github.com/modelware/sierra-method/releases/tag/v1.0.0), its
> [live site](https://www.modelware.io/sierra-method/), and the badges at the top of its
> [README](https://github.com/modelware/sierra-method#readme).

**Take screenshots as you go.** Steps that need one are marked **Screenshot**. The full list
is in [Submitting Project 4](#submitting-project-4).

---

### Part A: Automate (Deliverables 1-3)

#### Deliverable 1: Add the secret

1. On your repo's GitHub page, click **Settings** -> **Secrets and variables** -> **Actions**
   (left sidebar, under **Security**).
2. Click **New repository secret**.
3. **Name**: `OML_PLATFORM_API_KEY`. **Secret**: paste the value you were given. Click
   **Add secret**.

**Screenshot** the secret in the list (its value is hidden; never show the value itself).

#### Deliverable 2: Add CI

1. Copy the workflow from your Sierra clone (adjust the path if Sierra is not next to your
   repo):

   ```bash
   mkdir -p .github/workflows
   cp ../sierra-method/.github/workflows/ci.yml .github/workflows/
   ```

2. Run the three gates locally. All three should pass before you continue:

   ```bash
   oml lint
   oml validate --only
   oml reason --only
   ```

3. In **Source Control**, stage `ci.yml`, commit it as *Add CI workflow*, and click **Sync
   Changes**. (`main` is not protected yet, so this push goes straight in.)

   <details><summary>Terminal</summary>

   ```bash
   git add .github/workflows/ci.yml
   git commit -m "Add CI workflow"
   git push
   ```

   </details>

4. On GitHub, open the **Actions** tab and click the **CI** run. Wait for the green check.

#### Deliverable 3: Add CD

1. Copy the second workflow:

   ```bash
   cp ../sierra-method/.github/workflows/cd.yml .github/workflows/
   ```

2. In `.oml/settings.yml`, point the `render:` line under `defaults` at your own model.
   Sierra's line is:

   ```yaml
   render: -m src/model/md -b build/web -c src/model/oml/fireforce6.github.io/mission-control/bundle.oml
   ```

   Change `-m` to your Markdown folder and `-c` to your bundle. Keep `-b build/web`.
3. Check the site locally: run `oml render`, then open `build/web/index.html` in a browser.
4. Stage `cd.yml` and `.oml/settings.yml`, commit as *Add CD workflow*, and **Sync Changes**.

   <details><summary>Terminal</summary>

   ```bash
   git add .github/workflows/cd.yml .oml/settings.yml
   git commit -m "Add CD workflow"
   git push
   ```

   </details>

5. Turn on Pages: **Settings** -> **Pages** -> **Source**: **GitHub Actions**.
6. Let tags deploy: **Settings** -> **Environments** -> **github-pages** -> **Add deployment
   branch or tag rule** -> **Ref type**: **Tag**, pattern `v*.*.*` -> **Add rule**.

CD does not run yet. It runs when you push the `v1.0.0` tag in Part C.

> **Why step 6?** Pages only accepts deployments from `main` by default, but CD runs from a
> tag. Without the rule, the deploy fails with *"Tag v1.0.0 is not allowed to deploy to
> github-pages due to environment protection rules."* Your repo must also be **public**.

---

### Part B: Protect `main` (Deliverable 4)

#### Deliverable 4: Add a branch rule

1. On GitHub, click **Settings** -> **Rules** -> **Rulesets** -> **New ruleset** -> **New
   branch ruleset**.
2. **Ruleset Name**: `protect-main`. **Enforcement status**: **Active**. Leave the **Bypass
   list** empty, so the rule applies to you too.
3. **Target branches**: **Add target** -> **Include default branch**.
4. Under **Rules**, check:
   - **Restrict deletions** and **Block force pushes**
   - **Require a pull request before merging**, with **Required approvals** set to **0**
   - **Require status checks to pass**: click **Add checks**, type `build`, and select it
5. Click **Create**. **Screenshot** the rule.
6. Test it: make a throwaway edit (for example a blank line in `README.md`), commit it, and
   click **Sync Changes**. The push is rejected. Click **Show Command Output** and
   **screenshot** the rejection. Then undo it: **...** -> **Commit** -> **Undo Last Commit**,
   and right-click the file -> **Discard Changes**.

   <details><summary>Terminal</summary>

   ```bash
   git commit --allow-empty -m "Test branch protection"
   git push                     # rejected; screenshot the message
   git reset --hard HEAD~1      # drop the test commit
   ```

   </details>

> **Why 0 approvals?** You review your own pull requests. GitHub does not let authors
> approve their own, so you leave a review **comment** instead, and the `build` check still
> blocks any merge that fails a gate. This self-review is a workaround for a GitHub
> limitation, used in these instructions only. On a real team, a peer reviews the pull
> request, and required approvals is at least 1.

If `build` does not appear under **Add checks**, CI has not run yet: finish Deliverable 2
first.

---

### Part C: Release 1.0.0 (Deliverables 5-7)

#### Deliverable 5: Create a feature branch

1. Click the branch name in the status bar, pick `main`, then **...** -> **Pull**.
2. Click the branch name again -> **+ Create new branch...** -> `release-1.0.0`.
3. Click **Publish Branch** in Source Control.

   <details><summary>Terminal</summary>

   ```bash
   git switch main
   git pull
   git switch -c release-1.0.0
   git push -u origin release-1.0.0
   ```

   </details>

#### Deliverable 6: Push four commits through the gates

You will push four commits, one at a time. The first three each break exactly one gate; the
fourth fixes everything:

| Commit | Change | CI result |
| --- | --- | --- |
| 1 | Add an OML error (for example a reference to something that does not exist) | Fails at **Linting** |
| 2 | Undo commit 1's change, and violate a shape (for example leave a required field empty) | Fails at **Validating** |
| 3 | Undo commit 2's change, and make the model inconsistent (for example type an instance by two disjoint concepts) | Fails at **Reasoning** |
| 4 | Undo commit 3's change, and set `project.version` to `1.0.0` in `.oml/settings.yml` | **Passes** all gates |

1. **Commit 1.** Make the change, and run the gates locally to check it fails where you
   expect. Commit it (*Break lint*) and **Sync Changes**.

   <details><summary>Terminal</summary>

   ```bash
   git commit -am "Break lint"
   git push
   ```

   </details>

2. **Open the pull request.** On GitHub, click **Compare & pull request** in the yellow
   banner. Check that **base** is `main` and **compare** is `release-1.0.0`, title it
   *Release 1.0.0*, and click **Create pull request**.
3. **Watch CI.** At the bottom of the pull request, click **Details** next to the **build**
   check and confirm which step failed. **Screenshot** it.
4. **Commits 2, 3, and 4.** For each one: make the change, commit, **Sync Changes**, wait for
   CI, and **screenshot** the result. They all land on the same pull request.
5. **Review it yourself.** Open the **Files changed** tab and read the diff. Click **Review
   changes**, write what you checked, select **Comment**, and click **Submit review**.
6. **Merge.** On the **Conversation** tab, click **Merge pull request** -> **Confirm merge**
   (use **Create a merge commit** if asked). **Screenshot** the merged pull request.

Record the four commit hashes: the pull request's **Commits** tab lists them, each with a
red or green CI mark.

#### Deliverable 7: Tag and release v1.0.0

1. **Tag the merge commit.** Switch to `main` and **Pull**. Check that the top commit in
   **Graph** is *Merge pull request #...*, and record its hash. Then open the Command Palette
   (`Cmd/Ctrl+Shift+P`) -> **Git: Create Tag** -> `v1.0.0` -> message *Release 1.0.0*.
   Then Command Palette -> **Git: Push Tags**.

   <details><summary>Terminal</summary>

   ```bash
   git switch main
   git pull
   git log --oneline -1          # the merge commit; record its hash
   git tag -a v1.0.0 -m "Release 1.0.0"
   git push origin v1.0.0
   ```

   </details>

2. **Create the release.** On GitHub, click **Releases** (right sidebar) -> **Draft a new
   release** -> **Choose a tag**: `v1.0.0`. Title it `v1.0.0`, click **Generate release
   notes**, and click **Publish release**.
3. **Watch it deploy.** Open the **Actions** tab and the **CD** run the tag started. When
   both jobs are green, open the site URL it shows (`https://<owner>.github.io/<repo>/`).
   **Screenshot** the run and the site.

> **If CD fails,** fix it through another pull request, then move the tag to the new merge
> commit (`git tag -d v1.0.0`, `git push origin :refs/tags/v1.0.0`, then tag again). Mention
> it in your report.

---

### Part D: Show and defend (Deliverables 8-9)

#### Deliverable 8: Add README badges

`main` is protected, so this small change also goes through a pull request.

1. From an up-to-date `main`, create a branch named `readme-pages`.
2. In `README.md`, directly under the title, add these two lines. Replace `<owner>` and
   `<repo>` with the parts of your repo's URL, `https://github.com/<owner>/<repo>`:

   ```markdown
   [![Release](https://img.shields.io/github/v/release/<owner>/<repo>)](https://github.com/<owner>/<repo>/releases/latest)
   [![Live Site](https://img.shields.io/badge/Live-Site-brightgreen)](https://<owner>.github.io/<repo>/)
   ```

   If your README already has a fixed badge (like Sierra's old
   `img.shields.io/badge/Release-v0.1.0-blue`), replace it with the release badge above.
3. Preview it (`Cmd/Ctrl+Shift+V`): you should see **release v1.0.0** and a green
   **Live Site** badge.
4. Commit, **Publish Branch**, then open a pull request, review it, and merge it, as in
   Deliverable 6.
5. On your repo's GitHub page, confirm both badges show and **Live Site** opens your site.

This change comes after `v1.0.0`, so it is not part of the release. That is expected: do not
move the tag for it.

#### Deliverable 9: Write the final report

The final report closes the course. Revisit the engineering questions from
[Project 1](Week1.md#project-1-week-1-project-scope-problem-statement) (Section 3), answer
them using the analyses developed in the method, and **trace every conclusion to the
approved 1.0.0 release**. Then reflect on the whole approach.

For each Module 1 question, give:

| Part | What to include |
| --- | --- |
| Question | The question from Project 1 and its stakeholder. If you revised it, give both versions and why. |
| Evidence | The query, view, analysis, or generated product that answers it, linked **at tag `v1.0.0`** |
| Answer | The result, computed from the release, and what it tells the stakeholder |
| *or* Gap | If it cannot be answered, the diagnosed gap and its source (vocabulary, pattern, process, or outside the model), as in Week 5 |

Link evidence as `https://github.com/<owner>/<repo>/blob/v1.0.0/<path>`, so it points at the
released file rather than whatever `main` holds later. Your Week 5 `ANALYSIS.md` is a good
starting point. A diagnosed gap is a finding, not a failure.

Then write a **reflection** (1-2 pages):

- What did the approach make easier or harder than how you work today?
- Which capability mattered most: vocabularies, reasoning, methodology, analysis, agents,
  generation, or CI/CD?
- What would you change in your method?
- What would you need to use it on a real program?

---

## Submitting Project 4

Push everything, then submit **Project 4** on **your school's course platform** as one
document covering Weeks 6, 7, and 8.

**Week 6:** the project repo URL, the commit hash of the adjusted skill, the chat logs and
git-change screenshots from rounds 1-3, and the round 4 report and chat log.

**Week 7:** the URLs and commit hashes of the methodology repo and the example repo, the
description of the generator, package, and consumer, and the evidence for each step.

**Week 8:**

- [ ] Screenshots: the secret (name only), the branch rule, and the rejected direct push
- [ ] The pull request URL, the four commit hashes with the CI result of each, and the CI
      screenshots
- [ ] Screenshots of your review comment and the merged pull request
- [ ] The release URL, the commit hash tagged `v1.0.0`, and screenshots of the CD run and
      the live site
- [ ] Your GitHub Pages URL
- [ ] The final report: an answer or a diagnosed gap for each Module 1 question, traced to
      `v1.0.0`, and your reflection
