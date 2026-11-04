# Team Roles and RACI Matrix

## 1. Team Information

| Field | Value |
|---|---|
| Course | SENG 321 – Software Engineering |
| Semester | Fall 2026 |
| Team Number | `Team <XX>` |
| Project Name | Animal Shelter & Adoption System |
| Project Type | Web Application |
| Repository | https://github.com/seng321-termproject-pawradise/pawradise |
| Project Board | `https://github.com/orgs/seng321-termproject-pawradise/projects/<n>` |
| Requirements Source | Official Requirements Reference (FR-01 … FR-21, NFR-01 … NFR-09, BR-01 … BR-09) |

## 2. Team Members and Roles

Every member uses an individual GitHub account, so that contributions are traceable.

| # | Name | GitHub Username | Role | Abbreviation |
|---|---|---|---|---|
| 1 | İlayda `<Surname>` | `@<username>` | Product Owner and Requirements Engineer | PO |
| 2 | Sıla `<Surname>` | @sevvalsilacelik | Scrum Master and Agile Process Coach | SM |
| 3 | Işıl `<Surname>` | @gltknisil | Lead Software Architect and Technical Lead | ARCH |
| 4 | Efekan `<Surname>` | `@<username>` | Quality Assurance and Test Automation Engineer | QA |
| 5 | İlay `<Surname>` | @aslnurilay | DevOps and CI/CD Infrastructure Engineer | DEVOPS |
| 6 | Fatma `<Surname>` | `@<username>` | Implementation and Integration Engineer | IMPL |

## 3. Roles and Responsibilities

The responsibilities cover the six areas required for Sprint 1: **product, process, architecture, quality, DevOps, and implementation**.

### 3.1 Product Owner and Requirements Engineer (PO) – Product

- Owns the product vision and the official requirements reference (FR, NFR, BR).
- Prepares the Project Proposal and the System Vision and Boundary.
- Writes epics and user stories with acceptance criteria, estimates, dependencies, and owners.
- Maintains and prioritizes the product backlog.
- Ensures traceability between requirements, backlog items, API endpoints, and the implemented product.
- Validates deliverables against stakeholder needs.

### 3.2 Scrum Master and Agile Process Coach (SM) – Process

- Facilitates sprint planning, stand-ups, and retrospectives, and records meeting minutes in `docs/meetings/`.
- Maintains the RACI matrix and the contribution guidelines (`CONTRIBUTING.md`).
- Administers the GitHub Projects board and tracks team progress.
- Tracks and removes impediments in `docs/sprint-1-tracking.md`.
- Monitors the individual contribution requirement (2 pull requests authored, 2 reviewed per member per sprint).
- Prepares sprint releases and submission documents.

### 3.3 Lead Software Architect and Technical Lead (ARCH) – Architecture

- Defines the system and software architecture (C4 model, UML diagrams).
- Leads the API-first design and owns the OpenAPI specification.
- Makes and documents architectural and technology decisions.
- Leads code reviews and maintains technical consistency.

### 3.4 Quality Assurance and Test Automation Engineer (QA) – Quality

- Prepares test plans and the test strategy.
- Reviews acceptance criteria and the OpenAPI contract for testability.
- Develops unit, integration, and end-to-end tests.
- Tracks defects and monitors unit-test coverage (minimum 50 percent).

### 3.5 DevOps and CI/CD Infrastructure Engineer (DEVOPS) – DevOps

- Sets up and maintains the GitHub organization, repository settings, and branch protection.
- Builds the GitHub Actions continuous integration and continuous delivery pipeline.
- Prepares the Docker and docker-compose configuration so that the system starts with one command.
- Manages deployment and environment configuration.

### 3.6 Implementation and Integration Engineer (IMPL) – Implementation

- Implements frontend and backend features assigned through issues.
- Integrates components across the API and the user interface.
- Resolves integration defects.
- Supports technical documentation and the OpenAPI draft.

