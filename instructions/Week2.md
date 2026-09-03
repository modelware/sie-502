# Week 2: Representation I: The Basics of OML

You have a working environment. Now you write OML.

**Due before Module 3:**

1. [Assignment 2: Extend Fire Force VI](#assignment-2-extend-fire-force-vi) - extend the
   shared Mission Control example using only this module's constructs.

**Due before Module 4:**

2. [Project Deliverable 2: Initial Vocabulary and Description](#project-deliverable-2-initial-vocabulary-and-description) -
   the first OML for **your own project**: an initial vocabulary and a description.

> **New this week: you submit a commit, not a ZIP.** [Step 1](#step-1-fork-dont-just-clone)
> changes how you work with the repo - fork it, so you can commit. Do that before you write
> any OML.

---

## 1. Seeing What the Reasoner Sees

You write assertions. The reasoner adds **entailments**. To look at them:

```bash
# open the project folder in VS Code first: the extension starts the server for you
oml export -o build/owl   # your assertions, translated to OWL/Turtle
oml reason  -o build/owl  # the same, plus a *.ttl of what the reasoner inferred
```

Open the generated file for `stakeholders` and find `FireFighter`. You asserted only that it
is a `Stakeholder`; the entailment file also says it is an `Element`, `Categorized`, and a
`Thing`. Assertions and entailments live in **separate graphs**, which is what lets a query
scope itself to one, the other, or both.

Do this once by hand. It is the fastest way to internalize what your statements mean.

---

## 2. Setup for This Week

### Step 1: Fork, don't just clone

Last week you cloned `sierra-method`. You cannot commit to my repo. This week you need to,
so make your own copy:

1. Go to https://github.com/modelware/sierra-method
2. Click **Fork** (top right) → choose your account as the destination
3. Clone **your fork**:

```bash
git clone https://github.com/YOUR_USERNAME/sierra-method.git
```

4. **File → Open Folder…** in VS Code and select it.
5. Confirm the fork is **public** (Settings → General → Danger Zone) so I can read it.

### Step 2: Read before you write

The assignment is graded on *following the existing conventions*, so spend real time here.
Two folders:

| Folder | What's in it |
| --- | --- |
| `src/method/oml/www.modelware.io/sierra/` | The Sierra **vocabularies** - `base`, `component`, `stakeholder`, `mission`, `entity` |
| `src/model/oml/fireforce6.github.io/mission-control/` | The Fire Force VI **descriptions** - organized by analysis phase |

Navigate with the editor, not by scrolling. `Cmd`/`Ctrl` + hover shows a definition;
`Cmd`/`Ctrl` + click jumps to it; the back/forward buttons return you. Right-click an
ontology → **Open Diagram** to see a vocabulary graphically, or **Show Diagram** on a
description. Diagrams are live - edit a value and the diagram updates.

Read at least these five files before starting:

- `base.oml` - the aspects and scalar properties everything reuses
- `component.oml` - `Component`, `PhysicalPart`, `Port`, `direction`, `Connection`, `transfers`
- `stakeholder.oml` - `Stakeholder`, `Concern`, `Requirement`, `expresses`, `states`
- `system-analysis/connections.oml` - **the pattern your ports and connections must follow**
- `system-analysis/masses.oml` - how `ref instance` adds mass without touching `components.oml`

---

## Assignment 2: Extend Fire Force VI

**Due before Module 3.** Extend a portion of the Mission Control model using only this
module's constructs.

You are a new member of the Fire Force VI team adding an increment to the description. This
is **description-layer work**: use the existing Sierra vocabularies, do not modify them.

| # | Task | Notes |
| --- | --- | --- |
| 1 | **5+ component instances** added to a subsystem | Use `base:isContainedBy` - extend an existing branch |
| 2 | **Ports** on 2+ of them, each with a `direction` | Follow the `Component.Port_Name` convention |
| 3 | **2+ connections** between ports; say what each `transfers` | Read `connections.oml` first |
| 4 | **2 requirements**, each `isStatedBy` a real stakeholder | Use `stakeholders.oml` |
| 5 | **Trace one requirement** to a concern and a capability | Follow an existing chain in `requirements.oml` |
| 6 | **Masses** on new physical parts via `ref instance` | Leaves only - no roll-ups |
| 7 | **Build clean** | The graded bar |

| Do | Don't |
| --- | --- |
| Use the existing Sierra vocabularies | Modify Sierra - this is description-layer work |
| Follow the existing naming conventions | Invent a new scheme |
| Write `@dc:description` on everything | Leave things undocumented |

### The three gates

Run all three after every few edits, not once at the end. Run them in the VS Code integrated
terminal, so the extension's server is already there to answer.

```bash
oml lint      # syntax and well-formedness
oml validate  # closed-world checks (SHACL) - catches the completeness problems reasoning won't
oml reason    # DL consistency - must report no inconsistencies
```

Then open the viewpoints and confirm **your additions actually appear**. The views only
render the patterns; if you followed the pattern, your instance shows up. If it doesn't, you
deviated somewhere - that is the feedback loop, and it is faster than reading your own diff.

### How to submit

No ZIP this time.

1. Commit your changes to your fork with a descriptive message.
2. `git push`.
3. Copy the **commit hash** - GitHub shows it on the commit page.
4. On the **UofA learning platform**, submit a document stating two things: the **URL of your
   public fork** and the **commit hash**.

I clone your fork, check out your hash, run the three gates, read your OML, and look at your
work through the viewpoints. Commit hashes are unambiguous, which is the point.

**Graded on:** builds clean, conventions followed, and **semantically sensible** - a port
with no direction or a connection transferring nothing loses points *even though it
validates*.

---

## Project Deliverable 2: Initial Vocabulary and Description

**Due before Module 4** (not this week - you get Module 3 first).

This is the **project** track, not the assignment track: your own system, your own repo, built
on the scope statement you wrote for Project Deliverable 1. Nothing here touches
`sierra-method` - you start a new project from scratch and grow it every module for the rest of
the quarter.

### Starting the repo

```bash
mkdir my-project && cd my-project
oml init
```

`oml init` scaffolds into the **current** folder, so make the folder first - its name seeds the
defaults. Press Enter through all five prompts except **Base IRI**, which you set to
`http://www.example.com/method`. (Accepting `both` for the last one gives you a vocabulary *and*
a description.)

The base IRI is a prefix - init appends `/vocabulary` and `/description` - so you get
`http://www.example.com/method/vocabulary#`. An IRI *names* an ontology, it is not an address:
nothing is fetched, the domain need not exist, everything runs locally. Rename it later if you
like, keeping the folders in step.

### Open it in VS Code: before any other command

**File → Open Folder…** on `my-project`, the folder holding `.oml/`. The OML Code extension
starts the server for you: no `oml start`, no port, no login. Do this first - every `oml`
command needs that server, and without it `oml lint` just answers `start server first`.

Run the CLI from the window's **integrated terminal** (Terminal → New Terminal); a shell
started elsewhere will not find the server.

### Separate the method from the model

Init put both files in one tree. Split them the way Sierra does - vocabulary (your *method*)
under `src/method`, description (your *model*) under `src/model`.

**1. Give the description its own IRI.** Open `src/oml/example.com/my-project/description.oml`
and change the namespace on line 3 - `method` becomes `project`:

```diff
-description <http://www.example.com/method/description#> as myproject-desc {
+description <http://www.example.com/project/description#> as myproject-desc {
```

Leave the rest of the file alone, including the `uses` line pointing at the vocabulary.

**2. Move the files.**

```bash
mkdir -p src/method/oml/www.example.com/method
mkdir -p src/model/oml/www.example.com/project

mv src/oml/example.com/my-project/vocabulary.oml  src/method/oml/www.example.com/method/
mv src/oml/example.com/my-project/description.oml src/model/oml/www.example.com/project/
rm -rf src/oml
```

**3. Check it.** Your project should now look exactly like this - seven files, and no `src/oml`
left over:

```
my-project/
├── .gitignore
├── .mcp.json
├── .vscode/
│   └── settings.json
├── .oml/
│   └── settings.yml
├── README.md
└── src/
    ├── method/
    │   └── oml/
    │       └── www.example.com/
    │           └── method/
    │               └── vocabulary.oml
    └── model/
        └── oml/
            └── www.example.com/
                └── project/
                    └── description.oml
```

The two `.oml` files are the ones you work in; the rest is project configuration you can leave
alone. Their namespaces:

| File | Namespace |
| --- | --- |
| `…/method/oml/www.example.com/method/vocabulary.oml` | `http://www.example.com/method/vocabulary#` |
| `…/model/oml/www.example.com/project/description.oml` | `http://www.example.com/project/description#` |

Each path after its `oml/` folder reads back as the IRI. **That is the whole rule**: a namespace
is derived from the segments after the deepest `oml` folder - which is why `method/` and `model/`
*in front* of the anchor change nothing. If the two ever disagree the ontology cannot be found,
and the error surfaces in the file that *imports* it, so check folder names first.

Then `oml lint`, which should report `11 OML file(s) checked` - your two, plus nine core
vocabularies that live inside the language server rather than in your project.

That is your **first working base**: it loads, lints, and reasons. Part A fills in
`vocabulary.oml` and Part B fills in `description.oml`, replacing the placeholder `Component` /
`ComponentA` content with your own. To add further ontologies as you go, use **Source Explorer →
right-click → New Ontology…**, which derives the namespace and prefix for you.

### Part A: The vocabulary

| # | Requirement | Guidance |
| --- | --- | --- |
| 1 | **10-20 concepts** | Fewer and well-chosen beats many and vague |
| 2 | **A taxonomy with real specialization** | Every subtype must add something |
| 3 | **5-10 scalar properties**, domain + range | Enumerate closed value sets |
| 4 | **5-10 relations**, `from` / `to` / `reverse` | Verb phrases that read as sentences |
| 5 | **Cardinality restrictions where confident** | Constrain what you *know* |

### Part B: The description

| # | Requirement | Guidance |
| --- | --- | --- |
| 6 | **15-30 instances** of your system | Real names, annotations |
| 7 | **1-5 scalar property assertions** per instance | Mass, priority, etc. |
| 8 | **Traceability chains, 3+ levels** | Like `pursues`, `satisfies` |
| 9 | **Some recursive relation hierarchies** | Like `contains`, `aggregates` |
| 10 | **Lints clean** | Non-negotiable |

### Two things to hold yourself to

**Check against your Module 1 questions.** You wrote 3-5 questions your model should answer.
Walk each one through your vocabulary. If a question needs a relationship you haven't
declared, **you've found your next concept.** Those questions are your acceptance criteria -
use them while building, not after.

The inverse also applies: don't add vocabulary you have no intention of asking questions
about. It becomes overhead. A minimal vocabulary, fit for your questions, is the goal.

**Take inspiration from the running example where it fits.** Make it thoughtful and
interesting. By the time this is due you will have seen Module 3, so **all OML constructs are
usable** - this lecture's and the next one's, not just this module's six.

### Submitting

Same mechanism as Assignment 2: push your work, then submit a document on the **UofA learning
platform** stating your **project repo URL** and the **commit hash**. Going forward you keep two
repos - your `sierra-method` fork for assignments, and your project repo - and every delivery is
a document naming a URL and a hash.

---

## Further Reading

Not required, but this is the week the reading actually pays off.

| Ref | Source | Why |
| --- | --- | --- |
| REF06 | [Ontology Development 101: Stanford](https://protege.stanford.edu/publications/ontology_development/ontology101.pdf) | Probably the best introductory reading for this week. Classes, properties, instances, taxonomies, restrictions - and, importantly, the idea that ontology development is an iterative modeling activity rather than writing syntax |
| REF07 | [OWL 2 Primer: W3C](https://www.w3.org/TR/owl2-primer/) | Concentrate on classes, individuals, properties, hierarchies, domains/ranges, and simple restrictions rather than reading the whole spec. It deliberately progresses from simple representation toward more expressive constructs |
| REF08 | [Ontology-based systems engineering: a state-of-the-art review](https://doi.org/10.1016/j.compind.2019.05.003) | Answers *why would a systems engineer care about any of this?* Surveys how ontologies have been applied across SE knowledge areas and emphasizes explicit, shareable, reusable semantics |
| REF09 | [The Case for Integrated Model Centric Engineering (openCAESAR)](https://opencaesar.io/) | Argues that conventional MBSE is limited by informal semantics, fragmented tools, weak interoperability, and inadequate configuration/provenance management |

---

**Next: Module 3 - Representation II: Advanced Features of OML.** Reification and n-ary
relations, advanced restrictions, property characteristics, quantities, identity,
architecture, and SE patterns.
