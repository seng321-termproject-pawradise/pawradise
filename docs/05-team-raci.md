# Team Roles and RACI Matrix

## 1. Team Information

| Field | Value |
|---|---|
| Course | SENG 321 – Software Engineering |
| Semester | Fall 2026 |
| Team Number | Team 20 |
| Project Name | Pawradise – Animal Shelter & Adoption System |
| Project Type | Web Application |
| Repository | https://github.com/seng321-termproject-pawradise/pawradise |
| Project Board | https://github.com/orgs/seng321-termproject-pawradise/projects/2 |
| Requirements Source | Official Requirements Reference (FR-01 … FR-21, NFR-01 … NFR-09, BR-01 … BR-09) |

## 2. Team Members and Roles

Every member uses an individual GitHub account, so that contributions are traceable.

| # | Name | GitHub Username | Role | Abbreviation |
|---|---|---|---|---|
| 1 | İlayda Erşin | @ilayda-ersin | Product Owner and Requirements Engineer | PO |
| 2 | Şevval Sıla Çelik | @sevvalsilacelik | Scrum Master and Agile Process Coach | SM |
| 3 | Işıl Gültekin | @gltknisil | Lead Software Architect and Technical Lead | ARCH |
| 4 | Efekan Coşkun | @VarnaToent0 | Quality Assurance and Test Automation Engineer | QA |
| 5 | Aslınur İlay Tekin | @aslnurilay | DevOps and CI/CD Infrastructure Engineer | DEVOPS |
| 6 | Fatma Nur Kodak | @fnurkodak | Implementation and Integration Engineer | IMPL |

## 3. Roles and Responsibilities

The responsibilities cover the six areas required for Sprint 1: **product, process, architecture, quality, DevOps, and implementation**.

### 3.1 Product Owner and Requirements Engineer (PO) – Product

- Owns the product vision and the official requirements reference (FR, NFR, BR).
- Prepares the Project Proposal.
- Writes epics and user stories with acceptance criteria, estimates, dependencies, and owners.
- Maintains and prioritizes the product backlog.
- Ensures traceability between requirements, backlog items, API endpoints, and the implemented product.
- Validates deliverables against stakeholder needs.

### 3.2 Scrum Master and Agile Process Coach (SM) – Process

- Facilitates sprint planning, stand-ups, and retrospectives, and records meeting minutes in `docs/meetings/`.
- Maintains the RACI matrix and the contribution guidelines (`CONTRIBUTING.md`).
- Administers the GitHub Projects board and tracks team progress.
- Tracks and removes impediments through the GitHub Projects board and the meeting minutes in `docs/meetings/`.
- Monitors the individual contribution requirement (2 pull requests authored, 2 reviewed per member per sprint).
- Prepares sprint releases and submission documents.

### 3.3 Lead Software Architect and Technical Lead (ARCH) – Architecture

- Defines the system and software architecture (C4 model, UML diagrams).
- Prepares the System Vision and Boundary and the main API flows.
- Leads the API-first design and owns the OpenAPI specification.
- Makes and documents architectural and technology decisions.
- Leads code reviews and maintains technical consistency.

### 3.4 Quality Assurance and Test Automation Engineer (QA) – Quality

- Prepares test plans and the test strategy.
- Reviews acceptance criteria and the OpenAPI contract for testability.
- Develops unit, integration, and end-to-end tests.
- Tracks defects and monitors unit-test coverage.

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
| Software Requirements Specification (SRS) | **A/R** | I | C | C | I | C |
| Non-Functional Requirements | R | I | C | **A/R** | C | I |
| System Vision and Boundary | C | I | **A/R** | C | I | C |
| System Architecture | C | I | **A/R** | C | C | R |
| OpenAPI Design | C | I | **A/R** | C | I | R |
| UI/UX Design and User Flows (Figma) | C | I | C | C | I | **A/R** |
| Deployment Planning | I | I | C | C | **A/R** | I |
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

## 5. Deliverable Ownership

### 5.1 Sprint 1

| # | Deliverable | Accountable | Responsible | Location | Issue |
|---|---|---|---|---|---|
| 1 | Project Proposal (including open-source license) | PO | PO | `docs/01-proposal.md`, `LICENSE` | #20 |
| 2 | System Vision and System Boundary | ARCH | ARCH | `docs/02-vision-boundary.md` | #4 |
| 3 | OpenAPI Draft (YAML) | ARCH | ARCH, IMPL | `api/openapi.yaml`, `docs/03-api-flows.md` | #6 |
| 4 | Team Roles and RACI Matrix | SM | SM | `docs/05-team-raci.md` | #10 |
| 5 | Contribution Guidelines and Git-Flow Rules | SM | SM | `CONTRIBUTING.md` | #3, #12 |
| 6 | GitHub Organization, Repository, and Branch Protection | DEVOPS | DEVOPS | Repository settings | #1 |
| 7 | Initial Product Backlog | PO | PO | GitHub Projects board, `docs/04-backlog.md` | #21 |
| 8 | Review of acceptance criteria and API for testability | QA | QA | Pull request reviews and QA issues | #38, #39, #40, #41 |
| 9 | Individual Contributions, Sprint 1 Release, and Submission | SM | SM | `CONTRIBUTIONS.md`, tag `sprint-1` | #13, #35 |

### 5.2 Sprint 2

Task assignment and decisions: [`docs/meetings/sprint-2-decision-log.md`](meetings/sprint-2-decision-log.md).

| # | Deliverable | Accountable | Responsible | Location | Issue / PR |
|---|---|---|---|---|---|
| 1 | Software Requirements Specification | PO | PO | `docs/07-srs.md` | #55, #56 |
| 2 | Non-Functional Requirements | QA | QA, PO | `docs/08-nfr.md` | #51, #52 |
| 3 | Architecture Document (ADRs, C4, UML, database schema) | ARCH | ARCH | `docs/06-architecture.md` | #53, #54 |
| 4 | Figma Prototypes, User Flows, and UI/UX Document | IMPL | IMPL, ARCH, DEVOPS, SM (screens) | Figma, `docs/09-ui-ux.md` | #68 |
| 5 | OpenAPI Update (admin and shelter operations, version 0.2.0) | ARCH | IMPL | `api/openapi.yaml` | #19, #25 |
| 6 | Deployment Plan | DEVOPS | DEVOPS | `docs/10-deployment-plan.md` | #61, #62 |
| 7 | Product Backlog Update and User Story Issues | PO | PO | `docs/04-backlog.md`, GitHub Issues | #39, #40, #64 |
| 8 | Sprint 2 Board, Decision Log, and RACI Update | SM | SM | Project board, `docs/meetings/`, `docs/05-team-raci.md` | #57, #60 |
| 9 | Individual Contributions, Sprint 2 Release, and Submission | SM | SM | `CONTRIBUTIONS.md`, tag `sprint-2` | #67 |

## 6. Approval

This document was reviewed and accepted by all team members through its pull request.

| Version | Date | Change | Pull Request |
|---|---|---|---|
| 1.0 | 2026-11-03 | Initial team roles and RACI matrix | #17 |
| 1.1 | 2026-11-19 | Sprint 2 activities and deliverable ownership | #60 |
| 1.2 | 2026-11-24 | Sprint 2 issue and pull request links, deployment plan location, Figma screen ownership | #69 |
