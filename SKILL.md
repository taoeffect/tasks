---
name: tasks
description: "Whenever the user says something like: 'Begin task: <task name>' - always read this skill to understand how to proceed."
---

# Tasks

Tasks are stored in the project root under a folder: `.agents/tasks/<task name>/`

If this folder doesn't exist, create it.

`<task name>` refers to a task name specified by the user. For example, if the user says, `Begin task: update-pull-request-workflow`, `<task name>` would be `update-pull-request-workflow`.

Inside of this `<task name>` folder are the following possible files:

- `CURRENT_DESIGN.md` - detailed description of all relevant parts of the codebase for the task we're working on. It contains all files and important functions and/or selectors for the task description given to us. If this file doesn't exist, then your first task is to create it. If this file already does exist, you do not need to read it unless it is relevant to the current TODO that you're working on. Always make sure you've read it when working on the very first TODO.
- `PLAN.md` - detailed plan of the changes to be made. This file contains a detailed high-level plan of the changes that need to be made to the codebase in order to accomplish the task. It is based on the understanding of the codebase as established by the project `AGENTS.md` file (if it exists) and the `CURRENT_DESIGN.md` file. If this file doesn't exist, you are to create it based on the task description that the user gave you, and then afterward create `TODOs.md` based on this plan (see next bullet point). If this file already does exist, read it if you haven't already done so.
- `TODOs.md` - a non-hierarchical numbered outline of sequential steps to be taken. Each of the todos in this file correspond to a `STEP-<N>.md` file, and those files are where any sub-steps go. Always read this file before starting any task so that you know what specific step to work on next. Todos should be short high-level descriptions of the task to be completed (details go in STEP files). Example:

    ```md
    1. [x] Analyze all existing code related to creating users
    2. [ ] Refactor the codebase so that all users join the `#general` channel when they're created
    ```

- `STEP-<N>.md` - these files correspond to the numbered step from `TODOs.md`. They contain the current step status, a series of numbered sub-steps, and a final section of notes related to where we are in completing this step. You can use this file as a memory bank for important information for picking up where you left off in case the session ends, the context clears, and you need to pick up from where you left off in a new context. Here's an example of what this file could look like:

    ```md .agents/tasks/update-pull-request-workflow/STEP-1.md
    # Analyze all existing code related to creating users
    
    Status: COMPLETED
    
    ## Sub tasks
    
    1. [x] review all user-creation code in `backend/`
    2. [x] review all user-creation code in `frontend/`
    
    ## NOTES
    
    I've completed my investigation and found the following functions as relevant for creating users, starting with the backend:
    
    ### Backend user creation code
    
    - `createUser()` - `backend/userManagement.ts:78-190`
    - ...
    ```

- `KNOWLEDGE.md` - if this file exists, always read it before beginning on any TODOs. It contains project gotchas and dos & donts. Create/update this file as necessary with any useful feedback the user provides, and any discoveries you've made about the codebase as you're working (this is to save you from having to perform the same troubleshooting steps next time). Keep it concise and not too long. **Important:** this file is guaranteed to be read at the start of every session, so any discovery that a *future* TODO will need — a gotcha, constraint, or surprising behavior relevant to a later step — must be recorded here, not only in the current step's NOTES. Prefix such entries with the TODO they matter for (e.g. `For the TODO-3 test: ...`).


## NOTES

Work in segments of one TODO at a time unless instructed otherwise. When you've completed a TODO, give the user a very brief report on what you've done. Leave any further details in the `NOTES` section of the corresponding `STEP-<N>.md` file. If any of those details are discoveries a future TODO depends on, record them in `KNOWLEDGE.md` as well (see above), since old step files might not be read again.

To avoid using up unnecessary context, do not read previous step files (unless you need to in order to complete the current step you're working on).

Your progress should be tracked in the particular `STEP-<N>.md` file you're working on. However, if it's pertinent to completing the current step you're working on, you may update any of the other files in the tasks folder.

If, while working on a subtask, you discover that `PLAN.md` or `TODOs.md` must be adjusted, update them immediately before proceeding with implementation. When adding a TODO to an existing task, follow the rules in "Adding TODOs to an existing task" section instead.

- Important: never mark a TODO in TODOs.md as completed unless you've first created a corresponding `STEP-<N>.md` file for it, done the step, updated its notes section, and marked its status as `COMPLETED`.
- Important: as you are working, always update the current `STEP-<N>.md` file to mark subtasks within it as completed immediately after you complete them. No exceptions! Do not start the next subtask until you've done this! This is to ensure interrupted sessions can smoothly pick up from where you left off.
- Important: if your agent environment has a `todos` type tool (a UI checklist), NEVER USE IT when using this skill!
- Important: When creating files for a task, NEVER create multiple `STEP-<N>.md` files in a row! Except when adding a TODO to an existing task, ALWAYS ONLY create the STEP file for the current TODO that you're working on, and only after you've reviewed any relevant files and are ready to begin work.

After you've completed a step and marked its TODO in the `TODOs.md` as done, stop and let the user review your changes (unless otherwise instructed to continue without stopping).

## Adding TODOs to an existing task

Use this procedure when the user asks you to add a TODO to an existing task whose `CURRENT_DESIGN.md`, `PLAN.md`, and `TODOs.md` files already exist. Existing STEP files are not required for TODOs that have not yet been started.

This procedure is for follow-up work that can be completed after the existing TODOs.

- Read `TODOs.md`, `PLAN.md`, `KNOWLEDGE.md`, and any other task files relevant to the requested addition.
- Add a short, high-level TODO to the end of `TODOs.md`.
- DO NOT modify `PLAN.md` unless the new TODO contradicts the existing plan. If it does, add a **brief** addendum explaining the change in direction to the end of `PLAN.md` referencing this TODO number. This rule overrides the general PLAN update rule above.
- Create the corresponding `STEP-<N>.md` file and put all implementation details there. Set its status to `NOT STARTED`.
  - Creating this STEP file is an explicit exception to the rule against creating STEP files before their TODO becomes current.
- The earliest incomplete TODO remains the current TODO.

Then stop without implementing the new TODO unless instructed otherwise.

## Pausing for user intervention

If you hit a blocker that only the user can resolve — for example a command that needs privileges you lack (sudo / dnf / apt installs), a missing credential, or a resource you cannot obtain yourself — do NOT hack around it, do NOT keep retrying, and do NOT silently skip the affected work. Instead:

1. Add a section to the current `STEP-<N>.md` headed exactly `## PAUSED: user intervention required`, stating what is blocked, exactly what you need the user to do (including the precise commands to run), and any alternatives or workarounds you can suggest.
2. Stop immediately: do not mark the sub-task or TODO as done, and report the blocker and your suggestions as your final output.

