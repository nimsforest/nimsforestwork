# Distributed Locking Through Git Branches

## Overview

The nimsforestwork system uses **git branch creation as a distributed locking mechanism** to coordinate work among thousands of concurrent developers without requiring central coordination. This document explains the principles, implementation, and benefits of this approach.

## Core Principle

**Branch names are globally unique** within a git repository. When multiple agents attempt to create the same branch name, only the first to successfully push to the remote repository claims the work. This creates a **distributed lock** that prevents concurrent work conflicts.

## Why Branch-Based Locking?

### Traditional Approaches Don't Scale

**File-based locking** (moving files to claim work):
- ❌ **Merge conflicts** when thousands try to move same file
- ❌ **Complex conflict resolution** requiring human intervention  
- ❌ **Race conditions** in concurrent file operations
- ❌ **Inconsistent state** during file system operations

**Central coordination systems**:
- ❌ **Single point of failure** in coordinator service
- ❌ **Network latency** impacts claiming speed
- ❌ **Complex infrastructure** requirements
- ❌ **Scaling bottlenecks** with high concurrency

### Branch-Based Locking Advantages

- ✅ **Atomic operations**: Git push either succeeds or fails cleanly
- ✅ **Zero infrastructure**: Uses existing git remote
- ✅ **Instant feedback**: Immediate success/failure on claim attempt
- ✅ **Self-healing**: Stale branches are detectable and recoverable
- ✅ **Perfect scaling**: Git handles thousands of concurrent pushes
- ✅ **Distributed by design**: No central coordinator needed

## Locking Mechanism

### Branch Naming Convention

**Pattern**: `{current-state}-{work-item-name}`

**Examples**:
- `stamp-lostmypassword` - Agent stamping work from `new/` to `stamped/`
- `triaging-lostmypassword-a1b2c3d4` - Agent categorizing stamped work
- `readyforanalysis-userauth-b5c6d7e8` - Agent analyzing requirements
- `indevelopment-userauth-b5c6d7e8` - Agent doing development work

### Claiming Process

```bash
# Step 1: Create local branch with specific name
git checkout -b readyforanalysis-userauth-b5c6d7e8

# Step 2: IMMEDIATELY push to claim on remote (THE LOCK)
git push -u origin readyforanalysis-userauth-b5c6d7e8
# ✅ SUCCESS = You claimed the work
# ❌ FAILURE = Someone else claimed it first (branch already exists)

# Step 3: Only if push succeeded, proceed with work
if [ $? -eq 0 ]; then
    # Move work item folder to next state  
    git mv docs/work/newfeatures/readyforanalysis/userauth-b5c6d7e8/ \
           docs/work/newfeatures/inanalysis/userauth-b5c6d7e8/
    git commit -m "work: start analysis - userauth-b5c6d7e8"
    git push
    
    # DO THE ACTUAL WORK HERE
    
    # When complete, prepare for next state
    git mv docs/work/newfeatures/inanalysis/userauth-b5c6d7e8/ \
           docs/work/newfeatures/readyfordevelopment/userauth-b5c6d7e8/
    git commit -m "work: ready for development - userauth-b5c6d7e8"
    git push
    
    # Clean up - delete the claiming branch
    git checkout main
    git branch -d readyforanalysis-userauth-b5c6d7e8
    git push origin --delete readyforanalysis-userauth-b5c6d7e8
else
    # Failed to claim - clean up and try next work item
    git checkout main
    git branch -d readyforanalysis-userauth-b5c6d7e8
    echo "Work already claimed by another agent, trying next item..."
fi
```

## Race Condition Handling

### Concurrent Claiming Scenario

```bash
# Agent A and Agent B both see: userauth-b5c6d7e8/ folder in readyforanalysis/
# Both create local branch: readyforanalysis-userauth-b5c6d7e8

# Agent A pushes first:
git push -u origin readyforanalysis-userauth-b5c6d7e8
# ✅ SUCCESS - Agent A owns the work

# Agent B pushes second:
git push -u origin readyforanalysis-userauth-b5c6d7e8  
# ❌ FAILURE - "remote ref already exists"
# Agent B immediately tries next available work item
```

### Failure Recovery

**Immediate Recovery**:
```bash
# When claim fails, immediately clean up and move on
if ! git push -u origin readyforanalysis-userauth-b5c6d7e8; then
    git checkout main
    git branch -d readyforanalysis-userauth-b5c6d7e8
    # Try claiming next available work item (look for folders)
    find docs/work/*/readyfor* -maxdepth 1 -type d | head -1
fi
```

## Stale Branch Detection and Recovery

### Detecting Abandoned Work

```bash
# Find all claiming branches
git branch -r | grep 'origin/.*-.*-[a-f0-9]\{8\}'

# Check if branch is behind main (indicates potential failure)
for branch in $(git branch -r | grep 'origin/readyfor\|origin/indevelopment'); do
    commits_behind=$(git log --oneline main..$branch 2>/dev/null | wc -l)
    if [ $commits_behind -lt 0 ]; then
        echo "Stale branch detected: $branch"
    fi
done
```

### Recovery Mechanisms

