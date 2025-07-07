# Instructions for AI Agents Working with NimsforestWork

You are an AI agent working in a project that uses the nimsforestwork system for task management. Follow these instructions for all development work.

## Core Workflow

### 1. Before ANY Development Work

1. **Check system compatibility**: Always start with `make nimsforestwork-hello`
2. **Check existing work**: Look in `docs/work/` for existing work items
3. **Create work item if needed**: Use `make nimsforestwork-newissue` for new tasks

### 2. Work Item Types

- **changerequests/** - Modify existing functionality
- **bugs/** - Fix broken functionality (has `pendingfeedback/` state)
- **proposals/** - Add new functionality  
- **improvedocumentation/** - Improve documentation

### 3. Workflow States

Move work items through these states using `git mv`:

1. **new/** - Newly created items
2. **planned/** - Reviewed and accepted for implementation
3. **active/** - Currently being worked on
4. **pendingfeedback/** - Blocked waiting for clarification (bugs/changerequests only)
5. **ready/** - Implementation complete, ready for merge
6. **archive/** - Completed items

### 4. State Transitions

Use git to move files between states:

```bash
# Move from new to planned
git mv docs/work/changerequests/new/myfeature.md docs/work/changerequests/planned/
git commit -m "work: plan my feature implementation"

# Move to active when starting work
git mv docs/work/changerequests/planned/myfeature.md docs/work/changerequests/active/  
git commit -m "work: start my feature implementation"

# Complete work
git mv docs/work/changerequests/active/myfeature.md docs/work/changerequests/ready/
git commit -m "work: my feature ready for review"

# After merge to main
git mv docs/work/changerequests/ready/myfeature.md docs/work/changerequests/archive/
git commit -m "work: completed my feature"
```

## Available Commands

- `make nimsforestwork-hello` - Check system compatibility (START HERE)
- `make nimsforestwork-init` - Initialize work folder structure
- `make nimsforestwork-newissue` - Create new work item interactively
- `make nimsforestwork-lint` - Validate all work items

## Netflix Deployment Model

- Every merge to main branch goes directly to production
- No human gatekeepers blocking releases
- Work items document the "why" behind every change
- Comprehensive testing ensures quality

## Step-by-Step Process

### For Any Development Task:

1. **Start**: `make nimsforestwork-hello`
2. **Check**: Look in `docs/work/` for existing related work items
3. **Create**: If no existing item, run `make nimsforestwork-newissue`
4. **Plan**: Move item from `new/` to `planned/` with git mv + commit
5. **Work**: Move to `active/` when starting implementation
6. **Block**: Move to `pendingfeedback/` if blocked (bugs/changerequests only)
7. **Complete**: Move to `ready/` when implementation done
8. **Archive**: Move to `archive/` after merge to main

### Creating New Work Items

When running `make nimsforestwork-newissue`:

1. Select type (1-4)
2. Enter title (will become filename: lowercase, no spaces/hyphens)
3. Enter description (press Ctrl+D when done)
4. Edit the created file to fill in all template sections
5. Commit: `git add docs/work/{type}/new/{filename}.md && git commit -m "work: {title}"`

## Special Considerations

### Work Item Titles
- Enter natural title like "Add User Authentication"
- System converts to filename: "adduserauthentication.md"
- Keep titles descriptive but concise

### Blocked Work (pendingfeedback/)
- Only for bugs/ and changerequests/
- Use when you need clarification or are waiting for external input
- Always document what you're waiting for in the work item

### Documentation Integration
- Work items appear in docs visualizer at docs/work/
- This makes work transparent and discoverable
- Always keep work items updated with current status

## Error Handling

If you encounter:
- **"docs/work/ not found"**: Run `make nimsforestwork-init`
- **"File already exists"**: Choose different title or check existing items
- **Validation errors**: Run `make nimsforestwork-lint` to diagnose

## Quality Checks

Always run `make nimsforestwork-lint` to ensure:
- Proper folder structure
- Valid markdown format  
- Work item integrity
- System health

## Remember

- **Work items FIRST** - Never start coding without a work item
- **Document the WHY** - Work items explain reasoning behind changes
- **Follow the flow** - Always move through workflow states properly
- **Commit frequently** - Track your progress with git commits
- **Validate regularly** - Use `make nimsforestwork-lint` to check your work

Start every session with: `make nimsforestwork-hello`