### 3.7 Shared Responsibilities (All Members)

- Implement features assigned to them through issues.
- Author at least 2 pull requests and review at least 2 pull requests per sprint.
- Follow the workflow defined in `CONTRIBUTING.md`.
- Attend team meetings and keep their board items up to date.

## 4. RACI Matrix

### 4.1 Legend

| Code | Meaning | Description |
|---|---|---|
| **R** | Responsible | Does the work and produces the artifact. Several members may share responsibility. |
| **A** | Accountable | Owns the result and approves completion. Exactly one **A** per activity. |
| **C** | Consulted | Gives input before the decision or deliverable is completed (two-way communication). |
| **I** | Informed | Receives the decision or status because it affects later work (one-way communication). |

`A/R` means the same role is both accountable and responsible.

### 4.2 Matrix

| Activity | PO (İlayda) | SM (Sıla) | ARCH (Işıl) | QA (Efekan) | DEVOPS (İlay) | IMPL (Fatma) |
|---|:---:|:---:|:---:|:---:|:---:|:---:|
| Requirements Engineering | **A/R** | I | C | C | I | C |
| Project Proposal | **A/R** | C | C | I | I | I |
| System Architecture | C | I | **A/R** | C | C | R |
| OpenAPI Design | C | I | **A/R** | C | I | R |
| Frontend Development | C | I | **A** | C | I | R |
| Backend Development | C | I | **A** | C | C | R |
| Quality Assurance | C | I | C | **A/R** | C | C |
| DevOps | I | I | C | C | **A/R** | C |
| Documentation | R | **A/R** | R | R | R | R |
| Sprint Management | C | **A/R** | I | I | I | I |
| Testing | C | I | C | **A/R** | C | R |

**Notes:**

- **Frontend and Backend Development:** the Lead Software Architect is accountable for the technical
  quality of the implementation. The Implementation and Integration Engineer is the main responsible role;
  other members may also be responsible for implementation tasks assigned to them through issues.
  The issue assignee is always the responsible person for that task.
- **Documentation:** each role writes the documents of its own area (R). The Scrum Master is accountable
  for the consistency, completeness, and language (English) of the repository documentation.
- **Testing vs. Quality Assurance:** *Quality Assurance* covers the test strategy, test plans, coverage
  monitoring, and defect tracking. *Testing* covers writing and running unit, integration, and end-to-end tests.
  Developers write unit tests for their own code (R).

## 5. Sprint 1 Deliverable Ownership

| # | Deliverable | Accountable | Responsible | Location | Issue |
|---|---|---|---|---|---|
| 1 | Project Proposal (including open-source license) | PO | PO | `docs/01-project-proposal.md`, `LICENSE` | `#<n>` |
| 2 | System Vision and System Boundary | PO | PO | `docs/02-system-vision.md` | #4 |
| 3 | OpenAPI Draft (YAML) | ARCH | ARCH, IMPL | `api/openapi.yaml` | #6 |
| 4 | Team Roles and RACI Matrix | SM | SM | `docs/03-team-raci.md` | #10 |
| 5 | Contribution Guidelines and Git-Flow Rules | SM | SM | `CONTRIBUTING.md` | #3, #12 |
| 6 | GitHub Organization, Repository, and Branch Protection | DEVOPS | DEVOPS | Repository settings | #1 |
| 7 | Initial Product Backlog | PO | PO | GitHub Projects board, `docs/04-product-backlog.md` | `#<n>` |
| 8 | Review of acceptance criteria and API for testability | QA | QA | Pull request reviews | `#<n>` |
| 9 | Sprint 1 Submission Document and Release | SM | SM | `docs/sprint-1-submission.md`, tag `v0.1.0-sprint1` | #13 |

## 6. Approval

This document was reviewed and accepted by all team members through its pull request.

| Version | Date | Change | Pull Request |
|---|---|---|---|
| 1.0 | 2026-11-03 | Initial team roles and RACI matrix | `#<n>` |
