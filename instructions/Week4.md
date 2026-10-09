# Week 4: Methodology I

**Due before Module 5:**

1. [Assignment 4: Build One Fire Force Authoring Page End to End](#assignment-4-build-one-fire-force-authoring-page-end-to-end)

**Project work begun this week and submitted after Week 5:**

2. [Project 3 (Weeks 4-5), Week 4 component: Turn Your Vocabulary Into a Small Methodology](#project-3-weeks-4-5-week-4-component-turn-your-vocabulary-into-a-small-methodology)

> **Project numbering:** **Weeks 4 and 5 together form Project 3.** Week 4 builds the
> methodology; Week 5 adds its analysis layer. Submit one cumulative **Project 3** after
> completing Week 5, not a Week 4-only project submission.

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

---

## Assignment 4: Build One Fire Force Authoring Page End to End

Study with running example: Sierra Method + Fire Force system description.

1. Pick one pattern not already covered by a Sierra template
2. Write its focal type, structure, expected content, and rules
3. Implement the shape
4. Put it in a table-editor or tree-editor
5. Add one useful business-rule validation message
6. Add read-only context if the task needs it
7. Wrap the page as a compose template
8. Invoke it and use it to create three real instances
9. Deliberately break one rule, observe the message, and fix it

### Submitting

Push your work to your `sierra-method` fork, then submit a document on the **UofA learning
platform** with your **fork URL** and the **commit hash**.

---

## Project 3 (Weeks 4-5), Week 4 Component: Turn Your Vocabulary Into a Small Methodology

Narrative should explain rationale, alternatives, uncertainty, and open issues, not restate
the model.

| Deliverable | Minimum |
| --- | --- |
| Description patterns | 4 patterns |
| Description layout | Folders / files + rationale |
| Editors | One per pattern |
| Business rules | >= 2 |
| Notebook | Narrative + live / editor content |
| Compose templates | All editors reusable |
| Dogfooding | >= 10 instances through your editors |
| METHOD.md | What the method prescribes and why |

### Saving Your Week 4 Work

Commit and push this work to your project repository. Continue from it in Week 5; the
cumulative Project 3 submission is due after the Week 5 analysis layer is complete.
