# Plan as Code — Layout and Conventions

This repository uses the Plan as Code (PaC) protocol for durable, Git-native implementation planning. The PaC specification (`SPEC.md`) is the normative authority; always follow the latest available version of the spec.

## Layout

```text
.plan/
├── README.md            (this file)
├── plans/               (canonical plan records)
│   └── P001.md
└── feedback/            (append-only feedback threads)
```

## Conventions

- Plan IDs: `P###` (e.g. `P001`), allocated sequentially.
- Task IDs: `P###-T###` (e.g. `P001-T001`).
- Acceptance criteria: `AC-P###-T###-NN`.
- Findings: `P###-T###-F###`.
- Feedback items: `P###-T###-FB###` (or `P###-T###-F###-FB###`).
- Iterations preserve stable IDs; wording changes do not mint new IDs.

## Roles

- Planner owns `.plan/plans/**`.
- Implementer owns implementation artifacts.
- Verifier owns verification outcomes.
