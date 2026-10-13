# Week 8: The Capstone Release

**Due Oct 18:**

1. [Project 4 (Weeks 6-8), Week 8 component: The Capstone Release](#project-4-weeks-6-8-week-8-component-the-capstone-release)

There is **no assignment** this week. All of your time goes to the project.

> **Project numbering:** **Weeks 6, 7, and 8 together form Project 4.** This page describes the
> Week 8 component, which completes the project begun in the
> [Week 6 component](Week6.md#project-4-weeks-6-8-week-6-component-put-an-agent-to-work-on-your-method)
> and continued in the
> [Week 7 component](Week7.md#project-4-weeks-6-8-week-7-component-generate-publish-and-reuse-your-methodology).
> Submit one cumulative **Project 4** covering all three weeks.

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

Then **File -> Open Folder...** on your **project repo** and run every command from VS Code's
**integrated terminal**.

### Step 3: Know the two workflows Sierra ships

Sierra already has both workflows. Study them before copying them into your project repo:

| File | Runs on | What it does |
| --- | --- | --- |
| `.github/workflows/ci.yml` | Every push to `main` and every pull request into `main` | Signs in, starts the server, then runs three gates: `oml lint`, `oml validate`, `oml reason` |
| `.github/workflows/cd.yml` | Every pushed tag matching `v*.*.*` | Signs in, starts the server, runs `oml render`, and deploys `build/web` to GitHub Pages |

Both read the `OML_PLATFORM_API_KEY` secret to sign in to OML (`oml whoami` checks it).

The three CI gates catch different problems:

| Gate | Command | Fails when |
| --- | --- | --- |
| Lint | `oml lint` | An OML file has a syntax error or an OML validation error (for example an unresolved reference) |
| Validate | `oml validate` | A table-editor SHACL block in a Markdown file is violated (for example a required field is missing) |
| Reason | `oml reason` | The model is logically inconsistent (for example an instance typed by two disjoint concepts) |

---

## Project 4 (Weeks 6-8), Week 8 Component: The Capstone Release

Publish and defend a reproducible, verified, and approved version **1.0.0** of your
engineering model. Work in your **project repo** (the methodology repo from Week 7).

> **Worked example:** Sierra went through every step below. See its protected `main`, the
> pull request [#3](https://github.com/modelware/sierra-method/pull/3) with one commit per
> gate, the [v1.0.0 release](https://github.com/modelware/sierra-method/releases/tag/v1.0.0),
> its [live site](https://www.modelware.io/sierra-method/), and the two badges at the top
> of its [README](https://github.com/modelware/sierra-method#readme).

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

### Deliverable 1: Repository secrets setup

1. Open your project repo on GitHub and click the **Settings** tab (top right of the repo
   menu; you need to be the repo owner to see it).
2. In the left sidebar, under **Security**, expand **Secrets and variables** and click
   **Actions**.
3. On the **Secrets** tab, click **New repository secret**.
4. In **Name**, type exactly `OML_PLATFORM_API_KEY`.
5. In **Secret**, paste the value you were given. Do not add spaces or a trailing newline.
6. Click **Add secret**. It now appears under **Repository secrets** (its value is hidden
   from then on).

#### Tips

- **Never commit the key.** It belongs only in the secret, not in a file, a workflow, or a
  screenshot.
- If `oml whoami` fails in a workflow log, the secret is missing, misnamed, or mistyped.
  Open it again (pencil icon), click **Update secret**, and paste the value again.

### Deliverable 2: Continuous integration (CI)

1. In your project repo, copy the workflow from your `sierra-method` clone (adjust the path
   if Sierra is not next to your repo):

   ```bash
   mkdir -p .github/workflows
   cp ../sierra-method/.github/workflows/ci.yml .github/workflows/
   ```

2. Run all three gates locally, so CI confirms what you already know:

   ```bash
   oml start
   oml lint
   oml validate --only
   oml reason --only
   oml stop
   ```

   `oml validate` and `oml reason` each run lint first by default. `--only` skips that
   repeat, since you just ran `oml lint`.

3. Commit and push to `main` (it is not protected yet):

   ```bash
   git add .github/workflows/ci.yml
   git commit -m "Add CI workflow"
   git push
   ```

4. On GitHub, click the **Actions** tab. Click the **CI** run that just started, then the
   **build** job, and expand each step to watch it. Wait for a green check.

#### Tips

- Keep **lint, validate, and reason as separate steps** so a failure points at one gate.
- Leave the job name `build` unchanged, or note the new name: Deliverable 4 refers to it.
- A red run on `main` at this point means your model already fails a gate. Fix it before
  continuing.

### Deliverable 3: Continuous delivery (CD)

1. Copy the CD workflow the same way:

   ```bash
   cp ../sierra-method/.github/workflows/cd.yml .github/workflows/
   ```

2. Open `.oml/settings.yml` and find the `render:` line under `defaults`. Sierra's is:

   ```yaml
   render: -m src/model/md -b build/web -c src/model/oml/fireforce6.github.io/mission-control/bundle.oml
   ```

   Change `-m` to your Markdown folder and `-c` to your own bundle. Keep `-b build/web`
   (the folder `cd.yml` uploads).
3. Render locally and check the site:

   ```bash
   oml start
   oml render
   oml stop
   ```

   Open `build/web/index.html` in a browser and click through a few pages.
4. Commit and push to `main`:

   ```bash
   git add .github/workflows/cd.yml .oml/settings.yml
   git commit -m "Add CD workflow"
   git push
   ```

5. On GitHub, enable Pages: **Settings** -> **Pages** (left sidebar, under **Code and
   automation**) -> **Build and deployment** -> **Source**: choose **GitHub Actions** from the
   dropdown.
6. Allow tags to deploy. Selecting GitHub Actions creates a `github-pages` environment that,
   by default, accepts deployments only from `main`, but CD runs from a tag. Go to
   **Settings** -> **Environments** -> **github-pages** -> **Deployment branches and tags** ->
   **Add deployment branch or tag rule**, set **Ref type** to **Tag**, enter `v*.*.*` as the
   pattern, and click **Add rule**.

#### Tips

- CD runs only on a pushed `v*.*.*` tag, so it will not run until Deliverable 7.
- If you skip step 6, the CD deploy job fails with *"Tag v1.0.0 is not allowed to deploy to
  github-pages due to environment protection rules."*
- Your repo must be **public** for Pages to work on a free account.

### Deliverable 4: Protected baseline

1. On GitHub, go to **Settings** -> **Rules** -> **Rulesets** (left sidebar, under **Code
   and automation**).
2. Click **New ruleset** -> **New branch ruleset**.
3. Fill in the top of the form:
   - **Ruleset Name**: `protect-main`
   - **Enforcement status**: **Active**
   - **Bypass list**: leave it **empty**, so the rule applies to you too.
4. Under **Target branches**, click **Add target** -> **Include default branch** (this is
   `main`).
5. Under **Rules**, set:

   | Rule | Value |
   | --- | --- |
   | Restrict deletions | Checked |
   | Block force pushes | Checked |
   | Require a pull request before merging | Checked, with **Required approvals** set to **0** (you review your own pull request; see below) |
   | Require status checks to pass | Checked. Click **Add checks**, type `build`, and select the check from GitHub Actions |

6. Click **Create** at the bottom of the page.
7. Confirm the rule works by trying to push directly to `main`:

   ```bash
   git commit --allow-empty -m "Test branch protection"
   git push
   ```

   The push is rejected with a message that the branch is protected. Take a screenshot, then
   drop the test commit:

   ```bash
   git reset --hard HEAD~1
   ```

#### Tips

- **You are the reviewer.** Review your own pull request; no other student is involved.
  GitHub does not let an author **Approve** their own pull request, so set required approvals
  to 0 and record your review as a **Comment** instead (steps in Deliverable 6). The required
  CI check still blocks the merge until every gate passes.

  > **Note:** Self-review is a workaround for a GitHub limitation, used in these instructions
  > only. On a real team, a peer reviews the pull request, and required approvals is set to at
  > least 1 so that no one merges their own unreviewed change.
- The `build` check shows up under **Add checks** only after CI has run at least once, which
  is why Deliverable 2 comes first.
- If you see **Settings -> Branches** with **Add classic branch protection rule** instead,
  that works too: use branch name pattern `main` and the same settings, and check **Do not
  allow bypassing the above settings**.

### Deliverable 5: Feature branch

1. Start from an up-to-date `main` and create the branch:

   ```bash
   git switch main
   git pull
   git switch -c release-1.0.0
   ```

2. Push it to GitHub, so it exists there before your first commit:

   ```bash
   git push -u origin release-1.0.0
   ```

### Deliverable 6: Commit pushing

Push four commits to the feature branch, one at a time, **waiting for CI to finish after
each one**:

| Commit | Change | Expected CI result |
| --- | --- | --- |
| 1 | Introduce an OML error (for example a reference to a member that does not exist) | Fails at **Linting** |
| 2 | Fix commit 1, and violate a SHACL shape in a Markdown table editor (for example leave a required field empty) | Passes Linting, fails at **Validating** |
| 3 | Fix commit 2, and make the model inconsistent (for example type an instance by two disjoint concepts) | Passes Linting and Validating, fails at **Reasoning** |
| 4 | Fix commit 3, and set `project.version` to `1.0.0` in `.oml/settings.yml` | **All gates pass** |

1. Make the commit 1 change, run the gates locally to confirm it fails where you intend,
   then commit and push:

   ```bash
   git commit -am "Break lint"
   git push
   git rev-parse --short HEAD    # record this hash
   ```

2. Open a pull request. On the repo's GitHub page, click **Compare & pull request** in the
   yellow banner (or **Pull requests** -> **New pull request**). Set **base** to `main` and
   **compare** to `release-1.0.0`, give it a title such as *Release 1.0.0*, and click
   **Create pull request**.
3. Watch CI. At the bottom of the pull request's **Conversation** tab, the checks box shows
   the **CI / build** run. Click **Details** to open the log and confirm which step failed.
   Take a screenshot.
4. Repeat steps 1 and 3 for commits 2 and 3 (commit messages such as *Break validate* and
   *Break reason*). Every push to the branch reruns CI on the same pull request.
5. Push commit 4 (for example *Fix all gates for 1.0.0*) and wait for **All checks have
   passed**. The **Merge pull request** button turns green only now.
6. Review the pull request yourself:
   1. Click the **Files changed** tab and read the full diff.
   2. Click **Review changes** (or **Submit review**, top right).
   3. Write what you checked (for example: the diff has only the intended changes, and all
      three gates pass on the last commit).
   4. Select **Comment** (Approve is disabled for the author) and click **Submit review**.
7. Back on the **Conversation** tab, click **Merge pull request** -> **Confirm merge**.
   Choose **Create a merge commit** if offered a choice.

#### Tips

- **One gate per commit.** Each failing commit must pass every gate before the one it trips,
  so the log shows exactly which gate caught it.
- The **Commits** tab of the pull request lists all four commits with their hashes and a
  red or green mark for each CI run. Screenshot it as a summary.

### Deliverable 7: v1.0.0 release

1. Tag the **good commit as it landed on `main`** (the merge commit, which contains commit
   4) and push the tag:

   ```bash
   git switch main
   git pull
   git log --oneline -1          # confirm this is your merge commit; record its hash
   git tag -a v1.0.0 -m "Release 1.0.0"
   git push origin v1.0.0
   ```

2. On the repo's GitHub page, click **Releases** in the right sidebar (or go to
   `https://github.com/<owner>/<repo>/releases`), then **Draft a new release**.
3. Click **Choose a tag** and select `v1.0.0`.
4. Set **Release title** to `v1.0.0`. Click **Generate release notes**, then add a sentence
   on what the release contains.
5. Make sure **Set as the latest release** is checked and click **Publish release**.
6. Click the **Actions** tab and open the **CD** run started by the tag. Wait for both the
   **build** and **deploy** jobs to pass. The deploy job shows the site URL
   (`https://<owner>.github.io/<repo>/`).
7. Open the URL and check the rendered site. **Settings** -> **Pages** also shows *"Your
   site is live at ..."*.

#### Tips

- **Tags are not covered by your branch rule.** Tag only after the merge, so the release is
  exactly the reviewed, gated baseline.
- If CD fails, fix it through another pull request, then move the tag to the new commit
  (`git tag -d v1.0.0`, `git push origin :refs/tags/v1.0.0`, tag again) and say so in the
  report.

### Deliverable 8: GitHub Pages

Since `main` is protected, the README change also goes through a pull request.

1. Create a branch from the released `main`:

   ```bash
   git switch main
   git pull
   git switch -c readme-pages
   ```

2. Open `README.md` in VS Code. Directly under the `# ...` title line, add two badges, one
   for the release and one linking to your site, replacing `<owner>` with your GitHub user or organization and `<repo>` with the
   repo name (both appear in your repo's URL, `https://github.com/<owner>/<repo>`):

   ```markdown
   [![Release](https://img.shields.io/github/v/release/<owner>/<repo>)](https://github.com/<owner>/<repo>/releases/latest)
   [![Live Site](https://img.shields.io/badge/Live-Site-brightgreen)](https://<owner>.github.io/<repo>/)
   ```

   If your README already has a static badge (like Sierra's
   `![Release](https://img.shields.io/badge/Release-v0.1.0-blue)`), replace it with the release
   badge above, which updates itself with each new release.
3. Preview it: right-click `README.md` -> **Open Preview** (or `Cmd/Ctrl+Shift+V`). The release
   badge should read **release v1.0.0**, next to a green **Live Site** badge.
4. Commit and push:

   ```bash
   git commit -am "Link GitHub Pages and add release badge"
   git push -u origin readme-pages
   ```

5. Open a pull request into `main`, wait for CI to pass, review it with a **Comment**, and
   merge, as in Deliverable 6 steps 2 to 7.
6. Open the repo's main page on GitHub and confirm the release badge shows `v1.0.0`
   and clicking **Live Site** opens your site.

#### Tips

- This change comes **after** `v1.0.0`, so it is not part of the release. That is expected:
  do not move the tag for a README change.
- If the badge reads *no releases or repo not found*, the release is still a draft or the
  repo is private.

### Deliverable 9: Final report

The final report closes the course. Revisit the engineering questions from
[Project 1](Week1.md#project-1-week-1-project-scope-problem-statement) (Section 3), answer
them using the analyses developed in the method, and **trace every conclusion to the approved
1.0.0 release**. Then write your reflection on the whole approach.

For each Module 1 question:

| Part | What to include |
| --- | --- |
| Question | The question as written in Project 1, and its stakeholder. If you revised it since, give both versions and why. |
| Evidence | The query, view, analysis, or generated product that answers it, as a file path **at tag `v1.0.0`** (or a GitHub permalink to it) |
| Answer | The result, as computed from the release, and what it tells the stakeholder |
| Or a gap | If it cannot be answered, the diagnosed gap and its source (vocabulary, pattern, process, or outside the model), as in Week 5 |

Then the **reflection** (1-2 pages): what the approach made easier or harder than how you
work today, which capability mattered most (vocabularies, reasoning, methodology, analysis,
agents, generation, CI/CD), what you would change in your method, and what you would need
to use it on a real program.

#### Tips

- **Permalinks prove traceability.** Use links of the form
  `https://github.com/<owner>/<repo>/blob/v1.0.0/<path>` so each piece of evidence points at
  the released content, not at whatever `main` holds later.
- **A diagnosed gap is a finding**, not a failure. Say what blocks the answer and what would
  fix it.
- Reuse your Week 5 `ANALYSIS.md` as a starting point, updated to the release.

---

## Submitting Project 4

Push everything, then submit **Project 4** on the **UofA learning platform** as one document
covering Weeks 6, 7, and 8:

- **Week 6:** the project repo URL, the commit hash of the adjusted skill, the chat logs and
  git-change screenshots from rounds 1-3, and the round 4 report and chat log.
- **Week 7:** the URLs and commit hashes of the methodology repo and the example repo, the
  description of the generator, package, and consumer, and the evidence for each step.
- **Week 8:**
  - Screenshots of the secret (name only, never the value), the branch rule, and the rejected
    direct push to `main`.
  - The **pull request URL**, the **four commit hashes** with the CI result of each, and
    screenshots of each CI run and of the review comment and merge.
  - The **release URL**, the **commit hash tagged `v1.0.0`**, the CD run, and the **GitHub
    Pages URL**.
  - The final report: one answer or diagnosed gap per Module 1 question, traced to `v1.0.0`,
    and your reflection.
