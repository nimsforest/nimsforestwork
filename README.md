# NimsforestWork - Nim-Based Work Management System

[![Version](https://img.shields.io/badge/version-0.1.0-blue.svg)](#)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Platform](https://img.shields.io/badge/platform-Linux%20|%20macOS%20|%20Windows-lightgrey.svg)](#)
[![Dependencies](https://img.shields.io/badge/dependencies-git%20|%20make-green.svg)](#)
[![Tests](https://github.com/nimsforest/nimsforestwork/workflows/Test%20NimsforestWork/badge.svg)](https://github.com/nimsforest/nimsforestwork/actions)

A work management system designed for **Nim-based development** - where Nims (entities with agency) collaborate across distributed teams. Essential for environments where humans, AI agents, robots, and drones work together on complex tasks.

## 🤝 The Nim Way of Working

**What is a Nim?** An entity with agency, having MECE (Mutually Exclusive, Collectively Exhaustive) roles and responsibilities within an organization - regardless of whether executed by humans, AI agents, robots, or drones.

### Why Git Worktrees for Distributed Nims?

In distributed teams, a single task often requires:
- **Human Nims**: Strategic decisions, approvals, complex problem-solving
- **AI Agent Nims**: Code generation, documentation, testing automation  
- **Robot/Drone Nims**: Physical deployment, hardware integration
- **Mixed collaboration**: Some parts automated, others requiring human oversight

**The Challenge**: These different Nims work at different speeds and may take days or weeks to complete their parts of a task before it can merge to main.

**The Solution**: Git worktrees create isolated workspaces where multiple Nims can collaborate on long-running features without blocking each other or the main branch.

## 🚀 Quick Setup: Transform Any Repo into a Git Worktree Workspace

### Step-by-Step Example with "justanothertodoapp"

Let's say you have a project called `justanothertodoapp`:

```bash
# 1. You're currently in your project directory
cd /home/user/justanothertodoapp
pwd
# Output: /home/user/justanothertodoapp

# 2. Add nimsforestwork to your project
git clone https://github.com/nimsforest/nimsforestwork.git tools/nimsforestwork

# 3. Set up the workspace (this moves your repo!)
WORKSPACE_ROOT=/home/user/justanothertodoapp-workspace make -f tools/nimsforestwork/MAKEFILE.nimsforestwork nimsforestwork-setup-workspace

# 4. Your project is now organized as a workspace
cd /home/user/justanothertodoapp-workspace
```

### What Just Happened?

**Before:**
```
/home/user/justanothertodoapp/     # Your repo
├── src/
├── README.md
└── .git/
```

**After:**
```
/home/user/justanothertodoapp-workspace/     # New workspace
├── main/                                    # Your repo moved here
│   ├── src/
│   ├── README.md
│   └── .git/
├── tools/nimsforestwork/                    # Work management
├── work/                                    # Work items
├── AGENTINSTRUCTIONS.md                     # AI agent setup
└── Makefile                                 # Workspace commands
```

### 🌳 Ready for Distributed Nim Collaboration

```bash
# Create feature branches as separate directories for different Nims
cd /home/user/justanothertodoapp-workspace/main
git worktree add ../feature-auth feature-auth        # New branch for feature work
git worktree add ../bug-fix-login origin/bug-fix     # Existing branch for bug fixes

# Your workspace now supports parallel Nim work:
# justanothertodoapp-workspace/
# ├── main/              # main branch (stable)
# ├── feature-auth/      # Human Nim: UX design + AI Nim: implementation
# ├── bug-fix-login/     # AI Nim: automated testing + Human Nim: verification
# ├── work/              # shared work management across all Nims
# └── tools/             # shared tools
```

### 🎯 Real-World Nim Workflow Example

**Scenario**: Adding user authentication feature

1. **Issue Creation** (Any Nim):
   ```bash
   make nimsforestwork-newissue TITLE=userauthentication MESSAGE="Add OAuth and 2FA support"
   ```

2. **Feature Branch Setup** (Human Nim):
   ```bash
   cd main && git worktree add ../feature-auth feature-auth
   git mv docs/work/issues/new/userauthentication.md docs/work/newfeatures/next/
   ```

3. **Parallel Development** (Multiple Nims):
   - **AI Agent Nim** in `feature-auth/`: Generates OAuth integration code
   - **Human Nim** remotely: Reviews security implications, defines UX flows
   - **Another AI Agent** in `feature-auth/`: Writes comprehensive tests
   - **Human Nim** later: Final security audit and approval

4. **Long-Running Collaboration**: The feature branch can exist for weeks while different Nims contribute at their own pace, without blocking main branch development.

5. **Integration**: Only when ALL Nims complete their parts does the feature merge to main and go to production.

### 💡 Why This Matters for Distributed Teams

- **Asynchronous Collaboration**: Nims work when available, not in lockstep
- **Isolation**: No blocking or conflicts between different Nim workstreams  
- **Context Preservation**: Each feature branch maintains its complete development context
- **Quality Gates**: Features only merge when truly complete
- **Local Development**: Even distributed teams benefit from local worktree organization

## 📝 WORKSPACE_ROOT Explained

**WORKSPACE_ROOT** = Where you want the new workspace folder created

### Examples:

| Your Project | WORKSPACE_ROOT | Result |
|--------------|----------------|--------|
| `/home/user/myapp` | `/home/user/myapp-workspace` | Workspace at `/home/user/myapp-workspace/` |
| `/code/backend-api` | `/code/backend-api-workspace` | Workspace at `/code/backend-api-workspace/` |
| `C:\dev\frontend` | `C:\dev\frontend-workspace` | Workspace at `C:\dev\frontend-workspace\` |

### Convention:
- **Project**: `{name}`
- **Workspace**: `{name}-workspace` (recommended)

## 📋 Workflow Documentation

NimsforestWork implements a comprehensive issue management and product development workflow that supports the complete lifecycle from customer support to production delivery.

**📖 [Read the complete workflow documentation](WORKFLOW.md)**

Key features:
- **Issue Intake**: Systematic processing of customer support tickets
- **Product Backlog**: Complete lifecycle from analysis to production
- **Quality Gates**: Ensure quality while maintaining agility
- **State Management**: Clear progression through development stages
- **Business Rating**: Priority-based work management
- **Release Management**: Structured release process

The workflow supports bugs, change requests, new features, and documentation improvements through a unified process.

## Alternative Setup Methods

### 1. Traditional Project Integration (No Workspace)

```bash
# Add nimsforestwork as a submodule in tools/nimsforestwork
git submodule add https://github.com/nimsforest/nimsforestwork.git tools/nimsforestwork

# Navigate to the submodule directory
cd tools/nimsforestwork

# Run initial setup
make nimsforestwork-hello

# Return to project root and initialize
cd ../../
make nimsforestwork-init
```

### 2. Available Commands

- `make nimsforestwork-hello` - Check system compatibility
- `make nimsforestwork-init` - Initialize work folder structure  
- `make nimsforestwork-newissue` - Create new work item interactively
- `make nimsforestwork-lint` - Validate all work items

## Nim Integration

### For Human Nims Working with AI Agent Nims

Simply tell your AI agent:

```
Please read NIMINSTRUCTIONS.md and follow those instructions for all development work.
```

The AI agent Nim will then automatically:
- Create work items before starting any development
- Move work items through proper workflow states using git mv commands
- Follow the enterprise development lifecycle
- Document the "why" behind every change

### Supported AI Agent Nims

This works with any AI coding assistant:
- **Claude Code** - `"Read NIMINSTRUCTIONS.md and follow those instructions"`
- **Cursor** - Same instruction in system prompt
- **GitHub Copilot** - Include instruction in comments
- **Gemini CLI** - Pass instruction as context
- **Any AI tool** - The instructions are self-contained

### Human Nim Workflow

Human Nims can also follow the same process:
1. Read NIMINSTRUCTIONS.md for complete workflow guidance
2. Use git mv commands to move work items between states
3. Collaborate with AI Agent Nims using shared work items

## Work Item System

### Work Types

- **changerequests/** - Modify existing functionality
- **bugs/** - Fix broken functionality (has `pendingfeedback/` state)
- **proposals/** - Add new functionality  
- **improvedocumentation/** - Improve documentation

### Workflow States

1. **new/** - Newly created items
2. **planned/** - Reviewed and accepted for implementation
3. **active/** - Currently being worked on
4. **pendingfeedback/** - Blocked waiting for clarification (bugs/changerequests only)
5. **ready/** - Implementation complete, ready for merge
6. **archive/** - Completed items

### Creating Work Items

```bash
# Interactive creation (recommended)
make nimsforestwork-newissue

# Manual creation
cp docs/work/changerequests/template.md docs/work/changerequests/new/myfeature.md
# Edit the file, then commit
git add docs/work/changerequests/new/myfeature.md
git commit -m "work: add my feature request"
```

### Moving Through Workflow

```bash
# Plan the work
git mv docs/work/changerequests/new/myfeature.md docs/work/changerequests/planned/
git commit -m "work: plan my feature implementation"

# Start working  
git mv docs/work/changerequests/planned/myfeature.md docs/work/changerequests/active/
git commit -m "work: start my feature implementation"

# Complete work
git mv docs/work/changerequests/active/myfeature.md docs/work/changerequests/ready/
git commit -m "work: my feature ready for review"

# After merge to main
git mv docs/work/changerequests/ready/myfeature.md docs/work/changerequests/archive/
git commit -m "work: completed my feature"
```

## Netflix Deployment Model

- **Every merge to main goes directly to production**
- No human gatekeepers blocking releases
- Comprehensive automated testing ensures quality
- Work items document the "why" behind every change
- Automated rollback on failures

## Features

- ✅ **AI Agent Friendly** - Clear instructions for all major AI tools
- ✅ **Cross-project Compatible** - Works in any git repository  
- ✅ **Self-contained** - No external dependencies beyond git
- ✅ **Interactive Work Creation** - `make nimsforestwork-newissue`
- ✅ **Documentation Integration** - Work items visible in docs visualizer
- ✅ **Template-based** - Consistent work item structure
- ✅ **Validation** - Automated work item validation

## Team Onboarding

### For New Team Members

```bash
# Clone and setup
git clone <your-repo>
git submodule update --init --recursive
make nimsforestwork-hello

# Start working
make nimsforestwork-newissue
```

### For AI-Assisted Development

```bash
# Setup nimsforestwork in your project
make nimsforestwork-init

# Tell your AI agent
"Read pkg/nimsforestwork/AGENTINSTRUCTIONS.md and follow those instructions"

# Your AI will now automatically create work items and follow the workflow
```

## Advanced Usage

### Custom Project Paths

```bash
# Customize work directory location if needed
sed -i 's/docs\/work/custom\/path/g' pkg/nimsforestwork/MAKEFILE.nimsforestwork
```

### CI/CD Integration

```yaml
# .github/workflows/validate-work.yml
name: Validate Work Items
on: [push, pull_request]
jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - run: make nimsforestwork-lint
```

### Integration with Existing Projects

The system integrates seamlessly with any existing project:
- No modification of existing code required
- Work items stored in `docs/work/` for visibility
- Optional Makefile integration
- Compatible with any git workflow

## Why NimsforestWork?

### For Developers
- **Transparent workflow** - See all work in docs visualizer
- **AI assistance** - Let AI agents handle workflow automatically  
- **No overhead** - Simple file-based system
- **Git native** - Uses familiar git commands

### For Teams
- **Netflix deployment** - Fast, automated releases
- **Documentation first** - Every change has a "why"
- **Cross-project** - Same workflow everywhere
- **Onboarding friendly** - New members get productive fast

### For AI Agents
- **Clear instructions** - AGENTINSTRUCTIONS.md provides complete guidance
- **Deterministic workflow** - No ambiguity about next steps
- **Self-validating** - Built-in quality checks
- **Context aware** - Work items provide context for decisions

## Files and Structure

```
pkg/nimsforestwork/
├── README.md                    # This file (human-focused)
├── AGENTINSTRUCTIONS.md         # AI agent instructions
├── MAKEFILE.nimsforestwork      # All commands
└── templates/                   # Work item templates
    ├── proposal.md
    ├── bug.md  
    ├── changerequest.md
    └── improvedocumentation.md
```

When initialized, creates:
```
docs/work/
├── README.md
├── proposals/{new,planned,active,ready,archive}/
├── bugs/{new,planned,active,pendingfeedback,ready,archive}/
├── changerequests/{new,planned,active,pendingfeedback,ready,archive}/
└── improvedocumentation/{new,planned,active,ready,archive}/
```

## Getting Started

1. **Add to your project**: Copy or submodule pkg/nimsforestwork
2. **Initialize**: `make nimsforestwork-init`  
3. **Create work item**: `make nimsforestwork-newissue`
4. **Tell your AI**: `"Read pkg/nimsforestwork/AGENTINSTRUCTIONS.md"`
5. **Start developing** with full workflow automation

Perfect for teams that want to move fast with AI assistance while maintaining clear documentation and process.