# /learn-issue $ARGUMENTS

Execute the LEARN workflow for issue #$ARGUMENTS with teaching and interactive coding.

**YOU are the orchestrator. Balance learning with progress.**

This command is designed for developers who want to:

- Understand the codebase context before making changes
- Learn Rails/Vue patterns through hands-on practice
- Build mental models of how code connects

---

## Workflow Overview

```
PHASE L:  Locate & Understand   → Show issue + visual diagram, wait for "proceed"
PHASE E:  Explore Context       → Guided tour + reading assignment
PHASE A:  Approach & Plan       → Teach pattern, show plan, wait for "proceed"
PHASE R:  Read & Write Together → Interactive coding with TODO(human)
PHASE N:  Navigate & Verify     → Test + summarize learnings
```

---

## PHASE L: Locate & Understand

**STOP** - This is a hard gate.

1. Fetch the issue:

   ```bash
   gh issue view $ARGUMENTS --json number,title,body,labels,milestone,assignees
   ```

2. **Plain language summary** (2-3 sentences max):
   - What's being asked in simple terms
   - Why it matters to the user/business

3. **Scope table** - Be explicit about boundaries:
   | ✅ Changing | ❌ NOT Changing |
   |------------|-----------------|
   | [files/areas affected] | [areas explicitly unchanged] |

4. **Visual flow diagram** (ASCII art showing the code path):

   ```
   [Entry point] → [Component A] → [Component B] → [Output]
                         ↑
                    (we're changing this)
   ```

   Mark where changes will happen with arrows/comments.

5. **STOP HERE** and wait for user to say "proceed"

**Do NOT continue until you receive explicit approval.**

---

## After Phase L Approval: Branch Setup

6. Create feature branch:

   ```bash
   git checkout main && git pull origin main
   git checkout -b feat/issue-$ARGUMENTS-{short-description}
   ```

7. Assign issue:

   ```bash
   gh issue edit $ARGUMENTS --add-assignee @me
   ```

8. Set the GitHub project status to "In progress":

   ```bash
   # Get the project item ID for this issue
   ITEM_ID=$(gh project item-list 6 --owner 101skills-gmbh --format json --limit 1000 | jq -r '.items[] | select(.content.number == $ARGUMENTS) | .id')

   # Update status to "In progress" (project: Fobizz Team)
   gh project item-edit --project-id PVT_kwDOBCLGOc4A1L6N --id $ITEM_ID --field-id PVTSSF_lADOBCLGOc4A1L6NzgqsncE --single-select-option-id 47fc9ee4
   ```

   **Note:** If the issue is not in the project yet, first add it:

   ```bash
   gh project item-add 6 --owner 101skills-gmbh --url https://github.com/101skills-gmbh/fobizz-rails/issues/$ARGUMENTS
   ```

---

## PHASE E: Explore Context

8. **Give reading assignment BEFORE launching researcher**:

   > 📚 **Reading Assignment** (while I research the codebase)
   >
   > Read `[relevant-file:line-range]` to understand [what pattern/concept]
   >
   > **Questions to ponder:**
   >
   > - How does [X component] receive data?
   > - What pattern is being used for [Y]?
   > - Where might we need to make changes?

9. Launch `researcher` agent to explore relevant code

10. When researcher returns, provide **guided tour**:

    > 🗺️ **Guided Tour of Relevant Code**
    >
    > **Entry point:** `[file]` - where the user action starts
    >
    > **Key files** (in order of data flow):
    >
    > 1. `[file1]` - [purpose, 1 sentence]
    > 2. `[file2]` - [purpose, 1 sentence]
    > 3. `[file3]` - [purpose, 1 sentence]
    >
    > **How they connect:**
    >
    > ```
    > [file1] --calls--> [file2] --queries--> [file3]
    >    ↓                   ↓
    > [returns X]      [returns Y]
    > ```

---

## PHASE A: Approach & Plan

**STOP** - This is a hard gate.

11. **Teach the pattern FIRST** (before showing any plan):

    > ★ Insight ─────────────────────────────────────
    > **[Rails/Vue Concept]: [Pattern Name]**
    >
    > [2-3 paragraphs explaining:]
    >
    > - What this pattern is
    > - When/why to use it
    > - Common pitfalls
    >
    > **In this codebase:** [How this project already uses this pattern, with file references]
    > ─────────────────────────────────────────────────

12. **Show existing examples** from the codebase:

    ```ruby
    # From app/contexts/example_context.rb:45
    # This is how we already do [similar thing]
    ```

13. Launch `planner` agent to create plan at `.claude/working/plans/issue-$ARGUMENTS.md`

14. Show plan with **micro-tasks**, marking interactive coding opportunities:

    ```
    □ Task 1: [description] - scaffolding
    □ Task 2: [description] ← 🎯 You'll write the key logic
    □ Task 3: [description] - scaffolding
    □ Task 4: [description] ← 🎯 You'll implement validation
    ```

    **Rule:** At least 1-2 tasks should have 🎯 markers for interactive coding.

15. **STOP HERE** and wait for explicit approval.

**Do NOT continue until you receive explicit approval.**

---

## PHASE R: Read & Write Together

For each micro-task:

### If task is scaffolding (no 🎯):

16. Implement directly, then briefly explain:
    > I've added [what] in `[file]`. This [does X] because [reason].

### If task has 🎯 (interactive coding):

