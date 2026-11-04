# Pawradise – Animal Shelter & Adoption System

## 1. Project Description

Pawradise is a web-based animal shelter and adoption management system designed to connect animal shelters with potential adopters.

The platform provides a centralized environment where shelters can manage animal listings and adoption applications, while adopters can discover animals, submit adoption requests, and track their application statuses.

The system also provides administrative functionalities for managing shelter registrations, user accounts, and inappropriate animal listings.

## 2. Problem Statement

Animal shelters and potential adopters may face difficulties managing adoption processes through fragmented communication channels and manual procedures.

Information about animals may be incomplete or difficult to access, while adoption applications can be challenging to track and manage efficiently.

Pawradise aims to address these problems by providing a centralized platform that makes animal information accessible and adoption processes more organized, transparent, and manageable.

## 3. Main Features

- User registration, login, and logout.
- Role-based access control.
- Shelter registration and approval management.
- Shelter profile management.
- Animal listing creation, updating, and archiving.
- Animal browsing, searching, and filtering by species and age.
- Detailed animal information pages.
- Adoption application submission with required information and documents.
- Adoption application approval, rejection, and withdrawal.
- Automatic rejection of other pending applications when one application is approved.
- Adoption eligibility and document validation.
- User banning and unbanning.
- Animal handover recording.
- Application and animal status history.

## 4. User Roles

The system supports three user roles:

### 4.1 Admin

- View shelter registration requests.
- Approve or reject shelter registration requests.
- Ban or unban users.
- Remove or archive inappropriate animal listings.

### 4.2 Shelter Owner

- Create and update shelter profiles.
- Add animal listings using image URLs.
- Update or archive animal listings.
- View adoption applications.
- Approve or reject adoption applications.
- Record animal handover after adoption approval.

### 4.3 Adopter

- Browse available animals.
- Search and filter animals by species and age.
- View detailed animal information.
- Submit adoption applications with required information and documents.
- View adoption application statuses and history.
- Withdraw pending adoption applications.

## 5. Business Rules

The system follows these main business rules:

- Only one adoption application can be approved for a particular animal.
- When an application is approved, all other pending applications for the same animal are automatically rejected.
- Adopters can withdraw applications only while their status is Pending.
- An animal is marked as Adopted only after physical handover is completed.
- Mandatory documents must be provided before an adoption application can be approved.
- Apartment permission is required for adopters living in apartments who want to adopt a dog.
- Banned adopters cannot submit new adoption applications.
- When an adopter is banned, all their pending applications are automatically rejected. Previously approved applications remain unchanged.
- An adopter cannot have more than three animals with Reserved or Adopted status combined at the same time.
- All application and animal status changes are recorded with the responsible user, previous status, new status, and timestamp.

## 6. Technology Stack

The proposed technology stack is:

| Component | Technology |
|---|---|
| Frontend | React |
| Backend | Python (FastAPI) |
| Database | MySQL |
| ORM | SQLAlchemy |
| Data Validation | Pydantic |
| Authentication | JWT |
| API Architecture | REST API |
| API Specification | OpenAPI |
| Version Control | Git and GitHub |
| Documentation | Markdown |
| API Validation | Swagger Editor |

The technology stack is subject to final team confirmation.

## 7. Repository Structure

The repository structure is organized as follows:

```text
pawradise/
├── .github/
│   ├── ISSUE_TEMPLATE/
│   └── PULL_REQUEST_TEMPLATE.md
├── api/
│   └── openapi.yaml
├── docs/
│   ├── meetings/
│   ├── evidence/
│   ├── 01-proposal.md
│   ├── 02-vision-boundary.md
│   ├── 03-team-raci.md
│   └── 04-backlog.md
├── CONTRIBUTING.md
├── LICENSE
└── README.md
```

## 8. Team Members

| Team Member | Role |
|---|---|
| İlay | DevOps Engineer |
| İlayda | Product Owner |
| Işıl | Software Architect |
| Fatma | Implementation Developer |
| Sıla | Scrum Master |
| Efekan | Quality Assurance Engineer |

## 9. Getting Started

### Prerequisites

The project development environment and installation procedures will be documented as implementation progresses.

The planned technologies include:

- Node.js and npm for the React frontend.
- Python for the FastAPI backend.
- MySQL for database management.

### Installation and Execution

Detailed installation, configuration, and execution instructions will be provided when the application implementation and environment configuration are established.

## 10. Project Documentation

| Document | Description |
|---|---|
| `docs/01-proposal.md` | Project Proposal |
| `docs/02-vision-boundary.md` | System Vision and Boundary |
| `docs/03-team-raci.md` | Team Roles and RACI Matrix |
| `docs/04-backlog.md` | Initial Product Backlog |
| `api/openapi.yaml` | API Specification |
| `CONTRIBUTING.md` | Contribution Guidelines |

## 11. License

This project is licensed under the Apache License, Version 2.0.

See the [LICENSE](LICENSE) file for the full license text.
