# Week 2 — Representation I: The Basics of OML

You have a working environment. Now you write OML.

**Due before Module 3:**

1. [Assignment 2 — Extend Fire Force VI](#assignment-2--extend-fire-force-vi) — extend the
   Mission Control description using only this module's constructs.

**Due before Module 4:**

2. [Deliverable 2 — Vocabulary and Basic Model](#deliverable-2--vocabulary-and-basic-model) —
   your own vocabulary plus a first system model.

> **New this week: you submit a commit, not a ZIP.** [Step 1](#step-1--fork-dont-just-clone)
> changes how you work with the repo — fork it, so you can commit. Do that before you write
> any OML.

---

## 1. What This Module Covered

Six constructs. Everything else in OML builds on them.

| # | Construct | Keyword | What it does |
| --- | --- | --- | --- |
| 1 | **Specialization** | `<` | Subset relationship between concepts — builds a taxonomy |
| 2 | **Scalar properties** | `scalar property` | Attaches a *literal* value (string, double, enum) |
| 3 | **Relations** | `relation` | Attaches another *instance* — builds the knowledge graph |
| 4 | **Instances** | `instance`, `ref` | The actual system model; asserts types, values, relations |
| 5 | **Cardinality restrictions** | `restricts`, `functional` | Constrains how many values are allowed |
| 6 | **Patterns** | (composition) | Containment, aggregation, connectivity, traceability |

### The four ideas that trip people up

These are the ones worth re-reading before you start the assignment.

**1. Specialization is a subset relationship, not code inheritance.** `Signal < Item` means
*every* instance of `Signal` is an instance of `Item` — always, not usually. It is not
containment, and it is not equivalence. Unlike a programming language, a cycle is not an
error: if you assert `A < B` and `B < A`, the reasoner concludes `A` and `B` are equivalent
classes. Multiple specialization is normal.

**2. Domain and range are inference opportunities, not constraints.** This is the big one.
If `mass` has `domain PhysicalPart` and you assert a mass on something, the reasoner does
**not** complain that the thing isn't a physical part — it *concludes* that it must be one.
You will notice this when your element starts appearing in views you did not expect.

```
scalar property priority [ domain Prioritized  range Priority  functional ]
```

Read that as *"anything I put a priority on is thereby Prioritized"* — not as *"only
Prioritized things may have a priority."*

**3. Open world: absent does not mean false.** If you don't state a value, the reasoner
concludes *unknown*, not *missing*. So a `minimum 1` restriction will **not** fire just
because you left a value out. Maximum restrictions are easy to enforce (two distinct values
contradict `maximum 1`); minimum restrictions are not. That is what `oml validate` and SHACL
are for — validation is closed-world, reasoning is open-world.

**4. Different names do not mean different things.** Given a `functional` relation and two
asserted values, the reasoner takes the path of least resistance and infers the two
instances are *the same individual* — rather than flagging an error. The `oml reason` CLI
enables the unique names assumption by default (`-u`), which is what makes it flag the
contradiction instead.

### Declaring vs. restricting

Declaring a property or relation gives you the default multiplicity — `0..*`. Nothing is
required, and any number is allowed.

| To say... | Use | Where |
| --- | --- | --- |
| At most one value, globally | `functional` | On the relation/property declaration |
| At most one value, other direction | `inverse functional` | On the relation declaration |
| At least one value of type T | `restricts some p to T` | On the concept |
| Every value must be of type T (zero OK) | `restricts all p to T` | On the concept |
| An explicit count | `restricts p to min/max/exactly N` | On the concept |

`exactly N` is sugar for minimum and maximum at the same value.

> **A word on minimum restrictions.** Be careful putting them in a vocabulary. An
> inconsistent model blocks *all* downstream analysis — garbage in, garbage out. Early in a
> project your model is legitimately incomplete, and demanding completeness as a
> consistency gate stops you from working. Enforce **integrity** with the reasoner (a person
> with two social security numbers is plainly wrong); check **completeness** with validation
> (a requirement with no verification yet is merely unfinished).

### The Sierra taxonomy, in one sentence

Sierra's `base.oml` defines abilities as aspects — `Element` (can have a `description`),
`Expressible` (an `expression`), `Prioritized` (a `priority`), `Categorized` (a `category`),
`Container` / `Contained` — and every concept specializes the abilities it needs. That is why
asserting `R1 : Requirement` entails that `R1` is also `Categorized`, `Prioritized`,
`Expressible`, and an `Element`.

---

## 2. Seeing What the Reasoner Sees

You write assertions. The reasoner adds **entailments**. To look at them:

```bash
oml start                 # the CLI needs a running server (or open the folder in VS Code)
oml export -o build/owl   # your assertions, translated to OWL/Turtle
oml reason  -o build/owl  # the same, plus a *.ttl of what the reasoner inferred
```

Open the generated file for `stakeholders` and find `FireFighter`. You asserted only that it
is a `Stakeholder`; the entailment file also says it is an `Element`, `Categorized`, and a
`Thing`. Assertions and entailments live in **separate graphs**, which is what lets a query
scope itself to one, the other, or both.

Do this once by hand. It is the fastest way to internalize what your statements mean.

---

## 3. Setup for This Week

### Step 1 — Fork, don't just clone

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

### Step 2 — Read before you write

The assignment is graded on *following the existing conventions*, so spend real time here.
Two folders:

| Folder | What's in it |
| --- | --- |
| `src/method/oml/www.modelware.io/sierra/` | The Sierra **vocabularies** — `base`, `component`, `stakeholder`, `mission`, `entity` |
| `src/model/oml/fireforce6.github.io/mission-control/` | The Fire Force VI **descriptions** — organized by analysis phase |

Navigate with the editor, not by scrolling. `Cmd`/`Ctrl` + hover shows a definition;
`Cmd`/`Ctrl` + click jumps to it; the back/forward buttons return you. Right-click an
ontology → **Open Diagram** to see a vocabulary graphically, or **Show Diagram** on a
description. Diagrams are live — edit a value and the diagram updates.

Read at least these five files before starting:

- `base.oml` — the aspects and scalar properties everything reuses
- `component.oml` — `Component`, `PhysicalPart`, `Port`, `direction`, `Connection`, `transfers`
- `stakeholder.oml` — `Stakeholder`, `Concern`, `Requirement`, `expresses`, `states`
- `system-analysis/connections.oml` — **the pattern your ports and connections must follow**
- `system-analysis/masses.oml` — how `ref instance` adds mass without touching `components.oml`

---

## 4. The Patterns You Are Extending

Four patterns recur throughout Sierra. Recognize them and the assignment writes itself.

### Containment — one parent, many children

```
instance Payload : component:Component [
    base:description "Wildfire Detection Payload"
    base:isContainedBy FireSat
]
```

`Contains` is declared `inverse functional`: a thing has **at most one** container. That
single flag is what makes the containment tree a tree, and what makes the decomposition view
possible. You assert one direction; the reasoner infers `contains` the other way.

### Aggregation — grouping, not containment

Aggregation is a grouping hierarchy used for roll-ups (mass, cost, power). Unlike
containment it is *not* functional — an element can belong to several groups, and you can
build a logical hierarchy (say, by safety concern) that differs from the physical one.

### Connectivity — components, ports, connections, items

The full pattern, and the one Assignment 2 leans on hardest:

```
ref instance components:Platform [
    component:hasPort Platform.Power_Out
]
instance Platform.Power_Out : component:Port [ component:direction "Out" ]

relation instance Conn_PlatformPower_to_Payload : component:Connection [
    from Platform.Power_Out
    to   Payload.Power_In
    component:transfers ElectricalPower
]
```

Three things to copy exactly: the port naming convention `Component.Port_Name`, a
`direction` on every port, and a `transfers` on every connection. A port with no direction
and a connection that transfers nothing both *validate* — and both are meaningless.

### Traceability — multi-hop, and where the analysis value is

There is no direct relation from a `Requirement` to a `Concern`. The trace runs **through the
stakeholder**:

```
Requirement --isStatedBy--> Stakeholder --expresses--> Concern
                                                          |
                                                       derives
                                                          v
Capability <--requires-- Objective <--pursues-- Mission
```

Every one of those relations has a declared `reverse`, so the reasoner populates both
directions and you can query from either end. This chain is what answers *"if this
stakeholder leaves, which requirements go with them?"* — the reason for declaring relations
at all instead of writing prose in a description field.

---

## Assignment 2 — Extend Fire Force VI

**Due before Module 3.** Extend a portion of the Mission Control model using only this
module's constructs.

You are a new member of the Fire Force VI team adding an increment to the description. This
is **description-layer work**: use the existing Sierra vocabularies, do not modify them.

| # | Task | Notes |
| --- | --- | --- |
| 1 | **5+ component instances** added to a subsystem | Use `base:isContainedBy` — extend an existing branch |
| 2 | **Ports** on 2+ of them, each with a `direction` | Follow the `Component.Port_Name` convention |
| 3 | **2+ connections** between ports; say what each `transfers` | Read `connections.oml` first |
| 4 | **2 requirements**, each `isStatedBy` a real stakeholder | Use `stakeholders.oml` |
| 5 | **Trace one requirement** to a concern and a capability | The multi-hop pattern above |
| 6 | **Masses** on new physical parts via `ref instance` | Leaves only — no roll-ups |
| 7 | **Build clean** | The graded bar |

| Do | Don't |
| --- | --- |
| Use the existing Sierra vocabularies | Modify Sierra — this is description-layer work |
| Follow the existing naming conventions | Invent a new scheme |
| Write `@dc:description` on everything | Leave things undocumented |

### The three gates

Run all three after every few edits, not once at the end.

```bash
oml lint      # syntax and well-formedness
oml validate  # closed-world checks (SHACL) — catches the completeness problems reasoning won't
oml reason    # DL consistency — must report no inconsistencies
```

Then open the viewpoints and confirm **your additions actually appear**. The views only
render the patterns; if you followed the pattern, your instance shows up. If it doesn't, you
deviated somewhere — that is the feedback loop, and it is faster than reading your own diff.

### How to submit

No ZIP this time.

1. Commit your changes to your fork with a descriptive message.
2. `git push`.
3. Copy the **commit hash** — GitHub shows it on the commit page.
4. Email me **the URL of your public fork** and **that commit hash**.

I clone your fork, check out your hash, run the three gates, read your OML, and look at your
work through the viewpoints. Commit hashes are unambiguous, which is the point.

**Graded on:** builds clean, conventions followed, and **semantically sensible** — a port
with no direction or a connection transferring nothing loses points *even though it
validates*.

---

## Deliverable 2 — Vocabulary and Basic Model

**Due before Module 4** (not this week — you get Module 3 first). Building directly on your
Module 1 scope statement. This is your own project, in your own repo.

### Starting the repo

```bash
mkdir my-project && cd my-project
oml init
```

`oml init` scaffolds into the **current** folder — it does not take a project name and will
not create the directory for you, so make the folder first; its name seeds the defaults.
Five prompts, each with a default in brackets:

| Prompt | Default for a folder named `my-project` |
| --- | --- |
| Project title | `My Project` |
| Package name | `@oml/my-project` |
| Base IRI (namespace) | `https://example.com/my-project` |
| Description | `The My Project ontologies.` |
| Initial contents | `both` (vocabularies / descriptions / both) |

**Override the base IRI** — the default is an `example.com` placeholder. Use something you
control, the way Sierra uses `www.modelware.io` for the method and `fireforce6.github.io` for
the model.

`oml init -y` accepts all defaults; `-k both` presets the contents; `-f` overwrites instead
of skipping. Init refuses to run if `.oml/settings.yml` already exists unless you pass `-f`.

One rule matters more than the rest: **an ontology's namespace comes from its file path**,
relative to the nearest ancestor folder named `oml`.

```
src/oml/acme/sensors/Sensor.oml   →   http://acme/sensors/Sensor#
```

Keep that in mind and your folder tree stays in sync with your IRI space automatically. To
add ontologies later, use **Source Explorer → right-click a folder → New Ontology…**, which
asks for kind (vocabulary, vocabulary bundle, description, description bundle) and name and
derives the namespace and prefix for you.

You do not need to vendor the core vocabularies — `oml`, `dc`, `rdf`, `rdfs`, `xsd`, `owl`,
`codegen`, and `diagram` ship with the language server and import with no dependency
declaration.

### Part A — Initial vocabulary

| # | Requirement | Guidance |
| --- | --- | --- |
| 1 | **10–20 concepts** | Fewer and well-chosen beats many and vague |
| 2 | **A taxonomy with real specialization** | Every subtype must add something |
| 3 | **5–10 scalar properties**, domain + range | Enumerate closed value sets |
| 4 | **5–10 relations**, `from` / `to` / `reverse` | Verb phrases that read as sentences |
| 5 | **Cardinality restrictions where confident** | Constrain what you *know* |

### Part B — Basic system model

| # | Requirement | Guidance |
| --- | --- | --- |
| 6 | **15–30 instances** of your system | Real names, annotations |
| 7 | **1–5 scalar property assertions** per instance | Mass, priority, etc. |
| 8 | **Traceability chains, 3+ levels** | Like `pursues`, `satisfies` |
| 9 | **Some recursive relation hierarchies** | Like `contains`, `aggregates` |
| 10 | **Lints clean** | Non-negotiable |

### Two things to hold yourself to

**Check against your Module 1 questions.** You wrote 3–5 questions your model should answer.
Walk each one through your vocabulary. If a question needs a relationship you haven't
declared, **you've found your next concept.** Those questions are your acceptance criteria —
use them while building, not after.

The inverse also applies: don't add vocabulary you have no intention of asking questions
about. It becomes overhead. A minimal vocabulary, fit for your questions, is the goal.

**Take inspiration from the running example where it fits.** Make it thoughtful and
interesting. By the time this is due you will have seen Module 3, so **all OML constructs are
usable** — this lecture's and the next one's, not just the six above.

### Submitting

Same mechanism as Assignment 2: a public repo URL plus a commit hash. Going forward you keep
two repos — your `sierra-method` fork for assignments, and your project repo — and every
delivery is a URL and a hash.

---

## Further Reading

Not required, but this is the week the reading actually pays off.

| Ref | Source | Why |
| --- | --- | --- |
| REF06 | [Ontology Development 101 — Stanford](https://protege.stanford.edu/publications/ontology_development/ontology101.pdf) | Probably the best introductory reading for this week. Classes, properties, instances, taxonomies, restrictions — and, importantly, the idea that ontology development is an iterative modeling activity rather than writing syntax |
| REF07 | [OWL 2 Primer — W3C](https://www.w3.org/TR/owl2-primer/) | Concentrate on classes, individuals, properties, hierarchies, domains/ranges, and simple restrictions rather than reading the whole spec. It deliberately progresses from simple representation toward more expressive constructs |
| REF08 | [Ontology-based systems engineering — a state-of-the-art review](https://doi.org/10.1016/j.compind.2019.05.003) | Answers *why would a systems engineer care about any of this?* Surveys how ontologies have been applied across SE knowledge areas and emphasizes explicit, shareable, reusable semantics |
| REF09 | [The Case for Integrated Model Centric Engineering (openCAESAR)](https://opencaesar.io/) | Argues that conventional MBSE is limited by informal semantics, fragmented tools, weak interoperability, and inadequate configuration/provenance management |

---

**Next: Module 3 — Representation II: Advanced Features of OML.** Reification and n-ary
relations, advanced restrictions, property characteristics, quantities, identity,
architecture, and SE patterns.
