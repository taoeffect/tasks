# Tasks Skill

Complicated long-running multi-step tasks are difficult for agents to handle, especially in large massive projects.

The tasks skill makes it easy for agents to successfully complete very large long-running tasks while avoiding most context-limit problems by breaking up the task across several steps.

## Usage

To create a task, describe it and name it:

```
Read the task skill.

I want you to do this very long and complicated thing. Just setup the task, don't begin work on it.

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
Read the task skill.

Begin task: my-task-name
```

And watch it fly.

If at some point you want it to it to re-evaluate the PLAN.md based on what it's discovered so far, tell it:

``` json
Read the task skill.

Then verify the plan for the: my-task-name
```

This will cause it to do an in-depth review of the original plan based on the new knowledge it's gained while working on the task, and update any relevant files like PLAN.md, TODOs.md, etc.

