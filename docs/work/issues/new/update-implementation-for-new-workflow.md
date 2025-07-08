# Update Implementation for New Workflow Structure

## Issue Description

The WORKFLOW.md documentation has been created with a comprehensive process that maps customer support through production delivery. The current implementation needs to be updated to support the new folder structure and workflow states.

## Current State

- ✅ WORKFLOW.md created with complete process documentation
- ✅ README.md updated with workflow references
- ❌ AGENTINSTRUCTIONS.md still references old folder structure
- ❌ MAKEFILE.nimsforestwork creates old folder structure
- ❌ Templates don't match new workflow states

## Required Changes

### 1. Update AGENTINSTRUCTIONS.md

Current structure references:
- `docs/work/proposals/` → Should be `work/newfeatures/`
- `docs/work/bugs/` → Should be `work/bugs/`
- Old states (new/planned/active/ready) → New states (next/readyforanalysis/inanalysis/etc.)

Need to add:
- Complete new folder structure
- Issue intake process (issues/new/ → issues/triaging/ → categorization)
- New state progression for each category
- Business rating and complexity concepts
- Exception handling (pendingfeedback flows)

### 2. Update MAKEFILE.nimsforestwork

Current `nimsforestwork-init` creates:
```
docs/work/proposals/{new,planned,active,ready,archive}
docs/work/bugs/{new,planned,active,pendingfeedback,ready,archive}
docs/work/changerequests/{new,planned,active,pendingfeedback,ready,archive}
docs/work/improvedocumentation/{new,planned,active,ready,archive}
```

Should create:
```
work/issues/{new,triaging,pending}
work/bugs/{next,readyforanalysis,inanalysis,readyfordevelopment,indevelopment,readyfortesting,intesting,signedoff,pendingfeedback}
work/changerequests/{next,readyforanalysis,inanalysis,readyfordevelopment,indevelopment,readyfortesting,intesting,signedoff,pendingfeedback}
work/newfeatures/{next,readyforanalysis,inanalysis,readyfordevelopment,indevelopment,readyfortesting,intesting,signedoff}
work/improvedocumentation/{next,readyforanalysis,inanalysis,readyfordevelopment,indevelopment,readyfortesting,intesting,signedoff}
work/archive/{production,rejected}
```

Also update:
- `nimsforestwork-setup-workspace` to create new structure
- Path references from `docs/work/` to `work/`
- Template copying to match new structure

### 3. Update and Create Templates

Current templates:
- `bug.md` → Update with new workflow states
- `changerequest.md` → Update with new workflow states  
- `improvedocumentation.md` → Update with new workflow states
- `proposal.md` → Rename to `newfeature.md` and update

New template needed:
- `issue.md` → For generic issue intake

Template updates needed:
- Add Business Rating field (1-5)
- Add Complexity Assessment field (Simple/Normal/Complex/Madness)
- Add workflow state progression guidance
- Update state references to match new folder names
- Add platform information field (for bugs)
- Add steps to reproduce, expected result, actual result (for bugs)

### 4. Update Commands

Need new/updated commands:
- `nimsforestwork-newissue` → Should create in `work/issues/new/`
- `nimsforestwork-start-triage` → Move issue from new to triaging
- `nimsforestwork-promote-to-X` → Move from issues to appropriate category
- Update existing commands to work with new structure

## Expected Outcome

After completion:
- ✅ Agents can follow new workflow documented in WORKFLOW.md
- ✅ Folder structure matches enterprise process exactly
- ✅ All states have dedicated folders (no combining)
- ✅ Templates guide users through new process
- ✅ Commands support complete workflow
- ✅ Test suite validates new structure

## Priority

High - Required to make the documented workflow functional

## Complexity

Complex - Multiple files need coordinated updates

## Business Rating

4 (High) - Essential for tool functionality

## Platform

All platforms - Cross-platform implementation