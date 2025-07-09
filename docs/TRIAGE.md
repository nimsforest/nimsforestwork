# Triage Workflow Documentation

## Overview

The triage process is the **entry point** for all work items in the nimsforestwork system. For high-level workflow context, see [WORKFLOW.md](WORKFLOW.md). It consists of two critical stages that prevent race conditions and ensure unique identification of work items.

## Two-Stage Triage Process

### Stage 1: Stamping (new/ → stamped/)

**Purpose**: Add unique identifiers to work items to prevent duplicates

**Challenge**: Multiple agents could try to stamp the same file simultaneously, creating duplicates.

**Solution**: Branch-based claiming for stamping operations

#### Stamping Workflow

```bash
# Agent discovers work in new/
ls docs/work/issues/new/*.md

# Example: Found lostmypassword.md
# Step 1: Claim the stamping work with branch
git checkout -b stamp-lostmypassword
git push origin stamp-lostmypassword  # ONLY ONE AGENT SUCCEEDS

# Step 2: If push succeeds, you own the stamping
UUID=$(uuidgen | cut -d'-' -f1)  # Generate 8-character UUID
git mv docs/work/issues/new/lostmypassword.md docs/work/issues/stamped/lostmypassword-${UUID}.md
git commit -m "work: stamp with UUID - lostmypassword-${UUID}"
git push

# Step 3: Clean up stamping branch (work complete)
git checkout main
git branch -D stamp-lostmypassword  # Force delete - branch is ephemeral lock
git push origin --delete stamp-lostmypassword
```

#### Race Condition Prevention

- **Branch naming**: `stamp-{original-filename}`
- **Atomic claiming**: Only one agent can push branch with same name
- **Failed agents**: Immediately try next available work item
- **No duplicates**: Impossible to create multiple stamped files from one original
- **No upstream tracking**: Branches are ephemeral locks, not collaboration branches
- **Force deletion safe**: `git branch -D` is correct since branches are temporary locks

### Stage 2: Categorization (stamped/ → category/)

**Purpose**: Move stamped work items to appropriate work categories

**Process**: Standard branch-based claiming for category promotion

#### Categorization Workflow

```bash
# Agent discovers stamped work
ls docs/work/issues/stamped/*.md

# Example: Found lostmypassword-a1b2c3d4.md
# Step 1: Claim the categorization work
git checkout -b triaging-lostmypassword-a1b2c3d4
git push origin triaging-lostmypassword-a1b2c3d4

# Step 2: Analyze and categorize
# Read the work item, determine if it's a bug, changerequest, newfeature, etc.

# Step 3a: If more information needed - move to pending
if more_info_needed; then
    mkdir -p docs/work/issues/pending-lostmypassword-a1b2c3d4
    git mv docs/work/issues/stamped/lostmypassword-a1b2c3d4.md \
           docs/work/issues/pending-lostmypassword-a1b2c3d4/lostmypassword-a1b2c3d4.md
    # Add communication log explaining what info is needed
    echo "Need: More details about error conditions" > \
           docs/work/issues/pending-lostmypassword-a1b2c3d4/feedback-needed.md
    git add docs/work/issues/pending-lostmypassword-a1b2c3d4/
    git commit -m "work: pending feedback - lostmypassword-a1b2c3d4"
    git push
else
    # Step 3b: Create category folder and move file into it
    mkdir -p docs/work/bugs/next/lostmypassword-a1b2c3d4
    git mv docs/work/issues/stamped/lostmypassword-a1b2c3d4.md \
           docs/work/bugs/next/lostmypassword-a1b2c3d4/lostmypassword-a1b2c3d4.md
    git add docs/work/bugs/next/lostmypassword-a1b2c3d4/
    git commit -m "work: categorize as bug - lostmypassword-a1b2c3d4"
    git push
fi

# Step 4: Clean up triaging branch
git checkout main
git branch -D triaging-lostmypassword-a1b2c3d4  # Force delete - branch is ephemeral lock
git push origin --delete triaging-lostmypassword-a1b2c3d4
```

## Folder Structure

