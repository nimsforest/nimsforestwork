# NimsforestWork Workflow Documentation

A practical but thorough guide on how to manage any type of work using **distributed git-based coordination**. The focus is to be as agile as possible while ensuring quality through **branch-based distributed locking** and **folder-based state tracking**.

This document provides a high-level overview of the complete workflow. For detailed implementation, see:
- **[TRIAGE.md](TRIAGE.md)** - Triage process and stamping workflow
- **[DISTRIBUTEDLOCKING.md](DISTRIBUTEDLOCKING.md)** - Branch-based distributed locking mechanism

This document covers all aspects from input (new things to make) to production (how to make quality items while staying nimble) to output (releasing new quality versions). The system applies to any type of work - software, hardware, documentation, research, or even atomic-level processes.

## Overview

NimsforestWork implements a **distributed, git-native** issue management and work coordination workflow that supports the complete lifecycle from initial request to production delivery. The system uses **branch-based distributed locking** to coordinate thousands of concurrent workers without requiring central coordination infrastructure.

## Work Structure

```
docs/work/
├── issues/                           # Issue intake and triage
│   ├── new/                          # User-submitted files (original names)
│   │   └── lostmypassword.md         # Simple .md files
│   ├── stamped/                      # UUID-stamped files ready for categorization
│   │   └── lostmypassword-a1b2c3d4.md  # Files with unique identifiers
│   ├── pending-{item-uuid}/          # Waiting for user feedback before categorization
│   │   └── lostmypassword-a1b2c3d4/  # Work item folders
│   │       ├── lostmypassword-a1b2c3d4.md  # Original work item
│   │       └── feedback-needed.md    # What info is needed from user
│   └── template.md                   # Template for new issues
├── bugs/                             # Categorized bug work items
│   ├── next/                         # Ready for business rating assignment
│   │   └── lostmypassword-a1b2c3d4/  # Work item FOLDERS (after categorization)
│   │       └── lostmypassword-a1b2c3d4.md  # Original file inside folder
│   ├── readyforanalysis/             # Ready for technical analysis
│   ├── inanalysis/                   # Currently being analyzed
│   ├── readyfordevelopment/          # Ready for development work
│   ├── indevelopment/                # Currently being developed
│   │   └── userauth-b5c6d7e8/        # Rich work item folders
│   │       ├── userauth-b5c6d7e8.md  # Original work item
│   │       ├── analysis.md           # Analysis documents
│   │       ├── design.md             # Design documents
│   │       └── tests.md              # Test specifications
│   ├── readyfortesting/              # Ready for testing
│   ├── intesting/                    # Currently being tested
│   ├── signedoff/                    # Complete and approved
│   └── pendingfeedback/              # Blocked waiting for clarification
├── changerequests/                   # Same structure as bugs/ (with pending-{item-uuid}/)
├── newfeatures/                      # Same structure as bugs/ (with pending-{item-uuid}/)
├── improvedocumentation/             # Same structure as bugs/ (with pending-{item-uuid}/)
└── archive/
    ├── production/                   # Successfully deployed
    └── rejected/                     # Rejected or cancelled items
```

## Input Phase: New Things to Make

All work items enter through the **triage process** documented in **[TRIAGE.md](TRIAGE.md)**. This ensures unique identification and proper categorization through distributed coordination.

### Two-Stage Triage Process

1. **Stamping** (`new/` → `stamped/`): Add UUID to prevent duplicates
2. **Categorization** (`stamped/` → `{category}/next/` OR `pending-{item}/`): Determine work type and create folder structure, or request more user information

**User Feedback Loop**: If more information is needed, items move to `pending-{item-uuid}/` state. Users provide additional information and manually move items back to `stamped/` for re-triage.

See **[TRIAGE.md](TRIAGE.md)** for complete implementation details.

### Work Item Creation Methods

1. **Manual File Drop**: Users create `.md` files in `docs/work/issues/new/`
2. **Make Command**: `make nimsforestwork-newissue TITLE=... MESSAGE=...`
3. **Direct Creation**: Team members can create work items directly in appropriate categories

All methods feed into the **triage process** for UUID stamping and categorization.

### Work Item Types

