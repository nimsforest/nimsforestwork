# NimInstructions.md

## Disclaimer

A **Nim** is an entity with agency, having a MECE (Mutually Exclusive, Collectively Exhaustive) role and responsibility within an organization, regardless of whether it is being executed by a physical or synthetic being (human, robot, drone, AI agent).

---

# Instructions for Nims Working with NimsforestWork

You are a Nim working in a project that uses the nimsforestwork system for task management. Follow these instructions for all development work.

## Core Workflow

### 1. Before ANY Development Work

1. **Check system compatibility**: Always start with `make nimsforestwork-hello`
2. **Check existing work**: Look in `docs/work/` for existing work items
3. **Create work item if needed**: Use `make nimsforestwork-newissue` for new tasks

### 2. Issue Intake Process

All requests start as issues for proper categorization:

1. **issues/new/** - Initial intake of all requests
2. **issues/triaging/** - Under review for categorization
3. **issues/pending/** - Waiting for additional information

Once categorized, items move to appropriate work categories.

### 3. Work Item Categories

- **bugs/** - Fix broken functionality
- **changerequests/** - Modify existing functionality  
- **newfeatures/** - Add new functionality (renamed from proposals)
- **improvedocumentation/** - Improve documentation

### 4. Workflow States

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

### 5. Final States

- **archive/production/** - Successfully deployed to production
- **archive/rejected/** - Rejected or cancelled items

### 6. State Transitions

Use git to move files between states:

```bash
# Issue intake and categorization
git mv docs/work/issues/new/myrequest.md docs/work/issues/triaging/
git commit -m "work: triage my request"

# Promote to appropriate category
git mv docs/work/issues/triaging/myrequest.md docs/work/newfeatures/next/myfeature.md
git commit -m "work: promote to new feature"

# Move through development workflow
git mv docs/work/newfeatures/next/myfeature.md docs/work/newfeatures/readyforanalysis/
git commit -m "work: ready for analysis - my feature"

git mv docs/work/newfeatures/readyforanalysis/myfeature.md docs/work/newfeatures/inanalysis/
git commit -m "work: start analysis - my feature"

git mv docs/work/newfeatures/inanalysis/myfeature.md docs/work/newfeatures/readyfordevelopment/
git commit -m "work: ready for development - my feature"

# Continue through testing and completion
git mv docs/work/newfeatures/indevelopment/myfeature.md docs/work/newfeatures/readyfortesting/
git commit -m "work: ready for testing - my feature"

git mv docs/work/newfeatures/intesting/myfeature.md docs/work/newfeatures/signedoff/
git commit -m "work: signed off - my feature"

# Final archive
git mv docs/work/newfeatures/signedoff/myfeature.md docs/work/archive/production/
git commit -m "work: deployed to production - my feature"
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
2. **Check**: Look in `docs/work/` for existing related work items
3. **Create**: If no existing item, run `make nimsforestwork-newissue`
4. **Triage**: Move issue from `new/` to `triaging/` for review
5. **Categorize**: Promote issue to appropriate category (bug/changerequest/newfeature/documentation)
6. **Analyze**: Move to `readyforanalysis/` → `inanalysis/` → `readyfordevelopment/`
7. **Develop**: Move to `indevelopment/` during implementation
8. **Test**: Move to `readyfortesting/` → `intesting/` → `signedoff/`
9. **Deploy**: Move to `archive/production/` after deployment
10. **Block**: Move to `pendingfeedback/` if blocked (bugs/changerequests only)

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