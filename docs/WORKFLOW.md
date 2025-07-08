# NimsforestWork Workflow Documentation

A practical but thorough guide on how to manage a software product and development team. The focus is to be as agile as possible while still ensuring quality. We leverage from several agile methodologies like Kanban and Scrum.

This document covers all aspects from input (new things to make) to production (how to make quality items while staying nimble) to output (releasing new quality versions).

## Overview

NimsforestWork implements a comprehensive issue management and product development workflow that supports the complete lifecycle from initial customer contact to production delivery.

## Work Structure

```
work/
├── issues/                      # Customer Support System intake
│   ├── new/                     # New tickets from support system
│   ├── triaging/                # Support team processing (Open)
│   └── pending/                 # Pending (waiting for user info)
├── bugs/                        # Product Backlog - Bug items
│   ├── next/                    # Next (waiting for business rating assignment)
│   ├── readyforanalysis/        # Ready for Analysis
│   ├── inanalysis/              # In Analysis
│   ├── readyfordevelopment/     # Ready for Development
│   ├── indevelopment/           # In Development
│   ├── readyfortesting/         # Ready for Testing
│   ├── intesting/               # In Testing
│   ├── signedoff/               # Signed off
│   └── pendingfeedback/         # Needs more info from user
├── changerequests/              # Product Backlog - Change requests
│   ├── next/
│   ├── readyforanalysis/
│   ├── inanalysis/
│   ├── readyfordevelopment/
│   ├── indevelopment/
│   ├── readyfortesting/
│   ├── intesting/
│   ├── signedoff/
│   └── pendingfeedback/
├── newfeatures/                 # Product Backlog - New features
│   ├── next/
│   ├── readyforanalysis/
│   ├── inanalysis/
│   ├── readyfordevelopment/
│   ├── indevelopment/
│   ├── readyfortesting/
│   ├── intesting/
│   └── signedoff/
├── improvedocumentation/        # Documentation improvements
│   ├── next/
│   ├── readyforanalysis/
│   ├── inanalysis/
│   ├── readyfordevelopment/
│   ├── indevelopment/
│   ├── readyfortesting/
│   ├── intesting/
│   └── signedoff/
└── archive/
    ├── production/              # Production (final delivery)
    └── rejected/                # Rejected items
```

## Input Phase: New Things to Make

All customer communication flows through a Customer Support System. Issues are categorized and processed through a systematic workflow.

### Customer Creates a Bug Ticket

**New**: Customer reports through support system. Ticket moves to status Open and receives priority and type 'Bug'. An existing feature is not behaving as expected.

**Triaging (Open)**: Support team investigates and re-assesses priority. Can lead to:
- **Pending**: Support needs more information to reproduce the bug
- **Categorization**: Support can reproduce the bug and documents steps to test, expected result, and actual result in collaboration with test team

**Pending**: Support has provided information to user and waits to understand if the provided information resolves the issue.

**Categorized**: Support team creates Product Backlog Item in bugs/next/. Once Product Team assesses the item, it receives a Business Rating. Support team updates priority accordingly (5: Urgent, 4: High, 3/2: Normal, 1: Low).

**Resolved**: Bug is fixed and released to production, OR Support team unable to reproduce bug. Support team informs reporting user. If bug is fixed, Product team mentions fix in release notes.

### Customer Creates a Change Request Ticket

**New**: Customer reports through support system. Ticket moves to status Open and receives priority and type 'Change Request'. Customer wants behavior added or changed in application.

**Triaging (Open)**: Support team investigates and re-assesses priority. Can lead to:
- **Pending**: Support needs more information to understand the change
- **Categorization**: Support understands request and forwards to Product team

**Pending**: More information required from user to understand the change.

**Categorized**: Support creates item in changerequests/next/ with clear description. If accepted, support provides updates to user.

**Resolved**: Feature is delivered and released to production, OR Product team refuses change request, OR Support team can't understand/clarify request. Support team informs reporting user. If delivered, Product team mentions feature in release notes.

### Customer Creates a General Issue Ticket

**New**: Customer reports through support system. Ticket moves to status Open and receives priority and type 'Issue'. Existing feature behaving as expected but customer needs explanation, OR unexpected behavior but unclear if it's a bug.

**Triaging (Open)**: Support team investigates and re-assesses priority. Can lead to:
- **Pending**: Support needs more information from user
- **Resolution**: Support provides knowledge base article or dedicated answer
- **Reclassification**: Technical investigation required - becomes Bug or Change Request

**Pending**: More information required from user to reproduce issue.

**Resolved**: User overcomes issue with support help. If existing knowledge base insufficient, support creates knowledge article.

### Team Member Finds Bug Randomly

Team member creates item directly in bugs/next/ following Product Backlog Lifecycle for Bugs.

## Production Phase: Making Quality Items While Staying Nimble

### Tools Integration
- **Project Management System**: Product Backlog and Team Management
- **Version Control System**: Source code management
- **Testing Framework**: Quality assurance

### Product Backlog Lifecycle

This describes the general Product Backlog Lifecycle. Bugs follow a slightly different process.

**Next**: Items have gone through process ensuring Product team can make priority assessment. If information missing, creator contacted and item stays in 'Next'. Priority assessment process called 'Grooming the Product Backlog'.

**Ready for Analysis**: Item received priority assessment by Product Team. Development team brings it from this state to 'In Analysis'.

