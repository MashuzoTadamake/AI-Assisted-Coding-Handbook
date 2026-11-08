# AI-Assisted-Coding-Handbook
My personal reference for coding with AI: prompts, workflows, tools, and things to check. Researched with AI assistance and linked to original sources. I’ll update it as I use it.

**Build with AI. Understand the important parts. Check that they work.**

Version **1.0**

Start with sections 1 and 6. Use the others when you need them. You do not need to install skills, connect MCP servers, or set up automation before writing code.

Templates are suggested starting points. Tool details come from the linked documentation. Integrations still need checking in your own setup.

## Contents

1. [The everyday workflow](#1-the-everyday-workflow)
2. [Tools: start small](#2-tools-start-small)
3. [Plan the next useful thing](#3-plan-the-next-useful-thing)
4. [AGENTS.md: project instructions](#4-agentsmd-project-instructions)
5. [Give AI the right context](#5-give-ai-the-right-context)
6. [How to write a good prompt](#6-how-to-write-a-good-prompt)
7. [Understand the generated code](#7-understand-the-generated-code)
8. [Useful skills by situation](#8-useful-skills-by-situation)
9. [MCP: connect an outside tool](#9-mcp-connect-an-outside-tool)
10. [Hooks and automation](#10-hooks-and-automation)
11. [Use fewer tokens and measure cost](#11-use-fewer-tokens-and-measure-cost)
12. [Test and debug](#12-test-and-debug)
13. [Git and working with others](#13-git-and-working-with-others)
14. [Security: the essentials](#14-security-the-essentials)
15. [Deploy and recover](#15-deploy-and-recover)
16. [Quick lookup](#16-quick-lookup)

## 1. The everyday workflow

1. **Say what should happen.** Describe one feature or bug.
2. **Let AI inspect the existing code.** It should find the relevant files first.
3. **Make one useful change.** Keep unrelated cleanup separate.
4. **Run checks.** Ask AI to test its work; try the main action yourself too.
5. **Review and save.** Read the changes, ask about anything unclear, then commit with Git.

For a small edit, one message may be enough. For a large feature, agree on a short plan first.

**Done means:** it works, the important checks pass, you understand the important behavior, and unfinished parts are stated clearly.

## 2. Tools: start small

You need:

- An editor, such as VS Code.
- One coding assistant, such as Codex, Claude Code, Copilot or Antigravity.
- Git to save and review changes.
- The project's usual way to run and test the app.

For websites, also use the browser's developer tools to inspect errors and failed requests.

### Opening an existing project

Paste this:

```text
Inspect this project without changing files.
Tell me:
1. How to install dependencies and run the app.
2. How to run tests, code checks, and the build.
3. Which files handle [the feature I care about].
4. Whether any existing checks already fail.

Use the project's existing tools. Do not print secret values.
```

Save the commands that actually work. Ask AI to explain a command before you run it if you do not understand its effects.

## 3. Plan the next useful thing

For a new app, answer these questions:

```markdown
# Project brief
Who is it for?
What problem does it solve?
What is the first useful action a user should complete?
What must work in the first version?
What can wait?
What data will it store, and who can access it?
Where will it run?
```

Then ask:

```text
Use this brief to suggest a simple approach.
Explain the main parts and how data moves between them.
Give me a short plan where each step produces something I can check.
Start with one working user journey. Leave extra features for later.
Point out decisions I need to make before implementation.
```

For example, “add expense tracking” can start with **enter an expense → save it → see it in a list**. Charts and exports can come afterward.

For a small bug, skip the project brief. Give the reproduction and expected result instead.

## 4. AGENTS.md: project instructions

`AGENTS.md` is a text file containing instructions the coding assistant should reuse in that project. Put it at the repository root for tools that support it.

Keep it short. Include working commands and unusual project rules. Put the current task in your message.

### Copyable template

Replace the bracketed parts and delete anything you do not need:

```markdown
# Project instructions

## Commands
- Run app: [command]
- Run tests: [command]
- Check code: [command]
- Build: [command]

## Important project rules
- [Compatibility or data rule that is easy to miss.]
- [Generated files to avoid editing directly, if any.]

## When making changes
- Inspect the relevant code first.
- Preserve unrelated local changes.
- Use existing libraries and patterns unless a change is needed.
- Test the changed behavior and report what passed or failed.
- Explain important changes in plain language.
```

Codex supports layered `AGENTS.md` instructions, including overrides. Claude Code's current releases also support `AGENTS.md`, but loading depends on its settings and whether `CLAUDE.md` exists. Check what your assistant actually loaded. [Codex instructions](https://learn.chatgpt.com/docs/agent-configuration/agents-md), [Claude instructions](https://code.claude.com/docs/en/memory)

You do not need a large “memory bank.” Add a separate document only when you repeatedly need that information.

## 5. Give AI the right context

**Context means the information AI uses to do the task.**

| Task | Useful information |
|---|---|
| Fix a bug | Steps to reproduce, exact error, expected result |
| Change a page | Screenshot, page URL/path, expected appearance |
| Add a feature | User action, rules, examples of success and failure |
| Use a library | Installed version and official documentation |
| Continue old work | Goal, changes made, checks run, next step |

Give relevant files or let the assistant find them. Start with the affected area; expand if necessary.

### When a conversation becomes confused

Ask it to stop editing and summarize the facts. Start a new conversation if it keeps repeating mistakes or mixing unrelated tasks.

Copy this handoff into the new conversation:

```text
Goal:
Project and branch:
What changed:
What works:
What still fails:
Checks already run:
Relevant files:
Next step:

Verify the current files and Git state before continuing.
```

There is no need to restart after every small change. Keep going while the conversation is useful.

## 6. How to write a good prompt

A useful prompt answers **what you want, what the situation is, what limits matter, and how to tell whether it worked**.

Paste it as a normal message. No special command is needed. You can save the template for reuse.

### The main template

```text
GOAL
[What should the user be able to do?]

CONTEXT
[Current behavior, relevant page/files, screenshot, or exact error.]

RULES
[Things to preserve, tools to use, or changes to avoid.]

DONE WHEN
[Specific examples that must work, including a failure case if relevant.]

HOW TO WORK
Inspect the relevant code first. Make a focused change and run useful checks.
Ask me if a missing decision would change the result significantly.

FINISH WITH
What changed, how you checked it, and anything still uncertain.
Explain unfamiliar parts briefly.
```

You can shorten this for easy tasks. The headings help organize information; they are optional.

### Filled-in example

```text
GOAL
Let users download their own expenses as a CSV file.

CONTEXT
The dashboard already lists expenses. Find its data-loading code and reuse
our existing ownership rules.

RULES
Keep the current layout. Do not add a new dependency unless needed.
Do not change unrelated features.

DONE WHEN
- A signed-in user can download their own expenses.
- They cannot download another user's expenses.
- A user with no expenses gets a valid file with column headings.
- Values containing commas or quotes export correctly.

HOW TO WORK
Inspect the existing flow, implement the feature, and run relevant checks.

FINISH WITH
Explain the changed files, permission check, and test results.
```

### Three shorter prompts

**Fix a bug:**

```text
When I [steps], [actual result] happens.
It should [expected result]. Error: [exact error].
Find the cause, make a focused fix, and check that the bug is gone.
Explain the cause and checks. Preserve unrelated changes.
```

**Review code:**

```text
Review these changes against [requirement]. Do not edit yet.
Find real bugs, permission problems, data-loss risks, and missing checks.
For each finding, show the file, how it can fail, and how to verify it.
Separate confirmed problems from guesses.
```

**Improve a frontend:**

```text
Improve [page/component] for [audience and purpose].
Follow [existing design or reference]. Keep [required behavior].
Check mobile layout, keyboard use, loading, empty, and error states.
Show the result and explain the main changes.
```

### What to leave out

- Long role descriptions such as “you are the world's best engineer.”
- Repeated demands to “think harder” without new information.
- Several unrelated tasks in one request.
- Rules that do not matter to the task.

If the result is wrong, add the missing fact or a failing example. Repeating “fix it properly” gives the assistant little new information.

## 7. Understand the generated code

You do not need to memorize every line. You should understand:

- Where the action starts.
- Where data is read or saved.
- Where access is checked.
- What happens when something fails.
- Which test or manual check proves it works.

Paste this after a meaningful change:

```text
Explain this change using the actual files and function names.
Trace one successful example and one failure.
Show where data changes and permissions are checked.
Explain unfamiliar syntax only where it affects behavior.
Do not make more changes.
```

Then predict an edge case and try it. For search, ask: “What happens if an older request finishes after a newer one?” For a payment, ask: “What if the same request arrives twice?”

If the explanation is too complicated for the feature, ask whether the implementation can be simplified.

## 8. Useful skills by situation

**A skill is a saved set of instructions, sometimes with scripts, for a particular kind of task.** It guides the assistant; it does not guarantee correctness or provide credentials by itself.

Normal coding usually works without extra skills. Add one when you need its specific help.

### A small shortlist

The links below point to the publishers' own files. These are useful options, not a ranking or a requirement to install all of them.

| Situation | Skill | When to use it | Skip it when… |
|---|---|---|---|
| Development: failing GitHub checks | OpenAI: [gh-fix-ci](https://github.com/openai/skills/blob/main/skills/.curated/gh-fix-ci/SKILL.md) | A PR's GitHub Actions checks fail and you need the logs investigated | You are fixing a local bug or using another CI provider |
| Frontend: visual design | Anthropic: [frontend-design](https://github.com/anthropics/skills/blob/main/skills/frontend-design/SKILL.md) | Creating a page or making a deliberate visual redesign | You only need a small fix in an established design |
| Frontend: React performance | Vercel: [vercel-react-best-practices](https://github.com/vercel-labs/agent-skills/blob/main/skills/react-best-practices/SKILL.md) | React/Next.js components, data fetching, or performance work | Your project uses a different framework |
| Browser: check real interactions | OpenAI: [playwright](https://github.com/openai/skills/blob/main/skills/.curated/playwright/SKILL.md) | Clicking through forms, reproducing UI bugs, taking screenshots | A simple code-level check is enough |
| Security review | OpenAI: [security-best-practices](https://github.com/openai/skills/blob/main/skills/.curated/security-best-practices/SKILL.md) | Reviewing Python, JavaScript/TypeScript, or Go security practices | Your language is unsupported or you only need ordinary debugging |
| Database | Supabase: [supabase-postgres-best-practices](https://github.com/supabase/agent-skills/blob/main/skills/supabase-postgres-best-practices/SKILL.md) | PostgreSQL schema, queries, indexes, migrations, or row access rules | You use a different database |

Useful details: `gh-fix-ci` needs authenticated GitHub CLI access and includes a review-before-fix step. The Playwright skill drives a browser; it is not automatically a permanent regression test suite. The PostgreSQL skill also applies to Postgres hosted outside Supabase.

### How to install

For Codex's curated skills, ask its installer in chat:

```text
$skill-installer gh-fix-ci
```

For a skill from another repository, provide its folder URL:

```text
$skill-installer install https://github.com/anthropics/skills/tree/main/skills/frontend-design
```

These are instructions to enter in Codex chat, not terminal commands. Installation depends on the client; consult its skill setup instructions. If the skill does not appear, reload or restart and check again. [Codex skill setup](https://learn.chatgpt.com/docs/build-skills), [Claude skill setup](https://code.claude.com/docs/en/skills)

Before installing, read the skill and check any scripts or dependencies. Install the full skill folder, not just its `SKILL.md`, when it has supporting files.

### How to use one

After installation, name the skill and give it a normal task:

```text
Use frontend-design to improve this landing page.
It is for [audience], and its main purpose is [goal].
Follow this reference: [image/link]. Keep the current signup behavior.
Check the result on mobile too.
```

```text
Use supabase-postgres-best-practices to review this slow query.
Explain the likely cause and how to measure any improvement.
Propose changes first. Do not modify the production database.
```

Use one relevant skill first. Add another only if it covers a separate need.

## 9. MCP: connect an outside tool

**MCP lets an assistant use tools or data from another system**, such as documentation, GitHub, or a database. [MCP introduction](https://modelcontextprotocol.io/docs/2026-07-28/getting-started/intro)

| Need | Use |
|---|---|
| Tell AI how to do a repeated task | A skill |
| Give AI access to an outside system | An integration, sometimes through MCP |
| Run an exact local check | A script or normal command |

If existing tools already do the job, you can skip MCP.

### A first connection: official OpenAI documentation

If you have Codex CLI installed, run these in a terminal:

```bash
codex mcp add openaiDeveloperDocs --url https://developers.openai.com/mcp
codex mcp list
```

Then ask:

```text
Use the OpenAI documentation MCP server to find the current instructions
for [topic]. Open the relevant page and give me its official link.
```

Confirm the assistant actually used the server. Seeing it in the list only proves configuration exists. [Official setup](https://developers.openai.com/learn/docs-mcp)

Other clients have their own setup. Claude Code uses `claude mcp add`; VS Code provides **MCP: Add Server** and requires choosing the intended agent setup. [Claude MCP](https://code.claude.com/docs/en/mcp), [VS Code MCP](https://code.visualstudio.com/docs/agent-customization/mcp-servers)

### If it does not work

Check, in order: correct client/configuration → server enabled → runtime/network → login and permissions → actual tool call.

Start with read-only access. Review the publisher, executable or endpoint, and requested permissions. A local server can run code; an outside service may receive your data. [MCP security](https://modelcontextprotocol.io/docs/2026-07-28/tutorials/security/security_best_practices)

## 10. Hooks and automation

**A hook runs something at an assistant event**, such as starting a session or finishing an edit. **CI runs shared checks**, usually when code is pushed or a pull request changes.

You can skip hooks until a repeated problem makes them useful.

| Repeated need | Start with |
|---|---|
| Format code | The project's formatter |
| Check changes before sharing | A test/check command, then CI |
| Repeat a judgment-based procedure | A skill |
| Run something at an assistant event | A hook |
| Produce a scheduled report | A scheduled automation |

### Setup prompt

```text
I repeatedly need [task].
Use the simplest existing tool to automate it.
Make the normal command work first. Then connect the trigger if needed.
Explain when it runs, what it can change, how failures appear,
and how I can disable it. Test one harmless run.
```

Current Codex and Claude Code support hooks, but configuration and event behavior differ. Copy examples from your client's documentation. [Codex hooks](https://learn.chatgpt.com/docs/hooks), [Claude hooks](https://code.claude.com/docs/en/hooks)

For unattended work, set a time/spend limit, limit retries, and make failures visible. Start with reports or draft changes. Keep production releases under your normal review process.

For CI, use limited credentials and review workflow changes. Do not expose deployment secrets to untrusted pull-request code. [GitHub Actions security](https://docs.github.com/en/actions/reference/security/secure-use)

## 11. Use fewer tokens and measure cost

**Tokens are the units used to process text and other model inputs/outputs.** The assistant may process instructions, previous messages, code, tool results, and reasoning—not just your latest message.

### Useful habits

1. Give one clear task and examples of what should work.
2. Let AI find relevant files instead of pasting the whole repository.
3. Save working commands in `AGENTS.md`.
4. Provide the important error and enough surrounding information; keep full logs available.
5. Ask for a short final explanation.
6. Stop repeated failed attempts and collect new evidence.
7. Use extra skills, tools, or agents when they solve a real need.
8. Compare models on your own tasks, including review and repairs.

The cheapest successful workflow matters more than the shortest prompt.

### A small experiment

Try the same task from the same starting code with two approaches. Change one thing: prompt, model, skill, or amount of context. Repeat if practical.

```markdown
Task and starting commit:
Approach:
Client/model/settings:
Total attempts:
Reported usage:
Measured or estimated cost:
Time spent, including my review:
Did it meet the same requirements?
What needed fixing?
```

Count failed attempts too:

```text
Cost per accepted task = cost of all attempts / number of accepted tasks
```

If no task succeeds, report total cost and failures instead.

### Caching and billing

Caching can reuse earlier input at different prices. It does not mean that input disappears from context. Cache rules and prices depend on the provider/model. [OpenAI caching](https://developers.openai.com/api/docs/guides/prompt-caching), [Anthropic caching](https://platform.claude.com/docs/en/build-with-claude/prompt-caching)

Use the tool's actual usage report and bill. Do not add overlapping totals twice. Keep ordinary input, cached reads, cache writes, and output separate when pricing requires it.

A subscription's displayed API-price estimate is not necessarily your bill. Claude Code documents usage reporting through `/usage`; check the command supported by your installed version. [Claude usage](https://code.claude.com/docs/en/costs)

For automated Codex experiments, `codex exec --json` provides machine-readable events, including usage. This is optional; begin with the usage information your normal client exposes. [Codex reporting](https://learn.chatgpt.com/docs/non-interactive-mode)

## 12. Test and debug

### What to check

| Change | Minimum useful check |
|---|---|
| Text or spacing | Review the change and appearance |
| Calculation | Known answer and boundary cases |
| Form | Valid, invalid, empty, and failure states |
| Protected data | Owner, another user, and signed-out caller |
| API integration | Success, error, timeout, and invalid response |
| Database change | Test database, representative data, recovery plan |
| Deployment | Build, startup, and a real user action |

Use existing tests. Ask AI to explain which mistake each new test would catch. For a bug, a useful regression test fails with the broken behavior and passes with the repair.

For browser tests, prefer user-visible behavior and stable controls such as accessible button names. [Playwright guidance](https://playwright.dev/docs/best-practices)

### Debugging prompt

```text
Expected: [result]
Actual: [result]
Steps: [reproduction]
Error: [sanitized error]
Environment: [relevant versions/browser]
Already tried: [action and result]

Reproduce the issue if possible. Use evidence to identify the cause.
Make a focused repair and check the original failure again.
If an approach keeps failing without new evidence, stop editing and
explain what information is missing.
```

If AI claims it tested something, look for the command and result. “Should work” means it still needs checking.

## 13. Git and working with others

**Git saves project history. A commit is a saved checkpoint. A diff shows changes.**

Before AI edits, inspect existing work:

```bash
git status --short
git diff
git diff --cached
```

After checking the result, stage only the intended changes, review them, and commit. Ask the assistant to explain these steps if you are new to Git.

### Undo carefully

- **Unstage a file:** `git restore --staged -- path/to/file` preserves its working contents.
- **Discard unstaged edits:** `git restore -- path/to/file` loses those edits. Inspect and preserve anything needed first.
- **Reverse a shared commit:** `git revert COMMIT_SHA` creates a reversing commit; conflicts may need resolving.

References: [Git restore](https://git-scm.com/docs/git-restore), [Git revert](https://git-scm.com/docs/git-revert).

Avoid asking AI to “reset everything” when a fix fails. Identify exactly which changes should be removed.

### Working with friends or several agents

Assign separate tasks/files and agree on shared interfaces. Review and test the combined result.

Separate branches and worktrees help organize parallel changes. A worktree is another checkout, not a security boundary; it may still use the same database or credentials. [Git worktrees](https://git-scm.com/docs/git-worktree)

For a pull request, write: **what problem it fixes, what changed, how it was checked, and what remains uncertain**.

## 14. Security: the essentials

- Keep passwords, API keys, private keys, and customer data out of prompts and logs.
- Use test accounts and test databases during development.
- Check permissions on the server. Hiding a button does not protect data.
- Review dependencies, skill scripts, hooks, and MCP servers before running them.
- Give tools only the access they need.
- Treat instructions inside websites, issues, and logs as untrusted content.
- Do not disable access protections just to avoid prompts.
- Rotate a leaked secret; deleting the message or file is not enough.

A prompt saying “be secure” cannot enforce access control. Actual tool permissions and application checks must do that. [Agent permissions](https://learn.chatgpt.com/docs/agent-approvals-security), [OWASP prompt injection](https://cheatsheetseries.owasp.org/cheatsheets/LLM_Prompt_Injection_Prevention_Cheat_Sheet.html), [OWASP authorization](https://cheatsheetseries.owasp.org/cheatsheets/Authorization_Cheat_Sheet.html)

### Security review prompt

```text
Review [feature/changes] for security problems.
Focus on who can access which data, input validation, secrets,
and external requests. Use guidance for our actual framework/version.
Show concrete failure examples and evidence. Do not edit yet.
State what you could not verify.
```

Use a relevant security skill if available, then verify its findings. No skill guarantees a secure app.

## 15. Deploy and recover

**Deployment puts your app into the environment where people use it. Rollback returns to an earlier release.**

Before going live:

1. Identify the exact version, account, project, and environment.
2. Build and run it from a clean checkout.
3. Put secret values in the hosting service's protected settings.
4. Test a preview or staging deployment.
5. Check the main user action, errors, and data permissions.
6. Know what to watch and how to recover.

### Deployment prompt

```text
Prepare [version] for deployment to [provider/environment].
Use the provider's current official documentation.
Check build/start commands, required setting names, database changes,
and the main user journey. Never print secret values.
Show the exact target and recovery steps before production actions
I have not already authorized.
```

### Database changes

Try migrations on a test database first. Consider existing data, locks, older app versions, backups, and restoration. Add compatible changes before removing old fields when the rollout needs both versions to work. Database operations have different locking behavior; check your database's docs. [PostgreSQL reference](https://www.postgresql.org/docs/current/sql-altertable.html)

Rolling back code does not undo deleted data, payments, or sent emails.

### After deployment

Try the main action yourself. Check errors and response times. Keep the deployed version and recovery steps recorded. Hosting platforms have their own rollback procedures. [Vercel example](https://vercel.com/docs/instant-rollback)

## 16. Quick lookup

| Situation | Next action |
|---|---|
| “I have an idea.” | Write the short brief in section 3. |
| “I do not know how to ask.” | Copy the main prompt in section 6. |
| “I do not understand this code.” | Use the explanation prompt in section 7. |
| “The page looks bad.” | Give a reference and goal; consider frontend-design. |
| “The browser flow breaks.” | Reproduce it; consider the Playwright skill. |
| “GitHub checks fail.” | Read the logs; consider gh-fix-ci. |
| “A Postgres query is slow.” | Measure it; consider the Postgres skill. |
| “I need a security review.” | Use section 14 and a matching skill. |
| “AI keeps making random changes.” | Pause edits; restate the task and inspect the diff. |
| “I am using too many tokens.” | Compare complete attempts using section 11. |
| “I need an outside service.” | Check existing integrations, then MCP if needed. |
| “I repeat this task every week.” | Make the command work, then automate it. |
| “I want to go live.” | Follow section 15, including recovery. |

## Sources and updates

Links appear beside the advice they support. The guide was initially inspired by [EnzeD/vibe-coding](https://github.com/EnzeD/vibe-coding).

The prompt templates and workflow choices are practical suggestions. The skills above were checked against their publishers' repositories, but were not installed or benchmarked for this guide. Configuration, access, and supported features can change—check the linked instructions when setting something up.

---
Updated: 09-11-2026