**Automatic Recovery** (by monitoring agents):
```bash
# If branch is stale (created >24h ago, no recent commits)
STALE_BRANCH="readyforanalysis-userauth-b5c6d7e8"
CREATED_TIME=$(git log --format="%ct" -1 origin/$STALE_BRANCH)
CURRENT_TIME=$(date +%s)
AGE=$((CURRENT_TIME - CREATED_TIME))

if [ $AGE -gt 86400 ]; then  # 24 hours
    echo "Branch $STALE_BRANCH is stale, cleaning up"
    git push origin --delete $STALE_BRANCH
    # Work item automatically becomes available for new claims
fi
```

## State Transition Mapping

### Complete Development Workflow

| Current State | Claiming Branch | Next State | Work Type |
|---------------|----------------|------------|-----------|
| `new/` | `stamp-{item}` | `stamped/` | Add UUID |
| `stamped/` | `triaging-{item}` | `{category}/next/` | Categorize |
| `next/` | `readyforanalysis-{item}` | `inanalysis/` | Requirements |
| `readyforanalysis/` | `inanalysis-{item}` | `readyfordevelopment/` | Analysis |
| `readyfordevelopment/` | `indevelopment-{item}` | `readyfortesting/` | Development |
| `readyfortesting/` | `intesting-{item}` | `signedoff/` | Testing |
| `signedoff/` | `production-{item}` | `archive/production/` | Deploy |

### Branch Lifecycle

1. **Create**: `git checkout -b {state}-{item}`
2. **Claim**: `git push -u origin {state}-{item}` (THE LOCK)
3. **Work**: Move files, do actual work, commit progress
4. **Complete**: Move to next state, commit completion
5. **Cleanup**: Delete branch when state transition complete

## Scalability Analysis

### Performance Characteristics

**Git's distributed design** handles enterprise-scale concurrent operations:
- **Thousands of simultaneous pushes**: Git servers handle this routinely
- **Instant conflict detection**: Branch existence check is O(1)
- **No network chattering**: Single push operation determines success
- **Automatic load balancing**: Failed agents immediately try other work

### Real-World Scale Examples

- **Linux Kernel**: Hundreds of concurrent developers, hierarchical branching
- **Android/AOSP**: Thousands of developers, Gerrit-based workflow
- **GitHub**: Millions of repositories, branch-based collaboration
- **Enterprise Git**: Azure DevOps supports 1000+ projects with branch policies

### Theoretical Limits

- **Branch name collision**: Practically impossible with UUID-based naming
- **Git remote capacity**: Modern Git servers handle 100,000+ branches
- **Concurrent push limit**: Limited by network/server capacity, not Git protocol
- **Recovery time**: Proportional to number of stale branches (linear cleanup)

## Best Practices

### Agent Implementation

1. **Fast claiming**: Push immediately after branch creation
2. **Quick failure**: Clean up and move on if claim fails  
3. **Regular cleanup**: Delete branches after state transitions
4. **Monitoring**: Track stale branches for recovery
5. **FIFO respect**: Process oldest available work first

### Branch Naming

1. **Consistent format**: Always `{state}-{item-name}`
2. **Unique identifiers**: Use UUID-stamped item names
3. **Descriptive states**: Match folder names exactly
4. **No special characters**: Git-safe branch names only

### Error Handling

1. **Expect failures**: Claiming often fails in high-concurrency
2. **Immediate retry**: Try next available work without delay
3. **No persistent state**: Don't track failed claims locally
4. **Clean recovery**: Always clean up local branches on failure

## Security Considerations

### Access Control

- **Repository permissions**: Standard Git access controls apply
- **Branch protection**: Can protect specific patterns if needed
- **Audit trail**: All claims and work tracked in Git history
- **Identity**: Git commits identify who did what work

### Abuse Prevention

- **Rate limiting**: Git servers can limit push frequency per user
- **Branch quotas**: Can limit number of active branches per user
- **Monitoring**: Unusual branching patterns are easily detected
- **Recovery**: System self-heals from most abuse scenarios

## Integration with CI/CD

### Automated Testing

```bash
# Test branches can trigger builds
on:
  push:
    branches:
      - 'indevelopment-*'  # Trigger tests for development work
      - 'intesting-*'      # Trigger full test suite
```

### Deployment Gates

```bash
# Only allow promotion after tests pass
if tests_passed; then
    git mv docs/work/features/intesting/item.md docs/work/features/signedoff/
    git push origin --delete intesting-item  # Release lock
fi
```

## Monitoring and Observability

### Key Metrics

- **Active claims**: Number of `{state}-*` branches
- **Claim success rate**: Successful pushes / total attempts  
- **Stale branch count**: Branches older than threshold
- **Throughput**: Work items processed per time unit
- **Contention**: Failed claims per successful claim

### Alerting

- **Stale branch buildup**: Too many abandoned claims
- **Low success rate**: High contention on popular work
- **Processing delays**: Work items stuck in states too long

This distributed locking mechanism provides the foundation for coordinating thousands of concurrent developers without any central infrastructure, making it ideal for large-scale, distributed software development.