**In Analysis**: Development team defines necessary Art/Tech/Web tasks to deliver item. They can add 'Chore' type items linked to original item. Team considers dependencies and developer skill requirements. Item receives complexity assessment (Simple/Normal/Complex/Madness). Status changes to 'Ready for Development'.

**Ready for Development**: Development team understands requirements. Status changes to 'In Development' when developer starts. Developer creates version control branch with item number. Priority within same business rating: Simple > Normal > Complex > Madness.

**In Development**: Developer works on tasks according to skill-set (Art/Tech/Web). If different skill-set required, moves back to 'Ready for Development'. When all tasks complete, moves to 'Ready for Testing'. Requirements: Steps to Test and Expected Result documented, Expected Result achievable.

**Ready for Testing**: All development tasks done. Product team decides release schedule. All related items must be 'Ready for Testing'.

**In Testing**: Product team selects for release. Release team merges to release branch. Test team adds test case based on 'Steps to Test' and 'Expected Result'. Development build created and tested. If Steps to Test don't achieve Expected Result, item returns to 'Ready for Analysis' as Bug.

**Signed Off**: Test case succeeded in development build. Item included in test release. Next status is 'Production'.

**Production**: Release passed both test and production release testing. Final status for Product Backlog item.

### Product Backlog Lifecycle for Bugs

**Next**: Bug created with status 'Next' and type 'Bug'. Creator documents steps to reproduce, expected result, actual result, and platform. Moves to 'Ready for Analysis' when business rating assigned. Non-reproducible bugs: Product team decides reject or allow developer 1+ hour investigation based on business rating.

**Ready for Analysis**: Development team can analyze. Moves to 'In Analysis' when investigation starts. BR 5 bugs: Product team informs development to pause current work.

**In Analysis**: Developer reproduces steps and checks actual result. If actual result not achieved: developer stops, informs creator for reproduction help. If not achievable within 1 hour: bug returns to 'Next' for Product team decision. If actual result achieved:
- BR 5/4 Bug: Moves to 'Ready for Testing' (immediate investigation and fixing)
- BR 3/2/1 Bug: Moves to 'Ready for Development' (approach documented) or 'Ready for Testing' (solved during analysis)

**Ready for Development**: Technical investigation/fixing can proceed. Moves to 'In Development' when work starts. Priority: Simple > Normal > Complex > Madness within same business rating.

**In Development**: Bug fix developed. Can be misunderstanding requiring knowledge article (developer works with support team). Goes to 'Ready for Testing' with updated 'Steps to Test'. Expected Result unchanged.

**Ready for Testing**: Developer ensures Expected Result achieved through Steps to Test. Bug fix immediately merged to development branch. Scheduled for release by Product team. Expediting based on business rating:
- BR 5: Dedicated release
- BR 4: Added to existing scheduled release or new release created
- BR 3/2/1: Normal release scheduling

Further statuses same as general Product Backlog lifecycle.

### Feature Request Backlog Lifecycle

**Next**: Product team assesses if feature request should be accepted. Changes status to 'Pending Feedback', 'Accepted', or 'Rejected'.

**Pending Feedback**: More information required. Product team contacts reporter.

**Accepted**: Create item in newfeatures/next/ with user story description. Product team informs reporter. Final status for Feature Request.

**Rejected**: Product team informs reporter. Final status for Feature Request.

## State Transitions

### Standard Flow
1. **Intake**: issues/new/ → issues/triaging/ → categorization
2. **Development**: next/ → readyforanalysis/ → inanalysis/ → readyfordevelopment/ → indevelopment/ → readyfortesting/ → intesting/ → signedoff/
3. **Completion**: archive/production/ or archive/rejected/

### Exception Flows
- **Pending Information**: Any state → pendingfeedback/ → return to appropriate state
- **Rework Required**: intesting/ → readyforanalysis/ (as bug)
- **Cancellation**: Any state → archive/rejected/

## Quality Gates

Each state transition requires specific criteria:
- **Analysis**: Clear understanding of requirements
- **Development**: Documented approach and complexity assessment  
- **Testing**: Steps to Test and Expected Result documented
- **Production**: All tests passed and release approved

## Business Rating Priority

Priority system for work items:
- **5 (Urgent)**: Immediate attention, dedicated releases
- **4 (High)**: Fast-track to existing or new releases  
- **3/2 (Normal)**: Standard release scheduling
- **1 (Low)**: Opportunistic inclusion

## Complexity Assessment

Development complexity scale:
- **Simple**: Straightforward implementation
- **Normal**: Standard complexity
- **Complex**: Requires significant effort
- **Madness**: Extremely complex, requires careful planning

Work priority within same business rating: Simple > Normal > Complex > Madness

## Release Management

### Release Types
- **Dedicated Release**: Critical bugs (BR 5)
- **Scheduled Release**: Planned feature releases
- **Opportunistic Release**: Low-priority items added when ready

### Release Process
1. Items selected for release → intesting/
2. Development build created and tested
3. Test release created if development tests pass
4. Production release after test release validation
5. Items move to archive/production/

## Documentation Requirements

### Work Items Must Include
- Clear description of issue/requirement
- Steps to reproduce (bugs) or acceptance criteria
- Expected results
- Actual results (bugs)
- Platform information (bugs)
- Business rating and complexity assessment

### Knowledge Management
- Support team maintains knowledge base
- Failed reproductions documented
- Solutions shared across team
- Release notes updated for customer-facing changes

This workflow ensures systematic handling of all work types while maintaining quality and agility throughout the development process.