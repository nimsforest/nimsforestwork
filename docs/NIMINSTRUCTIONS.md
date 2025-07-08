# NimInstructions.md

## Disclaimer

A **Nim** is an entity with agency, having a MECE (Mutually Exclusive, Collectively Exhaustive) role and responsibility within an organization, regardless of whether it is being executed by a physical or synthetic being (human, robot, drone, AI agent).

---

# Instructions for Nims Working with NimsforestWork

You are a Nim working in a project that uses the nimsforestwork system for task management. Follow these instructions for all development work.

## Core Workflow

### 1. Triage Work (Must Complete First)

1. **Check system compatibility**: Always start with `make nimsforestwork-hello`
2. **Triage loop**: Repeat until `docs/work/issues/new/` is empty:
   - Check for untriaged items in `issues/new/`
   - Claim one by moving to `triaging/`: `git mv docs/work/issues/new/item.md docs/work/issues/triaging/`
   - Commit and push immediately to prevent conflicts: `git commit -m "work: claim for triage" && git push`
   - Analyze and promote to appropriate category
   - Commit and push the promotion: `git commit -m "work: promote to category" && git push`
3. **Verify triage complete**: Ensure `issues/new/` is empty before proceeding

### 2. Development Work

1. **Ensure all triage work is done**: `issues/new/` must be empty
2. **Check existing work**: Look in `docs/work/` for items in `ready*` states
3. **Claim development work**: Move item to appropriate `in*` state and push immediately
4. **Create work item if needed**: Use `make nimsforestwork-newissue` only if no existing work exists

### 3. Issue Intake Process

All requests start as issues for proper categorization:

1. **issues/new/** - Initial intake of all requests
2. **issues/triaging/** - Under review for categorization
3. **issues/pending/** - Waiting for additional information

Once categorized, items move to appropriate work categories.

### 4. Work Item Categories

- **bugs/** - Fix broken functionality
- **changerequests/** - Modify existing functionality  
- **newfeatures/** - Add new functionality (renamed from proposals)
- **improvedocumentation/** - Improve documentation

### 5. Workflow States

Each category follows the enterprise workflow:

1. **next/** - Ready for analysis (replaces planned)
2. **readyforanalysis/** - Analysis phase ready
3. **inanalysis/** - Currently being analyzed
4. **readyfordevelopment/** - Development phase ready
5. **indevelopment/** - Currently being developed
6. **readyfortesting/** - Testing phase ready
7. **intesting/** - Currently being tested
8. **signedoff/** - Complete and approved
9. **pendingfeedback/** - Blocked waiting for clarification (bugs/changerequests only)

### 6. Final States

- **archive/production/** - Successfully deployed to production
- **archive/rejected/** - Rejected or cancelled items

### 7. State Transitions

Use git to move files between states:

```bash
# Issue intake and categorization (with immediate push to claim)
git mv docs/work/issues/new/myrequest.md docs/work/issues/triaging/
git commit -m "work: claim for triage - my request"
git push

# Promote to appropriate category (with immediate push)
git mv docs/work/issues/triaging/myrequest.md docs/work/newfeatures/next/myfeature.md
git commit -m "work: promote to new feature"
git push

# Move through development workflow (claim by moving to in* state)
git mv docs/work/newfeatures/next/myfeature.md docs/work/newfeatures/readyforanalysis/
git commit -m "work: ready for analysis - my feature"
git push

git mv docs/work/newfeatures/readyforanalysis/myfeature.md docs/work/newfeatures/inanalysis/
git commit -m "work: claim and start analysis - my feature"
git push

git mv docs/work/newfeatures/inanalysis/myfeature.md docs/work/newfeatures/readyfordevelopment/
git commit -m "work: ready for development - my feature"
git push

git mv docs/work/newfeatures/readyfordevelopment/myfeature.md docs/work/newfeatures/indevelopment/
git commit -m "work: claim and start development - my feature"
git push

# Continue through testing and completion
git mv docs/work/newfeatures/indevelopment/myfeature.md docs/work/newfeatures/readyfortesting/
git commit -m "work: ready for testing - my feature"
git push

git mv docs/work/newfeatures/readyfortesting/myfeature.md docs/work/newfeatures/intesting/
git commit -m "work: claim and start testing - my feature"
git push

git mv docs/work/newfeatures/intesting/myfeature.md docs/work/newfeatures/signedoff/
git commit -m "work: signed off - my feature"
git push

# Final archive
git mv docs/work/newfeatures/signedoff/myfeature.md docs/work/archive/production/
git commit -m "work: deployed to production - my feature"
git push
```

## Available Commands

- `make nimsforestwork-hello` - Check system compatibility (START HERE)
- `make nimsforestwork-init` - Initialize work folder structure
- `make nimsforestwork-newissue` - Create new issue for intake
- `make nimsforestwork-start-triage` - Move issue from new to triaging
- `make nimsforestwork-promote-to-bug` - Promote issue to bug category
- `make nimsforestwork-promote-to-changerequest` - Promote issue to change request
- `make nimsforestwork-promote-to-newfeature` - Promote issue to new feature
- `make nimsforestwork-promote-to-documentation` - Promote issue to documentation
- `make nimsforestwork-lint` - Validate all work items

## Netflix Deployment Model

- Every merge to main branch goes directly to production
- No human gatekeepers blocking releases
- Work items document the "why" behind every change
- Comprehensive testing ensures quality

## Step-by-Step Process

### For Any Development Task:

1. **Start**: `make nimsforestwork-hello`
2. **Complete triage**: Process all items in `issues/new/` until empty
3. **Check existing work**: Look in `docs/work/` for items in `ready*` states
4. **Claim work**: Move to appropriate `in*` state and push immediately
5. **Create new work**: Only if no existing items, use `make nimsforestwork-newissue`
6. **Work through states**: `ready*` → `in*` → `ready*` (next stage)
7. **Always push**: Commit and push immediately after each state change
8. **Complete**: Move to `signedoff/` → `archive/production/`
9. **Block**: Move to `pendingfeedback/` if blocked (bugs/changerequests only)

### Creating New Work Items

When running `make nimsforestwork-newissue`:

1. Enter title (will become filename: lowercase, no spaces/hyphens)
2. Enter description (press Ctrl+D when done)
3. Edit the created file to fill in all template sections including:
   - Business Rating (1-5 scale)
   - Complexity Assessment (Simple/Normal/Complex/Madness)
   - Platform information (if applicable)
4. Commit: `git add docs/work/issues/new/{filename}.md && git commit -m "work: {title}"`
5. Use triage commands to categorize and promote the issue

## Special Considerations

### Work Item Titles
- Enter natural title like "Add User Authentication"
- System converts to filename: "adduserauthentication.md"
- Keep titles descriptive but concise

### Business Rating and Complexity
- **Business Rating**: 1-5 scale (1=Low, 5=Critical)
- **Complexity**: Simple/Normal/Complex/Madness
- These fields guide prioritization and resource allocation

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
- Proper folder structure (issues/, bugs/, changerequests/, newfeatures/, improvedocumentation/)
- Valid markdown format  
- Work item integrity
- Workflow state compliance
- Business rating and complexity fields
- System health

## Remember

- **Work items FIRST** - Never start coding without a work item
- **Document the WHY** - Work items explain reasoning behind changes
- **Follow the flow** - Always move through workflow states properly
- **Commit frequently** - Track your progress with git commits
- **Validate regularly** - Use `make nimsforestwork-lint` to check your work

Start every session with: `make nimsforestwork-hello`