- **bugs/**: Fix broken functionality (any domain)
- **changerequests/**: Modify existing functionality (any domain)
- **newfeatures/**: Add new functionality (any domain)
- **improvedocumentation/**: Improve documentation (any domain)

Each type follows the same workflow states after categorization. The system applies universally - whether the work involves code, hardware, processes, or even atomic-level operations.

## Production Phase: Making Quality Items While Staying Nimble

### Distributed Coordination System

The production phase uses **branch-based distributed locking** with **git worktrees** for coordination. This enables:

- **Thousands of concurrent workers** working without central coordination
- **Git worktrees** for same-machine concurrent agents
- **Branch-based claiming** prevents work conflicts
- **Folder-based state tracking** through filesystem

See **[DISTRIBUTEDLOCKING.md](DISTRIBUTEDLOCKING.md)** for complete implementation details.

### Product Backlog Lifecycle

All work items follow this **FIFO-based state progression** using distributed locking:

#### Workflow States

1. **next/**: Ready for business rating assignment
2. **readyforanalysis/**: Ready for technical analysis  
3. **inanalysis/**: Currently being analyzed (claimed via branch)
4. **readyfordevelopment/**: Ready for development work
5. **indevelopment/**: Currently being developed (claimed via branch)
6. **readyfortesting/**: Ready for testing
7. **intesting/**: Currently being tested (claimed via branch)
8. **signedoff/**: Complete and approved
9. **pending-{item-uuid}/**: Blocked waiting for user clarification (any category, any state)

#### State Transition Claiming

Each state transition requires **branch-based claiming**:

```bash
# Example: Claiming analysis work
git worktree add ../inanalysis-userauth-b5c6d7e8 -b inanalysis-userauth-b5c6d7e8
cd ../inanalysis-userauth-b5c6d7e8
git push -u origin inanalysis-userauth-b5c6d7e8  # THE CLAIM

# If successful, move work item folder
git mv docs/work/newfeatures/readyforanalysis/userauth-b5c6d7e8/ \
       docs/work/newfeatures/inanalysis/userauth-b5c6d7e8/
```

**Branch naming**: `{target-state}-{work-item-uuid}`

### Work Item Enrichment

As work items progress through states, they accumulate artifacts:

- **Analysis phase**: `analysis.md`, `requirements.md`
- **Development phase**: `design.md`, `implementation.md`, `tests.md`  
- **Testing phase**: `test-results.md`, `review.md`

All artifacts remain in the work item folder as it moves through states.

### Priority and Complexity

**Business Rating Priority** (assigned in `next/` state):
- **5 (Urgent)**: Immediate attention, dedicated releases
- **4 (High)**: Fast-track to existing or new releases  
- **3/2 (Normal)**: Standard release scheduling
- **1 (Low)**: Opportunistic inclusion

**Complexity Assessment** (assigned during analysis):
- **Simple**: Straightforward implementation
- **Normal**: Standard complexity
- **Complex**: Requires significant effort
- **Madness**: Extremely complex, requires careful planning

**FIFO Processing**: Within same business rating: Simple > Normal > Complex > Madness

## State Transitions

### Branch-Based Distributed Locking

**Critical**: All state transitions use **git worktrees** and **branch-based claiming** to prevent concurrent work conflicts (detailed in **[DISTRIBUTEDLOCKING.md](DISTRIBUTEDLOCKING.md)**).

The system **replaces traditional push-to-claim** with **branch-based claiming** for superior distributed coordination.

### Standard Flow

1. **Triage**: `new/` → `stamped/` → `{category}/next/` (detailed in **[TRIAGE.md](TRIAGE.md)**)
2. **Development**: `next/` → `readyforanalysis/` → `inanalysis/` → `readyfordevelopment/` → `indevelopment/` → `readyfortesting/` → `intesting/` → `signedoff/`
3. **Completion**: `archive/production/` or `archive/rejected/`

### Claiming Mechanism

**Git worktree + branch claiming** (detailed in **[DISTRIBUTEDLOCKING.md](DISTRIBUTEDLOCKING.md)**):

```bash
# Standard claiming pattern
WORK_ITEM="userauth-b5c6d7e8"
TARGET_STATE="indevelopment"
BRANCH_NAME="${TARGET_STATE}-${WORK_ITEM}"

# Create isolated worktree + claim
git worktree add ../${BRANCH_NAME} -b ${BRANCH_NAME}
cd ../${BRANCH_NAME}
git push -u origin ${BRANCH_NAME}  # THE LOCK

# If successful, move work item folder
git mv docs/work/newfeatures/readyfordevelopment/${WORK_ITEM}/ \
       docs/work/newfeatures/indevelopment/${WORK_ITEM}/
```

### Exception Flows
- **Pending Information**: Any state → `pending-{item-uuid}/` → return to appropriate state (user-driven)
- **Rework Required**: `intesting/` → `readyforanalysis/` (as new bug)
- **Cancellation**: Any state → `archive/rejected/`
- **Stale Branch Recovery**: Automated cleanup of abandoned work
- **User Feedback**: Users manually move items from `pending-{item-uuid}/` back to workflow when they provide information

## Quality Gates

Each state transition requires specific criteria:
- **Analysis**: Clear understanding of requirements, complexity assessment
- **Development**: Documented approach, design decisions recorded
- **Testing**: Steps to Test and Expected Result documented
- **Production**: All tests passed, release approved, deployed successfully

Quality gates are enforced through work item folder content - missing required artifacts prevent state progression.

## Release Management

### Netflix Deployment Model

- **Every merge to main** goes directly to production
- **No human gatekeepers** blocking releases
- **Work items document** the "why" behind every change
- **Comprehensive testing** ensures quality before merge

### Release Process

1. Work items complete development → `readyfortesting/`
2. Agents claim testing work via branch-based locking
3. Testing completed → `signedoff/`
4. **Automatic deployment** → `archive/production/`

### Monitoring and Recovery

- **Branch monitoring**: Detect stale/abandoned work
- **Automatic cleanup**: Recovery of failed work items
- **Distributed metrics**: No central tracking required
- **Self-healing system**: Agents detect and resolve conflicts

## System Architecture Benefits

### Enterprise Scale Coordination

- **Thousands of concurrent workers** supported
- **Zero infrastructure** requirements beyond git
- **Same-machine concurrency** via git worktrees  
- **Perfect fault tolerance** through distributed design
- **Self-organizing work flow** via FIFO folder processing

### Complete Documentation

For implementation details, see:
- **[TRIAGE.md](TRIAGE.md)**: Complete triage process and stamping workflow
- **[DISTRIBUTEDLOCKING.md](DISTRIBUTEDLOCKING.md)**: Branch-based distributed locking mechanism
- **[NIMINSTRUCTIONS.md](NIMINSTRUCTIONS.md)**: Agent-specific implementation instructions

This workflow ensures systematic handling of all work types while maintaining quality and agility at enterprise scale through pure git-based distributed coordination. The system is domain-agnostic and scales from software development to manufacturing, research, and even atomic-level processes.