When you get a response — whether the user answers right away or in a later session (check the current step's file for an unresolved `## PAUSED:` section when continuing a task) — act on it yourself, record the response and how the blocker was resolved under that section, change its heading to `## RESOLVED: user intervention`, and continue the step from where it left off. If the response leaves you still blocked, pause again the same way, stating what is still missing.

## Review Only Instruction

If the user tells you either "review only", "verify only" or "verify plan" for the task (or something to that effect), this indicates that they don't want you to actually complete any TODOs. Instead, for this session just review the existing CURRENT_DESIGN.md, PLAN.md, TODOs.md (and any relevant STEP file), compare it to the codebase, and verify whether the current plan is sound or if any adjustments should be made. If adjustments should be made, update the documents accordingly and then stop after giving the user a brief summary of the changes.

## Managing limited context

There are two useful tools an agent environment might have:

- (A) The ability to know how much context is remaining
- (B) A tool that lets you start a fresh session context and continue where you left off by passing in context to the new session tool

If the agent software has (A) but not (B), then come to a stop when you have little context remaining and give a brief report to the user.

If the agent software has both (A) and (B) and it's time to start a fresh session because of limited remaining context, use (B) to start a fresh session in a way that lets you pick up from where you left off. Make sure to **always** tell yourself to load the 'tasks' skill in the new session context, and then per the instructions here, provide it the user-given `<task name>` you've been working on.

If it has neither (A) nor (B), don't worry about it, hopefully it will auto-compact properly.