17. Set up scaffolding with `TODO(human)` placeholder:

    ```ruby
    def process_region_code(zipcode, country_code)
      # TODO(human): Implement the lookup logic here
      # Consider: What happens if zipcode format is invalid?
      # Hint: Use RegionCode::Lookup with the country_code parameter
    end
    ```

18. Present the **Learn by Doing** request:

    > ● **Learn by Doing**
    >
    > **Context:** I've set up [what's ready]. The [component] needs [what functionality].
    >
    > **Your Task:** In `[file]`, implement `[function/section]`. Look for `TODO(human)`.
    >
    > **Guidance:**
    >
    > - Consider [trade-off 1]
    > - The existing pattern in [file] shows how to [similar thing]
    > - Think about edge case: [specific edge case]

19. **STOP** and wait for user to implement

20. When user completes, review and provide **ONE insight** connecting to broader patterns:

    > ★ Insight ─────────────────────────────────────
    > Nice! Your use of [pattern] here connects to [broader concept].
    > In Rails, this pattern is called [name] and you'll see it in [other places].
    > ─────────────────────────────────────────────────

### After each logical chunk:

21. Run validation:

    ```bash
    pnpm vue-tsc && pnpm lint
    ```

22. Show diff: `git diff`

23. Wait for commit approval, then commit:
    ```bash
    git add . && git commit -m "<type>(<scope>): <description>"
    ```

---

## PHASE N: Navigate & Verify

24. Run full validation:

    ```bash
    pnpm test && pnpm vue-tsc && pnpm lint
    ```

25. **Trace the flow together**:

    > 🔍 **Let's trace a request through your changes:**
    >
    > 1. User [does action]
    > 2. `[Controller]` receives request at line X
    > 3. Calls `[Service]` which you modified
    > 4. Your new code [does what]
    > 5. Returns [result] to user

26. **Summarize learnings**:

    > ★ Session Learnings ───────────────────────────
    >
    > **Concepts practiced:**
    >
    > - `[Rails: Pattern]` - [1 sentence what you learned]
    > - `[Vue: Pattern]` - [1 sentence what you learned]
    >
    > **Codebase knowledge gained:**
    >
    > - `[file/pattern]` - now you know [what it does]
    > - `[file/pattern]` - now you know [how it connects]
    >
    > **Files to remember:**
    >
    > - `[file]` - [why it's important]
    >   ─────────────────────────────────────────────────

27. **Log learnings to Notion** (automatic):
    - Always save session learnings to the Notion database (see Notion Integration below)
    - Inform user: "I've saved these learnings to your Notion database."

28. If Notion logging fails, show error and offer manual copy of learnings

29. Continue to PR review (spawn pr-review-toolkit agents as in `/work-on-issue`)

30. Create PR after approval

---

## Pace Control Commands

User can say at any time:

| Command                  | Claude does                              |
| ------------------------ | ---------------------------------------- |
| "slow down"              | Pause, explain current context in detail |
| "show me the flow"       | Draw ASCII diagram of current code path  |
| "give me reading"        | Assign relevant code to read             |
| "teach me X first"       | Explain concept before continuing        |
| "let me write this part" | Set up TODO(human) for current task      |
| "skip the teaching"      | Switch to faster `/work-on-issue` style  |
| "log this learning"      | Save current insight to Notion           |

---

## Notion Integration

**Learnings are automatically logged to Notion after every session.**

**Database URL:** https://www.notion.so/iratxe-garrido/230184fb518d80478baee27a5d72b3da

When Phase N completes:

1. Use Notion MCP to create a new page in the learnings database
2. Page structure:
   - **Title**: `Issue #$ARGUMENTS - [short description]`
   - **Date**: Today's date
   - **Content**:
     - Issue summary (what was the problem/feature)
     - Concepts learned (Rails patterns, Vue patterns)
     - Key files discovered (with brief description of each)
     - Code patterns to remember (with examples if helpful)
     - Any gotchas or edge cases encountered

3. If Notion MCP is not configured or fails:
   - Show error message
   - Display formatted learnings so user can manually copy
   - Offer to retry or show setup instructions

---

## Concept Tags

Throughout the workflow, tag insights by area:

- `[Rails: ActiveRecord]` - Model patterns, validations, associations
- `[Rails: Controllers]` - Request handling, params, responses
- `[Rails: Services]` - Service objects, contexts, business logic
- `[Rails: Jobs]` - Background processing, Sidekiq
- `[Vue: Composition]` - Composables, setup, reactivity
- `[Vue: Components]` - Props, events, slots
- `[Vue: State]` - Pinia, refs, computed

---

## Critical Rules

1. **Teach before code** - Always explain the pattern before writing it
2. **Interactive coding** - At least 1-2 tasks should have 🎯 for user to write
3. **Visual diagrams** - Use ASCII art to show code flow
4. **Reading assignments** - Give user code to read while researching
5. **One concept at a time** - Don't overwhelm with multiple patterns
6. **Connect to broader patterns** - Relate specific code to general concepts

---

## Error Recovery

### User seems lost

```
It sounds like you might need more context. Let me:
1. Show you the visual flow diagram again
2. Explain [specific concept]
3. Point you to similar code in the codebase

Which would help most?
```

### User wants to go faster

```
Got it! I'll switch to the faster /work-on-issue style.
You can say "teach me" anytime to slow down again.
```
