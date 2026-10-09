# Week 6: AI Agents over OML

**Due before Module 7:**

1. [Assignment 6: Put an Agent to Work on Fire Force](#assignment-6-put-an-agent-to-work-on-fire-force)
2. [Project 4 (Week 6): Put an Agent to Work on Your Method](#project-4-week-6-put-an-agent-to-work-on-your-method)

> **Project numbering:** This is **Project 4**, it covers **Week 6 only**, and it is submitted
> this week. It is separate from Project 3, which covered Weeks 4-5.

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

### Step 3: Know where the agent configuration lives

Sierra already ships the OML MCP server config and the two skills. Use whichever AI agent
you have (Claude Code, Codex, Cursor, Gemini / Antigravity, or GitHub Copilot):

| File | Purpose |
| --- | --- |
| `.mcp.json` | OML MCP server config (Claude Code) |
| `.codex/config.toml` | OML MCP server config (Codex) |
| `.cursor/mcp.json` | OML MCP server config (Cursor) |
| `.agents/mcp_config.json` | OML MCP server config (other agents) |
| `.claude/skills/oml-mcp/SKILL.md` | **oml-mcp** skill: how to read, write, and verify through MCP tools |
| `.claude/skills/oml-text/SKILL.md` | **oml-text** skill: how to work with OML source text |

Every config launches the same server: `npx -y @oml/mcp`. If your agent does not pick up the
repo config, add that command to its MCP settings. You need **Node.js** installed.

### Step 4: Read the oml-mcp skill before you start

The skill tells the agent which tools to call and in what order. You will grade the agent
against it, so know it well:

| Workflow | Expected tool sequence |
| --- | --- |
| Retrieve | `oml_search` -> `oml_about` -> (`oml_paths` / `oml_members`) -> `oml_sparql` |
| Update | Retrieve context -> `oml_shapes` -> one atomic `oml_update` |
| Verify (gates) | `oml_validate` + `oml_reason` (both `only: true`, in one message) |

Also note the rules: **trust results, don't repeat**, **batch independent calls**, and
**never edit ontology files directly**.

### Keep your chat logs

Most deliverables this week ask for the **entire chat log that actually happened**. Export or
copy the full transcript (prompts, tool calls, tool results, and answers) before you close
the session. Do not edit or summarize it.

---

## Assignment 6: Put an Agent to Work on Fire Force

Work in your `sierra-method` fork.

| # | Task | What to do |
| --- | --- | --- |
| 1 | Connect the agent | Start the OML MCP server in your AI agent and make one successful `oml_workspace` call. Record the workspace roots returned. Take and send a screen capture. |
| 2 | Compare three ways of answering | Choose **3 questions from Assignment 5**. Answer each one: **(A)** unaided, **(B)** with the source-grounded AI agent, and **(C)** with your own query. Show your work. |
| 3 | Study the skill | Study the oml-mcp skill and load it into your AI agent. Send it **a couple of read prompts and three write prompts** (including one that should fail reasoning or validation). **Write your observations** on how well it followed the skill: tools called, their sequence, repetitions, etc. **Keep and send the entire chat log that actually happened.** |
| 4 | Review and commit | After the AI finishes writing, confirm the intended gates ran and passed. Inspect the repo `git diff`, then commit the reviewed change. |

### Tips

- **Task 2:** **(A)** means reading the model or the Fire Force description yourself, with
  no tools. **(B)** means asking the agent with the OML MCP enabled. **(C)** means running
  your own SPARQL query from Assignment 5. Compare the three answers: do they agree, and if
  not, which one is right and why?
- **Task 3:** For the write prompt that should fail, ask for a change that breaks a shape
  or makes the model inconsistent. Record whether the agent caught it with `oml_validate` or
  `oml_reason` and how it responded.
- **Task 4:** Do not commit changes you have not read. If the diff has anything you did not
  ask for, fix it or revert it first.

### Submitting

Push your work to your `sierra-method` fork, then submit a document on the **UofA learning
platform** with your **fork URL**, the **commit hash**, and a **report** containing the rest
of the asks (screen capture, answer comparison, observations, and chat logs).

---

## Project 4 (Week 6): Put an Agent to Work on Your Method

Run the same experiment three times, adding one capability each time, then improve the
skill based on what you see.

| # | Deliverable | Minimum |
| --- | --- | --- |
| 1 | AI chat script without MCP or skill | With no skills nor MCP at all, ask the AI agent 3 read questions and 3 write questions. Make them different ones. Save the chat log and send it. Screenshot the git changes and send it. |
| 2 | Enable the OML MCP in your agent | Ask the AI agent the same 3 read questions and 3 write questions. Save the chat log and send it. Screenshot the git changes and send it. |
| 3 | Add the oml-mcp and oml-text skills | Keep the OML MCP, and add both skills (copy them from Sierra). Ask the AI agent the same 3 read questions and 3 write questions. Save the chat log and send it. Screenshot the git changes and send it. |
| 4 | Modify the oml-mcp skill if needed | Analyze the chat log and compare it with the instructions in the skill. Adjust the skill to make the AI follow it more closely. Report on the result and send the chat log. |

### Tips

- **Same questions in rounds 1, 2, and 3.** The questions are the controlled variable; the
  capability is what changes. The 3 read questions must differ from each other, and so must
  the 3 write questions.
- **Start each round in a fresh chat session** so earlier rounds do not leak context.
- **Revert between rounds.** After you save the log and screenshot for rounds 1, 2, and 3,
  undo the agent's changes so every round starts from the same state:

  ```bash
  git restore .
  git clean -fd
  ```

  Check `git status` first so you do not discard your own work.
- **Round 1:** turn off MCP servers in your agent and make sure it cannot see the skills
  (e.g. temporarily move the skills folder out of the repo).
- **Round 4:** point to the specific log lines where the agent drifted from the skill, the
  skill change you made, and whether a rerun behaved better.

### Submitting

Revert the git changes after completing rounds 1, 2, and 3 (after taking screenshots and
logs). Commit only the result of round 4 (the adjusted skill) in your project repo. Then
submit a document on the **UofA learning platform** with your **project repo URL**, the
**commit hash**, and the chat logs, screenshots, and report.
