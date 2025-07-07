# NimsforestWork - AI-Powered Work Management System

A self-contained work management system designed for developers and AI agents. Provides deterministic work item lifecycle management with Netflix-style deployment philosophy.

## Quick Start for Project Integration

### 1. Add to Any Project

```bash
# Option A: Add as git submodule (recommended)
git submodule add https://github.com/nimsforest/nimsforest.git/pkg/nimsforestwork pkg/nimsforestwork

# Option B: Copy the package directly  
cp -r /path/to/nimsforest/pkg/nimsforestwork pkg/

# Initialize the system
make -f pkg/nimsforestwork/MAKEFILE.nimsforestwork nimsforestwork-init

# Add to main Makefile (optional but recommended)
echo "-include pkg/nimsforestwork/MAKEFILE.nimsforestwork" >> Makefile
```

### 2. Available Commands

- `make nimsforestwork-hello` - Check system compatibility
- `make nimsforestwork-init` - Initialize work folder structure  
- `make nimsforestwork-newissue` - Create new work item interactively
- `make nimsforestwork-lint` - Validate all work items

## AI Agent Integration

### For Developers Using AI Tools

Simply tell your AI agent:

```
Please read pkg/nimsforestwork/AGENTINSTRUCTIONS.md and follow those instructions for all development work.
```

The AI agent will then automatically:
- Create work items before starting any development
- Move work items through proper workflow states  
- Follow the Netflix deployment model
- Document the "why" behind every change

### Supported AI Tools

This works with any AI coding assistant:
- **Claude Code** - `"Read pkg/nimsforestwork/AGENTINSTRUCTIONS.md and follow those instructions"`
- **Cursor** - Same instruction in system prompt
- **GitHub Copilot** - Include instruction in comments
- **Gemini CLI** - Pass instruction as context
- **Any AI tool** - The instructions are self-contained

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