```
docs/work/issues/
├── new/                           # User-submitted work items (original names)
│   └── lostmypassword.md         # Simple files
├── stamped/                      # UUID-stamped work items ready for categorization  
│   └── lostmypassword-a1b2c3d4.md  # Simple files with UUIDs
├── pending-{item-uuid}/          # Waiting for user feedback before categorization
│   └── lostmypassword-a1b2c3d4/  # Work item folders
│       ├── lostmypassword-a1b2c3d4.md  # Original work item
│       └── feedback-needed.md    # What info is needed from user
└── template.md                   # Template for new issues

docs/work/bugs/
├── next/                         # Categorized bugs ready for analysis
│   └── lostmypassword-a1b2c3d4/  # Work item FOLDERS
│       └── lostmypassword-a1b2c3d4.md  # Original file inside folder
├── readyforanalysis/             # Ready for technical analysis
│   └── userauth-b5c6d7e8/        # Work item folders
│       ├── userauth-b5c6d7e8.md  # Original work item
│       └── analysis.md           # Analysis documents can be added
├── inanalysis/                   # Currently being analyzed
├── readyfordevelopment/          # Ready for development work  
├── indevelopment/                # Currently being developed
│   └── userauth-b5c6d7e8/        # Work item folders
│       ├── userauth-b5c6d7e8.md  # Original work item
│       ├── analysis.md           # Analysis from previous stage
│       ├── design.md             # Design documents
│       └── tests.md              # Test specifications
├── readyfortesting/              # Ready for testing
├── intesting/                    # Currently being tested
├── signedoff/                    # Complete and approved
└── pending-{item-uuid}/          # Blocked waiting for user clarification
    └── userauth-b5c6d7e8/        # Work item folders
        ├── userauth-b5c6d7e8.md  # Original work item
        └── feedback-needed.md    # What info is needed from user
```

## Branch Naming Conventions

### Stamping Branches
- **Pattern**: `stamp-{original-filename}`
- **Example**: `stamp-lostmypassword`
- **Lifecycle**: Create → Push → Stamp → Delete
- **No upstream tracking**: Branches are temporary locks, deleted immediately after use
- **Force deletion**: Use `git branch -D` since branches are ephemeral locks

### Triaging Branches  
- **Pattern**: `triaging-{stamped-filename}`
- **Example**: `triaging-lostmypassword-a1b2c3d4`
- **Lifecycle**: Create → Push → Categorize OR Pending → Delete

### Pending Branches
- **Pattern**: `pending-{item-uuid}`
- **Example**: `pending-lostmypassword-a1b2c3d4`
- **Used for**: Moving items to pending state during triage or development

## Branch Deletion Logic

### Why Force Delete (`-D`) is Correct

**Branch lifecycle for stamping:**
1. **Create**: `git checkout -b stamp-filename`
2. **Push**: `git push origin stamp-filename` (no upstream tracking)
3. **Work**: Move file, commit, push changes
4. **Delete**: `git branch -D stamp-filename` (force delete)

**Why `-D` instead of `-d`:**
- **Without upstream tracking**: Git doesn't know branch exists remotely
- **Safety check fails**: `git branch -d` checks if branch is "fully merged"
- **False negative**: Git thinks branch has "unmerged changes" 
- **Actually safe**: We successfully pushed our work, branch served its purpose

**Branch purpose:**
- **Ephemeral locks**: Temporary claim mechanism, not collaboration branches
- **Work already saved**: Changes committed and pushed before deletion
- **No data loss**: Branch deletion doesn't affect pushed commits
- **Cleanup required**: Prevents branch accumulation

## Error Handling

### Stamping Failures
```bash
# If branch push fails (someone else claimed it)
git branch -D stamp-lostmypassword  # Force delete - branch is ephemeral lock
# Immediately try next available work item
```

### Categorization Failures
```bash
# If branch push fails (someone else claimed it)
git branch -D triaging-lostmypassword-a1b2c3d4  # Force delete - branch is ephemeral lock
# Immediately try next available work item
```

