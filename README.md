# Tasks Skill

Complicated long-running multi-step tasks are difficult for agents to handle, especially in large massive projects.

The tasks skill makes it easy for agents to successfully complete very large long-running tasks while avoiding most context-limit problems by breaking up the task across several steps.

## Installation

```
$ npx skills add taoeffect/tasks
```

Or manually create a `tasks` folder in your agent's `skills` folder, and place the `SKILL.md` in that folder. [Learn more about agent skills](https://agentskills.io/specification).

## Usage

To create a task, describe it and name it:

```
Read the 'tasks' skill.

vvvvvv DELETE THIS AND EDIT THE PARAGRAPH BELOW vvvvvv
I want you to do this very long and complicated thing.
Just setup the task, don't begin work on it.
^^^^^^ DELETE THIS AND EDIT THE PARAGRAPH ABOVE ^^^^^^

Call this task: my-task-name
```

This will create a folder in the current project: `.agents/tasks/my-task-name` with some of the following files:

- `CURRENT_DESIGN.md` - a detailed description of the codebase as it relates to this task
- `PLAN.md` - a detailed plan describings the changes to be made
- `TODOs.md` - a series of sequential steps to complete the task
- `STEP-<N>.md` - one step file for every TODO from TODOs.md to work in, with subtasks inside of it
- `KNOWLEDGE.md` - specialized useful knowledge for the agent for working with the project

Then create a new session and say:

```
Read the 'tasks' skill.

Begin task: my-task-name
```

And watch it fly.

You can pause the session at any time. And you can start an entirely new one and ask it to pick up from where it left off:

```
Read the 'tasks' skill and continue the task: my-task-name
```

If at some point you want it to it to re-evaluate the PLAN.md based on what it's discovered so far, tell it:

```
Read the 'tasks' skill.

Then verify the plan for the: my-task-name
```

This will cause it to do an in-depth review of the original plan based on the new knowledge it's gained while working on the task, and update any relevant files like PLAN.md, TODOs.md, etc.

## Optimization Tips

Many coding agents will have a built-in "todos" type tool that they use to keep track of long tasks.

We strongly recommend configuring the agent harness to disable such tools if you're going to use the tasks skill, as they could trip up the agent which would then be responsible for not just updating the task files, but also the harness' todos.

The tasks skill is superior to any agent harness `todos` tool because it's persistent and works across all coding agents.

## Tips For Very Large Projects

It is useful to maintain a directory structure that looks like this:

```
.agents/
  tasks/                  # task files created by this skill go here
    my-task/              # example task
  notes/                  # research notes should be saved here
  issues/                 # a "poor-man's quick & dirty issue tracker" of markdown files corresponding to bugs/features
  review/                 # subfolders named after equivalent tasks
    my-task/REVIEW-1.md   # first review of completed work for the 'my-task' task
```

For extremely complicated large tasks (like complicated refactors) I recommend having the agent make a high-level plan and store it in `.agents/notes/NAME_OF_PLAN.md`. Then either use that directly as the PLAN.md for the task (via the tasks skill), or tell the agent to use that high level plan as part of the creation of the task files for a new task.
