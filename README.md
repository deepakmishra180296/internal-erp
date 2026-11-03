# Internal ERP

An internal ERP tool for our organisation, covering employee profiles, leave management and payroll.

> **Status: planning phase. Implementation has not started.**
> This repository currently holds planning documents only. There is no source code, build, or test setup yet.

## Modules

| Module | Purpose | Status |
| --- | --- | --- |
| Profile | Employee records, bank details, UAN, organisation details (client, POD, role) | Requirements, discovery, spec done |
| Leave | Leave requests, approvals, balances, holidays, audit history | Requirements, discovery, spec done; plan authored |
| Payroll | Monthly payroll run, payslips, claims, Indian tax/statutory rules | Requirements and discovery done; spec pending |

The modules are built separately but reuse each other's data instead of re-collecting it. Profile owns employee data, Leave feeds unpaid leave into Payroll, and Payroll reads from both.

## How we are working

We move through four stages for every module. Each stage produces a document that the next stage builds on.

1. **Requirements** – The client shares what they need for the module. These are the raw briefs, kept as received.
2. **Discovery** – We turn the briefs into a PRD-style document. It captures the why, the open questions and the decisions made, and is shareable with the client.
3. **Specs** – We break the discovery into numbered vertical slices with checkbox acceptance criteria. Slice 1 is a walking skeleton. The spec is the source of truth.
4. **Plan** – We write the execution plan: design structure, test strategy and the order of work for each slice. Implementation starts only after this.

If a spec, plan or workflow state changes, the others are updated to match.

## Repository layout

```
.
├── docs/
│   ├── requirements/         1. Client requirement briefs
│   ├── discovery/            2. Discovery documents (PRDs)
│   ├── specs/                3. Slice-based specs with acceptance criteria
│   └── plans/                4. Execution plans (not yet created)
└── .claude/                  Local workflow state (not committed)
```

### Documents

| Module | Requirements | Discovery | Spec |
| --- | --- | --- | --- |
| Profile | [requirements](docs/requirements/profile-module-requirements.md) | [discovery](docs/discovery/profile-module-discovery.md) | [spec](docs/specs/profile-module-spec.md) |
| Leave | [requirements](docs/requirements/leave-module-requirements.md) | [discovery](docs/discovery/leave-module-discovery.md) | [spec](docs/specs/leave-module-spec.md) |
| Payroll | [requirements](docs/requirements/payroll-module-requirements.md) | [discovery](docs/discovery/payroll-module-discovery.md) | pending |

## Getting started

There is nothing to install or run yet. This section will be filled in once code exists.
