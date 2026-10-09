# Week 7: Generate, Publish, and Consume

**Due before Module 8:**

1. [Assignment 7: Generate, Publish, and Consume Sierra](#assignment-7-generate-publish-and-consume-sierra)

**Project work continued this week and completed with Week 8:**

2. [Project 4 (Weeks 6-8), Week 7 component: Generate, Publish, and Reuse Your Methodology](#project-4-weeks-6-8-week-7-component-generate-publish-and-reuse-your-methodology)

> **Project numbering:** **Weeks 6, 7, and 8 together form Project 4.** This page describes the
> Week 7 component, which builds on the
> [Week 6 component](Week6.md#project-4-weeks-6-8-week-6-component-put-an-agent-to-work-on-your-method).
> Continue the same project through Week 8 and submit one cumulative **Project 4**, not a
> separate Week 7 project.

---

## Setup

### Step 1: Update OML Code and the OML CLI to the latest version

In VS Code, open **Extensions** (`Cmd/Ctrl+Shift+X`), find **OML Code**, and click **Update**
if offered. Then **reload the window** when prompted.

```bash
npm install -g @oml/cli@latest
oml -v
```

You need **Node.js 20 or later** for the local registry.

### Step 2: Sync your `sierra-method` fork

On **your fork's** GitHub page: click **Sync fork** -> **Update branch**. Then pull it
locally:

```bash
git pull
git status
```

Then **File -> Open Folder...** on the repo and run every command from VS Code's **integrated
terminal**.

### Step 3: Know how a Sierra generator is built

A generator is authored in OML, as instances of the `codegen` vocabulary. Study the two that
Sierra already ships before writing your own:

| File | What it holds |
| --- | --- |
| `src/method/oml/www.modelware.io/sierra/codegen/conops.oml` | `ConOpsSpec` and `ConOpsTemplate`: stakeholders and concerns to `ConOps.md` |
| `src/method/oml/www.modelware.io/sierra/codegen/statemachine.oml` | `StateMachineSpec` and `StateMachineTemplate`: a state machine to an XState `.js` file |
| `src/model/oml/fireforce6.github.io/mission-control/codegen/configs.oml` | `codegen:Config` instances that run those specs on Fire Force |

Each generator has three parts:

| Part | Lives in | Role |
| --- | --- | --- |
| `codegen:Spec` | `src/method` | A SPARQL `CONSTRUCT` (`codegen:construct`) that builds the data graph, plus its parameters (`codegen:parameter`), referenced as `${name}` |
| `codegen:Template` | `src/method` | A Nunjucks template (`codegen:body`) that renders one file from that graph |
| `codegen:Config` | `src/model` | Binds a spec to a context (`codegen:context`) and to parameter values (`codegen:binding`) |

Run a config by name (requires signing in to OML):

```bash
oml start
oml codegen -c conops --dry-run    # preview without writing files
oml codegen -c conops              # writes build/docs/ConOps.md
oml stop
```

### Step 4: Clone and start the local registry

Clone [modelware/verdaccio](https://github.com/modelware/verdaccio) **next to** your
`sierra-method` folder (not inside it), then start it in its own terminal:

```bash
git clone https://github.com/modelware/verdaccio.git
cd verdaccio
node registry.mjs
```

Leave it running. It serves http://localhost:4873 and logs npm in for you, so publishing needs
no further setup. Ctrl-C stops it. See its README for changing the port or starting over.

---

## Assignment 7: Generate, Publish, and Consume Sierra

Work in your `sierra-method` fork for Parts 1 and 2, and in a clone of `sierra-example` for
Part 3.

### Part 1: Create a transition-table generator

Create an OML-authored generator that produces a Markdown table of **source state**,
**triggering event**, and **target state**.

| # | Task | What to do |
| --- | --- | --- |
| 1 | Author the spec and template | Add a new `codegen:Spec` (SPARQL `CONSTRUCT`) and `codegen:Template` (Nunjucks) under `src/method`, for example `src/method/oml/www.modelware.io/sierra/codegen/transitions.oml`. |
| 2 | Parameterize it | The spec accepts an output `path` parameter. The table shows **(unspecified)** for a transition with no triggering event. |
| 3 | Configure it for Fire Force | Add a `codegen:Config` in Fire Force's `configs.oml` that runs your spec on the `MissionControlDashboard` state machine with `path` bound to `build/docs`. |
| 4 | Generate and verify | Run it to produce `build/docs/MissionControlDashboard-transitions.md`. Verify that **every transition appears exactly once**. |
| 5 | Commit and push | Commit the generator and the config, and push to your fork. |

The generated table should have this shape (rows abbreviated):

| Source state | Event | Target state |
| --- | --- | --- |
| DashboardInitial | history_response | Monitoring |
| Monitoring | alert | AlertHandling |
| AlertHandling | alert_acknowledgement | Monitoring |
| Monitoring | *(unspecified)* | Idle |

#### Tips

- **Start from `statemachine.oml`.** Its `CONSTRUCT` already finds the states of a machine,
  their transitions (`oml:hasSource`, `oml:hasTarget`), and the optional event
  (`state:isTriggeredBy`). You need less than it does.
- **Missing events.** Keep the event inside an `OPTIONAL` so transitions without one are not
  dropped, then handle the empty value in the template (for example with `or` or the
  `default` filter).
- **Exactly once.** Count the `state:Transition` instances in `sm1.oml` and compare with the
  rows in your table. Duplicates usually come from a cross-join in the `WHERE` clause; missing
  rows usually come from a required pattern that should be `OPTIONAL`.
- **Iterate with `--dry-run`** until the output looks right, then generate the file.
- `build/` is git-ignored, so the generated file is not committed. Put its content in your
  report instead.

### Part 2: Package and publish Sierra

| # | Task | What to do |
| --- | --- | --- |
| 1 | Review the package settings | Pull latest in `sierra-method`. In `.oml/settings.yml`, review the package `project.version`, the files included under `pack`, and anything excluded. |
| 2 | Pack and inspect | Pack the package and list the archive's contents. Confirm your generator from Part 1 is in it. |
| 3 | Run the registry | Make sure the local registry from Setup Step 4 is running. |
| 4 | Preview, publish, confirm | Preview the publication with `--dry-run`, publish a **new version**, and confirm it is available in the registry. |
| 5 | Screenshots | Take a screenshot of each step and add a short comment explaining what it shows. |

```bash
oml pack -o build/dist
tar -tzf build/dist/*.tgz
oml publish -r http://localhost:4873 --dry-run
oml publish -r http://localhost:4873
npm view @modelware/sierra-method versions --registry http://localhost:4873
```

#### Tips

- **New version.** A version can be published only once. Raise `project.version` in
  `.oml/settings.yml` (for example `0.1.0` -> `0.2.0`) before publishing, and commit that
  change.
- **Only `src/method` ships.** The Fire Force model under `src/model` is not part of the
  package, which is why the generator's spec and template must live under `src/method` and
  only the config lives in the model.
- You can also browse http://localhost:4873 to confirm the new version.

### Part 3: Consume the published package

| # | Task | What to do |
| --- | --- | --- |
| 1 | Clone and install | Clone [sierra-example](https://github.com/modelware/sierra-example) and install **the version you published** in Part 2 from the local registry. |
| 2 | Confirm the install | Confirm Sierra was installed under `deps/@modelware/sierra-method/`, at your version, and that your generator is there. |
| 3 | Open the notebooks | Open `sierra-example` in OML Code and open the files in `src/md` (`Stakeholders.md`, `StateMachine.md`). Confirm they load and render with Sierra's templates. |
| 4 | Screenshots | Take a screenshot of each step and add a short comment explaining what it shows. |

```bash
git clone https://github.com/modelware/sierra-example.git
cd sierra-example
oml install @modelware/sierra-method@<your-version> -r http://localhost:4873
```

#### Tips

- **Name your version.** A plain `oml install` installs what `.oml/settings.yml` declares;
  naming the version moves the dependency to the one you published and saves it there.
- **Keep the registry running.** Install fails if the registry from Setup Step 4 is stopped.
- If the notebooks show unresolved imports, check the `deps/` folder and reload the VS Code
  window.

### Submitting

Push your work to your `sierra-method` fork, then submit a document on the **UofA learning
platform** with:

- Your **fork URL** and the **commit hash** of the generator (Part 1).
- The content of the generated `MissionControlDashboard-transitions.md`, and how you verified
  every transition appears exactly once.
- The commented screenshots for Parts 2 and 3.

---

## Project 4 (Weeks 6-8), Week 7 Component: Generate, Publish, and Reuse Your Methodology

Do the same three steps on your own method: give it a generator, publish it, and prove it
works from an independent repository.

| # | Deliverable | Minimum |
| --- | --- | --- |
| 1 | Extend your methodology with a generator | A reusable generator (spec and template under your method's source) that produces **non-trivial** code or a document from a model. It may be one you identified in the first lecture. |
| 2 | Publish your methodology | Package its ontologies, editor templates, generation specifications, and supporting files. Inspect the archive and publish a version to your local registry. |
| 3 | Create an independent consumer | A separate example repository that installs your published methodology and uses it end to end (see below). |

The consumer repository must:

- Declare and install your published methodology as a package.
- Create new description files that use the methodology's vocabularies.
- Create one (or more) Markdown files using the methodology's editor templates.
- Use those Markdown files to add model elements that the generator will use.
- Generate the textual product on the new descriptions using the **installed** generator from
  Deliverable 1.

### Tips

- **Non-trivial** means the output depends on the model's structure: it iterates, nests, or
  joins across several concepts and relations, not one label printed into a fixed file.
- **Mirror the Sierra layout.** Keep reusable content (vocabularies, editor templates, specs,
  templates) in the packaged folder and configure `pack` in `.oml/settings.yml` to include
  it. Keep the configs with the models that use them.
- **Model `sierra-example` for the consumer.** Copy its `.oml/settings.yml` shape: a
  `dependencies` entry for your package and a `codegen:Config` in the consumer that points at
  the spec inside `deps/`.
- **Inspect before publishing.** List the archive and check that every file the consumer
  needs is there. A missing template or spec shows up only when the consumer tries to use it.
- **Republishing.** If the consumer exposes a gap in the package, fix the method, raise
  `project.version`, publish again, and install the new version in the consumer.

### Saving Your Week 7 Work

Commit and push both repositories, and continue from them in Week 8. Preserve the following
evidence for the cumulative Project 4 submission:

- The **URLs and commit hashes** of your methodology repo and of the new example repo.
- A description of what you did: the generator and what it produces, what the package
  contains, and how the consumer uses it.
- Evidence of each step: the archive listing, the publish output, the installed `deps/`
  folder, the Markdown files in use, and the generated product.
