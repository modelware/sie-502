# Week 3: Representation II: Reasoning over OML

Last week you wrote assertions. This week you make the **reasoner** talk back: entailments,
inconsistencies, explanations, and rules.

**Due before Module 4:**

1. [Assignment 3: Reasoning Experiments](#assignment-3-reasoning-experiments) - eight
   experiments on `sierra-method`, with screenshots and explanations.
2. [Project Deliverable 2: Initial Vocabulary and Description](Week2.md#project-deliverable-2-initial-vocabulary-and-description) -
   assigned last week, due now. See also
   [Project Deliverable 2, continued](#project-deliverable-2-continued) below.

---

## 1. Setup

Work in **your fork** of `sierra-method` from last week. Start clean:

```bash
git pull            # if you synced your fork with upstream
git status          # should be clean before you start breaking things
```

Open the folder in VS Code and run every command from its **integrated terminal**.

### The commands you need this week

```bash
oml reason                 # fast check only: is the model consistent?
oml reason -e              # explain any inconsistency it finds
oml reason -u false        # turn OFF the unique names assumption
```


### How to work

Some experiments **deliberately break the model**. That is the point: you are learning to
read the reasoner's complaint. After each one, **undo your edit** before starting the next,
so you know which change caused which result. You can do this from VS Code's git tab. Or you can run this from terminal:

```bash
git diff                                    # confirm what you changed
git checkout -- src/                        # revert everything
```

Do not commit the broken states.

---

## Assignment 3: Reasoning Experiments

**Due before Module 4.** Run all eight experiments. For each one, submit:

- a **screenshot** of the command output (and of the relevant `.ttl` where asked), and
- a short **explanation in your own words**: what you changed, what the reasoner said, and
  *why* it said it.

The explanation is the graded part. A screenshot with no reasoning earns nothing.

Paths below are relative to the repo root. The Fire Force descriptions live under
`src/model/oml/fireforce6.github.io/mission-control/`, the Sierra vocabularies under
`src/method/oml/www.modelware.io/sierra/`.

### 1. Baseline: assertions vs entailments

```bash
oml reason
```

Open `build/owl/fireforce6.github.io/mission-control/system-analysis/`. Compare:

| File | Contains |
| --- | --- |
| `components.ttl` | What you **asserted** |
| `components__entailments.ttl` | What the reasoner **inferred** |

Pick one instance and account for its entailments. Where did each inferred type come from?
Why are the two kept in **separate graphs** rather than merged?

### 2. Break irreflexivity

In `base.oml`, add the `irreflexive` flag to the `Contains` relation entity. In
`components.oml`, make `FireSat` contain itself:

```
base:isContainedBy FireSat
```

Then:

```bash
oml reason -e
```

Explain what `irreflexive` asserts and why self-containment contradicts it. Read the
explanation output: which axioms does the reasoner name as the culprits?

### 3. Trigger the disjointness closure

In `connections.oml`, `ElectricalPower` is a `component:Energy`. Give it `component:Signal`
as a second type, then:

```bash
oml reason -e
```

You never wrote "Energy and Signal are disjoint" anywhere. Explain where the disjointness
came from. Now comment out `<https://www.modelware.io/sierra/component#>` from the
**vocabulary** bundle (`bundle.oml` in the Sierra folder) and re-run.

The inconsistency disappears. Explain why - this is the most important lesson of the week
about what a **bundle** actually does.

### 4. Domain inference gives you a type for free

`component:mass` has `domain PhysicalPart`. In `masses.oml`, add a mass assertion to a
component that is typed only `component:Component` - for example `components:Payload`:

```
ref instance components:Payload : component:Component [
    component:mass 120.0^^si:kg
]
```

Run `oml reason` and look for the **inferred** `PhysicalPart` type in the
entailments. Explain why a *domain* declaration produces a new type rather than an error.
Contrast this with how a typed programming language would treat the same situation.

### 5. Functional collision under the unique names assumption

`component:mass` is `functional`. Give `BatteryCell1` a second, different mass in
`masses.oml`, then:

```bash
oml reason -e
oml reason -u false
```

Explain both results. Why does the same model flip from inconsistent to consistent when you
disable the unique names assumption? What does the reasoner conclude in the second case
instead of complaining?

### 6. Multiple domains are conjunctive

In `base.oml`, change `priority` to:

```
domain Prioritized, Element
```

```bash
oml reason -e
```

Then change the domain to:

```
domain component:Port, component:Component
```

```bash
oml reason -e
```

One is harmless and the other is not. Explain why multiple domains mean **AND**, not **OR**,
and what that implies for the second case. (Check the `component.oml` vocabulary: what is the
relationship between `Port` and `Component`?)

### 7. From specialization to definition

In `stakeholder.oml`, add:

```
concept CriticalRequirement < Requirement [
    restricts base:priority to "High"
]
```

`R1` in `requirements.oml` has `base:priority "High"`, yet it is **not** classified as a
`CriticalRequirement`. Explain why. Then change `<` to `=` and re-run.

Confirm `R1` is now classified but `R2` still is not. This is the difference between a
**necessary** condition and a **necessary and sufficient** one - state it in your own words,
and say when you would use each in a real vocabulary.

### 8. Write the PowerDependency rule

Add to `component.oml`:

```
relation dependsOnPower [ from Component to Component ]

rule PowerDependency [
    Connection(p1,c,p2) &
    portOf(p1,src) & portOf(p2,tgt) &
    transfers(c,i) & Energy(i)
    -> dependsOnPower(tgt,src)
]
```

```bash
oml reason
```

Then inspect `connections__entailments.ttl` and find the derived `dependsOnPower` facts.
Explain each one: trace it back to the connection, ports, and item that produced it. Note the
**direction** of the derived relation and why the rule reverses it (`tgt` depends on `src`).

Then answer: why does this need a **rule** rather than a property chain or a restriction?

### Submitting

Submit a **report** (PDF or DOCX) on the **UofA learning platform** containing your eight
screenshots and explanations. Unlike Assignment 2, no repo URL or commit hash is needed -
these are throwaway experiments you revert.

**Graded on:** all eight attempted, correct explanations of *why*, and evidence you read the
reasoner's output rather than just capturing that it went red.

---

## Project Deliverable 2, continued

Project Deliverable 2 was assigned last week and is
[**due before Module 4**](Week2.md#project-deliverable-2-initial-vocabulary-and-description).
Now that you have seen this module, enrich it with what you learned:

| # | Add | Why |
| --- | --- | --- |
| 1 | **A vocabulary bundle and a description bundle** | Without them, disjointness and other closure axioms never fire - see Experiment 3 |
| 2 | **Property characteristics** where they hold | `functional`, `irreflexive`, `asymmetric`, `inverse functional` |
| 3 | **Disjointness** between sibling concepts that cannot overlap | Otherwise the reasoner assumes they can |
| 4 | **At least one defined concept** (`=` with a restriction) | Let the reasoner classify instead of you asserting - Experiment 7 |
| 5 | **A rule**, if your questions call for one | Derive facts instead of hand-maintaining them - Experiment 8 |
| 6 | **`oml reason` passes** | The graded bar. A model that reasons clean is the deliverable |

Keep your **Module 1 business questions** as the acceptance criteria. Add terms because a
question needs them, not because they exist in Sierra.

Also make sure your repo's **`README.md`** explains how to read your repo: what the system is,
where the vocabularies and descriptions live, and how to build it.

### Submitting

Push your work, then submit a document on the **UofA learning platform** with your **project
repo URL** and the **commit hash**.

---

## Further Reading


| Ref | Source | Why |
| --- | --- | --- |
| REF10 | [OWL 2 Primer: W3C](https://www.w3.org/TR/owl2-primer/) (advanced sections) | Return to the primer for existential and universal restrictions, equivalence, disjointness, property characteristics, and keys. The distinction between existential and universal restrictions, and their very different inference consequences, is worth the time |
| REF11 | [Defining N-ary Relations on the Semantic Web: W3C](https://www.w3.org/TR/swbp-n-aryRelations/) | Maps almost directly onto OML relation entities. Starts from the limitation that RDF/OWL properties are binary, then develops the patterns for relationships carrying extra participants or data |
| REF12 | [PROV-O: The PROV Ontology: W3C](https://www.w3.org/TR/prov-o/) | A real, standardized ontology to read end to end. Shows how concepts, properties, restrictions, and specialization assemble into an interoperable model - and connects to identity and provenance |