### Stale Branch Detection
```bash
# Find abandoned stamping work
git branch -r | grep 'origin/stamp-'

# Check if branch is behind main (indicates failure)
git log --oneline main..origin/stamp-lostmypassword

# If branch is stale, force cleanup (by admin)
git push origin --delete stamp-lostmypassword
```

## User Experience

### Manual Work Item Creation
Users can drop `.md` files directly into `docs/work/issues/new/`:

```bash
# User creates file manually
echo "# Lost Password Issue" > docs/work/issues/new/lostmypassword.md
git add docs/work/issues/new/lostmypassword.md
git commit -m "Add lost password issue"
git push
```

### User Feedback Loop
When more information is needed, users provide feedback and resume triage:

```bash
# User finds their item in pending state
ls docs/work/issues/pending-lostmypassword-a1b2c3d4/
# - lostmypassword-a1b2c3d4.md (original)
# - feedback-needed.md (what info is needed)

# User adds additional information
echo "Error occurs when using Chrome browser" > \
  docs/work/issues/pending-lostmypassword-a1b2c3d4/additional-info.md

# User manually moves back to stamped for re-triage
git mv docs/work/issues/pending-lostmypassword-a1b2c3d4/lostmypassword-a1b2c3d4.md \
       docs/work/issues/stamped/lostmypassword-a1b2c3d4.md
git commit -m "user: provided additional info for lostmypassword-a1b2c3d4"
git push
```

### Automated Processing
- Agents continuously monitor `new/` and `stamped/` folders
- Automatic stamping adds UUIDs asynchronously
- Agents process stamped items for categorization
- No user action required except when feedback is needed

## Quality Assurance

### UUID Generation
- **Method**: `uuidgen | cut -d'-' -f1`
- **Length**: 8 characters (4.3 billion combinations)
- **Collision risk**: Negligible for practical work volumes

### File Validation
- Work items must be valid markdown with title
- Template compliance checked during categorization
- Business rating and complexity assessment added during triage

## Monitoring and Metrics

### Triage Backlog
```bash
# Check stamping backlog
find docs/work/issues/new -name '*.md' ! -name 'template.md' | wc -l

# Check categorization backlog  
find docs/work/issues/stamped -name '*.md' | wc -l
```

### Active Triage Work
```bash
# Check active stamping operations
git branch -r | grep 'origin/stamp-' | wc -l

# Check active categorization operations
git branch -r | grep 'origin/triaging-' | wc -l
```

## Best Practices

1. **FIFO Processing**: Always process oldest files first within each folder
2. **Quick Categorization**: Don't spend excessive time analyzing during triage
3. **Clean Branch Hygiene**: Always delete branches after successful operations
4. **Failure Recovery**: Immediately try next work item if claiming fails
5. **No Central Coordination**: Each agent operates independently

## Work Item Evolution

### File to Folder Transition

**Critical transition** happens during categorization:

- **Before categorization**: Simple `.md` files in `new/` and `stamped/`
- **After categorization**: Folder-based work items in category directories

### Folder-Based Work Items

Once categorized, work items become **folders** that can contain:

- **Original work item**: `{name}-{uuid}.md` (the original request)
- **Analysis documents**: `analysis.md`, `requirements.md`
- **Design documents**: `design.md`, `architecture.md`  
- **Test specifications**: `tests.md`, `test-cases.md`
- **Implementation notes**: `implementation.md`, `notes.md`
- **Review feedback**: `review.md`, `feedback.md`

### Benefits of Folder Structure

1. **Rich work items**: Can contain multiple related documents
2. **Atomic moves**: Entire folder moves between states
3. **Version control**: All work item artifacts tracked together
4. **Collaboration**: Multiple files allow parallel work on different aspects
5. **Traceability**: Complete history of work item evolution

## Integration with Development Workflow

Once categorized, work items follow the standard branch-based development workflow documented in [DISTRIBUTEDLOCKING.md](DISTRIBUTEDLOCKING.md). The unique UUID and folder structure ensures no collisions throughout the entire development lifecycle.

All subsequent state transitions move the **entire folder**, preserving all accumulated work artifacts as the item progresses through the development pipeline.