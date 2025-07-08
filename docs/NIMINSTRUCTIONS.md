# NimInstructions.md

## Disclaimer

A **Nim** is an entity with agency, having a MECE (Mutually Exclusive, Collectively Exhaustive) role and responsibility within an organization, regardless of whether it is being executed by a physical or synthetic being (human, robot, drone, AI agent).

---

# Instructions for Nims Working with NimsforestWork

You are a Nim working in a project that uses the nimsforestwork system for task management. Follow these instructions for all development work.

## Core Workflow

### 1. Triage Work (Must Complete First)

**See TRIAGE.md for complete implementation details.**

1. **Check system compatibility**: `make nimsforestwork-hello`
2. **Process triage queue**: `make nimsforestwork-stamp-work` 
3. **Categorize stamped items**: Use branch-based claiming workflow
4. **Verify triage complete**: Ensure `issues/new/` and `issues/stamped/` are empty

### 2. Development Work  

**See DISTRIBUTEDLOCKING.md for complete implementation details.**

1. **Ensure all triage work is done**: Both `issues/new/` and `issues/stamped/` must be empty
2. **Find and claim work**: `make nimsforestwork-claim-work` with branch-based locking
3. **Work in isolation**: Use git worktrees for concurrent development
4. **Create new work**: Use `make nimsforestwork-newissue` only if no existing work exists

### 3. Issue Intake Process

**See TRIAGE.md for complete triage workflow.**

Items flow: `new/` → `stamped/` → (`pending-{item}/` ↔ user feedback) → `{category}/next/` → ... → (`pending-{item}/` ↔ user feedback) using branch-based claiming.

### 4. Work Item Categories

- **bugs/** - Fix broken functionality
- **changerequests/** - Modify existing functionality  
- **newfeatures/** - Add new functionality (renamed from proposals)
- **improvedocumentation/** - Improve documentation

### 5. Workflow States

**See DISTRIBUTEDLOCKING.md for complete state workflow.**

Each category follows: `next/` → `readyforanalysis/` → `inanalysis/` → `readyfordevelopment/` → `indevelopment/` → `readyfortesting/` → `intesting/` → `signedoff/` with `pending-{item-uuid}/` available at any state.

### 6. Final States

- **archive/production/** - Successfully deployed to production
- **archive/rejected/** - Rejected or cancelled items

### 7. State Transitions

**See DISTRIBUTEDLOCKING.md for complete branch-based claiming workflow.**

Uses **git worktrees + branch-based claiming** with pattern: `{target-state}-{work-item-uuid}`

## Available Commands

**Core Commands**:
- `make nimsforestwork-hello` - Check system compatibility (START HERE)
- `make nimsforestwork-init` - Initialize work folder structure
- `make nimsforestwork-newissue` - Create new issue for intake

**Triage Commands** (see TRIAGE.md):
- `make nimsforestwork-check-triage` - Check triage queue status
- `make nimsforestwork-stamp-work` - Stamp items with UUIDs (new→stamped)

**Development Commands** (see DISTRIBUTEDLOCKING.md):
- `make nimsforestwork-claim-work` - Claim work using branch-based locking
- `make nimsforestwork-check-stale-branches` - Check for abandoned work
- `make nimsforestwork-cleanup-stale` - Clean up stale branches

**Validation**:
- `make nimsforestwork-lint` - Validate all work items

## Netflix Deployment Model

- Every merge to main branch goes directly to production
- No human gatekeepers blocking releases
- Work items document the "why" behind every change
- Comprehensive testing ensures quality

## Step-by-Step Process

### For Any Development Task:

**See TRIAGE.md and DISTRIBUTEDLOCKING.md for complete workflow details.**

1. **Start**: `make nimsforestwork-hello`
2. **Complete triage**: `make nimsforestwork-stamp-work` until `issues/new/` is empty
3. **Find and claim work**: `make nimsforestwork-claim-work` 
4. **Work in isolation**: Use git worktrees for concurrent development
5. **Complete**: Merge back to main, move to `archive/production/`

### Creating New Work Items

Run `make nimsforestwork-newissue` to create work items in `issues/new/` - they automatically enter the triage workflow.

## Special Considerations

### Pending States
- `pending-{item-uuid}/` available in any category at any state
- Users provide feedback and manually move back to workflow
- Document what information is needed in `feedback-needed.md`

### Work Item Structure
- Start as files in `new/` and `stamped/`
- Become folders after categorization
- Accumulate artifacts throughout development lifecycle

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

## Quick Reference

**Essential reading for implementation**:
- **TRIAGE.md** - Complete triage process and UUID stamping
- **DISTRIBUTEDLOCKING.md** - Branch-based distributed locking with worktrees
- **WORKFLOW.md** - High-level overview and system architecture

**Start every session with**: `make nimsforestwork-hello`