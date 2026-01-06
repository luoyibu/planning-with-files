---
name: planning-with-files
description: Transforms workflow to use Manus-style persistent markdown files for planning, progress tracking, and knowledge storage. Use when starting complex tasks, multi-step projects, research tasks, or when the user mentions planning, organizing work, tracking progress, or wants structured output.
---

# Planning with Files

Work like Manus: Use persistent markdown files as your "working memory on disk."

## Quick Start

Before ANY complex task:

1. **Create `task_plan.md`** in the working directory
2. **Define phases** with checkboxes
3. **Update after each phase** - mark [x] and change status
4. **Read before deciding** - refresh goals in attention window

## The 3-File Pattern

For every non-trivial task, create THREE files:

| File | Purpose | When to Update |
|------|---------|----------------|
| `task_plan.md` | Track phases and progress | After each phase |
| `notes.md` | Store findings and research | During research |
| `[deliverable].md` | Final output | At completion |

## Core Workflow

```
Loop 1: Create task_plan.md with goal and phases
Loop 2: Research → save to notes.md → update task_plan.md
Loop 3: Read notes.md → create deliverable → update task_plan.md
Loop 4: Deliver final output
```

### The Loop in Detail

**Before each major action:**
```bash
Read task_plan.md  # Refresh goals in attention window
```

**After each phase:**
```bash
Edit task_plan.md  # Mark [x], update status
```

**When storing information:**
```bash
Write notes.md     # Don't stuff context, store in file
Edit task_plan.md  # Add notes.md to Related Files if new, or update timestamp
```

**When creating new markdown files:**
```bash
Write new_file.md
Edit task_plan.md  # Add to Related Files section immediately
```

**When answering key questions:**
```bash
Edit task_plan.md  # Update Key Questions & Answers section with answer
```

## task_plan.md Template

Create this file FIRST for any complex task:

```markdown
# Task Plan: [Brief Description]

## Goal
[One sentence describing the end state]

## Phases
- [ ] Phase 1: Plan and setup
- [ ] Phase 2: Research/gather information
- [ ] Phase 3: Execute/build
- [ ] Phase 4: Review and deliver

## Key Questions & Answers

1. **[Question Summary]**
   - Status: ⏳ Pending / ✅ Answered / ❌ Dropped / 🔄 In Progress
   - Answer: (To be answered or recorded)
   - Evidence / Reference: (Link or citation)
   - Last Updated: (Date)

2. **[Question Summary]**
   - Status: ⏳ Pending
   - Answer: (To be answered)
   - Evidence / Reference: (To be added)
   - Last Updated: (To be added)

## Related Files

| File | Purpose | Last Updated | Status |
|------|---------|--------------|--------|
| notes.md | Research findings | (Pending) | Active |
| [deliverable].md | Final output | (Pending) | (Pending) |

**File Index Rules:**
- Add new files immediately after creation
- Update "Last Updated" when file is modified
- Status: Active / Reference / Complete / Archived

## Decisions Made
- [Decision]: [Rationale]

## Errors Encountered
- [Error]: [Resolution]

## Status
**Currently in Phase X** - [What I'm doing now]
```

## notes.md Template

For research and findings:

```markdown
# Notes: [Topic]

## Sources

### Source 1: [Name]
- URL: [link]
- Key points:
  - [Finding]
  - [Finding]

## Synthesized Findings

### [Category]
- [Finding]
- [Finding]
```

## Critical Rules

### 1. ALWAYS Create Plan First
Never start a complex task without `task_plan.md`. This is non-negotiable.

### 2. Read Before Decide
Before any major decision, read the plan file. This keeps goals in your attention window.

### 3. Update After Act
After completing any phase, immediately update the plan file:
- Mark completed phases with [x]
- Update the Status section
- Log any errors encountered

### 4. Store, Don't Stuff
Large outputs go to files, not context. Keep only paths in working memory.

### 5. Log All Errors
Every error goes in the "Errors Encountered" section. This builds knowledge for future tasks.

### 6. Update Checkpoints
After EVERY major action, check if task_plan.md needs updating:
- [ ] Did I complete a phase? → Update checkbox
- [ ] Did I answer a key question? → Update Key Questions & Answers section
- [ ] Did I create a new file? → Add to Related Files section
- [ ] Did I encounter an error? → Add to Errors Encountered section
- [ ] Did I make a decision? → Add to Decisions Made section

**Update Frequency:**
- After each phase completion: MANDATORY
- After every 5-10 tool calls: RECOMMENDED
- Before any major decision: MANDATORY (read + update)

## When to Use This Pattern

**Use 3-file pattern for:**
- Multi-step tasks (3+ steps)
- Research tasks
- Building/creating something
- Tasks spanning multiple tool calls
- Anything requiring organization

**Skip for:**
- Simple questions
- Single-file edits
- Quick lookups

## Anti-Patterns to Avoid

| Don't | Do Instead |
|-------|------------|
| Use TodoWrite for persistence | Create `task_plan.md` file |
| State goals once and forget | Re-read plan before each decision |
| Hide errors and retry | Log errors to plan file |
| Stuff everything in context | Store large content in files |
| Start executing immediately | Create plan file FIRST |

## Advanced Patterns

See [reference.md](reference.md) for:
- Attention manipulation techniques
- Error recovery patterns
- Context optimization from Manus

See [examples.md](examples.md) for:
- Real task examples
- Complex workflow patterns
