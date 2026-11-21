# Software Requirements Specification (SRS): Animal Shelter & Adoption System

**Course:** SENG 321 – Software Engineering  
**Role:** Product Owner & Requirements Engineer  
**Deliverable:** `docs/07-srs.md` (Sprint 2 – Simplified ISO/IEC/IEEE 29148 Standard)  
**Version:** 1.3 (Aligned with Official FR Definitions, `operatingHours?: string`, `docs/06-architecture.md`, and `api/openapi.yaml`)  
**License:** Apache License 2.0  

---

## 1. Introduction

### 1.1 Purpose
This Software Requirements Specification (SRS) defines the complete functional, data, interface, and quality requirements for the **Pawradise – Animal Shelter & Adoption System**. It follows a streamlined **ISO/IEC/IEEE 29148** structure and serves as the authoritative specification linking the Product Backlog (`docs/04-backlog.md`), System Architecture & Domain Model (`docs/06-architecture.md`), OpenAPI Contract (`api/openapi.yaml`), and Non-Functional Requirements (`docs/08-nfr.md`).

### 1.2 Scope
The Animal Shelter & Adoption System is a web-based platform connecting verified animal shelters with prospective adopters. The system governs the end-to-end lifecycle of shelter registration and administrative approval, animal listing management via external Image URLs, catalog discovery and filtering for authenticated users, adoption application intake with mandatory document verification, atomic single-approval reservation workflows, physical handover recording, and immutable status history auditing.

Explicit In-Scope and Out-of-Scope boundaries are defined in the System Vision (`docs/02-vision-boundary.md`) and Project Proposal (`docs/01-proposal.md` §6).

### 1.3 Definitions, Acronyms, and Abbreviations

| Term / Code | Definition |
| :--- | :--- |
| **FR** | Functional Requirement (`FR-01`–`FR-21`). |
| **NFR** | Non-Functional Requirement (specified in `docs/08-nfr.md`). |
| **BR** | Business Rule (`BR-01`–`BR-09`). |
| **US** | User Story in the Product Backlog (`US-01`–`US-15` in `docs/04-backlog.md`). |
| **OI / K2** | Open Issue / Team Decision (`K2` resolves `OI-04` regarding shelter approval gating). |
| **RBAC** | Role-Based Access Control (`ADMIN`, `SHELTER_OWNER`, `ADOPTER`). |
| **Soft Delete** | Archiving an animal listing (`status = Archived`) instead of permanent database deletion. |

### 1.4 References
1. **Project Proposal:** `docs/01-proposal.md` (v1.4)
2. **System Vision & Boundary:** `docs/02-vision-boundary.md`
3. **API Flows & Concurrency Specification:** `docs/03-api-flows.md`
4. **Product Backlog:** `docs/04-backlog.md` (v1.4)
5. **System Architecture & Domain Model:** `docs/06-architecture.md`
6. **Non-Functional Requirements:** `docs/08-nfr.md`
7. **OpenAPI Contract:** `api/openapi.yaml`

---

## 2. Overall Description

### 2.1 Product Perspective
The system operates as a containerized, API-first web application backed by an ACID-compliant relational database. Detailed system context, external actors, and boundary definitions are maintained in `docs/02-vision-boundary.md`.

### 2.2 User Classes and Characteristics
1. **Admin (`ADMIN`):** Provisioned administrative accounts (not created via public registration per `A-03`). Responsible for reviewing shelter registration requests (`FR-03`, `FR-04`), moderating inappropriate listings (`FR-05`), and banning/unbanning users (`FR-06`).
2. **Shelter Owner (`SHELTER_OWNER`):** Representatives of animal shelters who register an account (`FR-01`), operate within role-based access rules (`FR-02`), submit a shelter registration request (`FR-03`), and manage their `ShelterProfile` (`FR-07`).
   - **Decision K2 (Resolution of OI-04 – Shelter Approval Gate):** A Shelter Owner whose `ShelterProfile.status` is not yet **`Approved`** is strictly prohibited from executing operational shelter functions (**`FR-08` through `FR-12`, `FR-19`, and `FR-20`**); any such attempt is rejected with HTTP **`403 Forbidden`**.
3. **Adopter (`ADOPTER`):** Prospective pet owners who register and authenticate (`FR-01`, `FR-02`), browse and filter available animals (`FR-13`–`FR-15`), submit adoption applications with required document URLs (`FR-16`), track application statuses (`FR-17`), and withdraw pending applications (`FR-18`).

### 2.3 Assumptions and Dependencies
This SRS incorporates the baseline assumptions **`A-01` through `A-09`** documented in `docs/02-vision-boundary.md` and `docs/01-proposal.md` §7:
- **A-01:** Access via modern web browsers over HTTPS.
- **A-02:** Animal images are stored as external HTTP/HTTPS URLs (`AnimalImage` table, exposed as `imageUrls` in API); binary file uploads are out of scope.
- **A-03:** Admin accounts are provisioned/seeded rather than publicly registered.
- **A-04 & Decision K2 (Closes OI-04):** Shelter Owners must have an Admin-approved shelter profile (`ShelterProfile.status == Approved`) before performing `FR-08`–`FR-12`, `FR-19`, and `FR-20` (`403 Forbidden` otherwise).
- **A-05 & A-06:** Apartment residence (`Application.livesInApartment`) and mandatory document links (`ApplicationDocument` with `type: APARTMENT_PERMISSION`) are submitted per application.
- **A-07 & A-08:** Status history records (`AnimalStatusHistory`, `ApplicationStatusHistory`) are immutable (`BR-09`); automatic rejections record the approving Shelter Owner as `changedBy`.
- **A-09:** Eligibility checks at approval (`FR-20`) enforce `BR-05`, `BR-06`, and `BR-08`.

### 2.4 Core Business Rules Summary

| Rule ID | Business Rule Specification |
| :---: | :--- |
| **BR-01** | Multiple adopters can apply for the same `Available` animal, and an adopter can apply for multiple animals. |
| **BR-02** | Only one application can be approved per animal (`FR-11`). Approving one application atomically sets `Animal.status = Reserved` and automatically transitions all other `Pending` applications for that animal to `Rejected` (`FR-19`). |
| **BR-03** | Adopters can withdraw only applications that are currently in `Pending` status (`409 Conflict` otherwise). |
| **BR-04** | An animal transitions from `Available` $\rightarrow$ `Reserved` upon application approval (`ownerAdopterId` remains `null`), and from `Reserved` $\rightarrow$ `Adopted` (setting `ownerAdopterId` to the approved adopter) strictly after physical handover is recorded (`FR-12`). |
| **BR-05** | Mandatory documents must be provided before an application can be approved (`FR-20`, `422 Unprocessable Content` otherwise). |
| **BR-06** | An apartment permission document (`type: APARTMENT_PERMISSION`) is mandatory for dog adoption when `Application.livesInApartment == true` (`FR-16`, `FR-20`). |
| **BR-07** | Banned users (`User.banned == true`) cannot submit adoption applications (`403 Forbidden`). When an adopter is banned, all of their `Pending` applications are atomically rejected; `Approved` applications remain unchanged *(closes OI-03)*. |
| **BR-08** | An adopter shall not have more than **three (3)** animals in total with `Reserved` or `Adopted` status at the same time (`Available` and `Archived` do not count). Because `Animal.ownerAdopterId` is `null` while an animal is `Reserved` and is populated only at physical handover (`FR-12`), the 3-animal limit **must be calculated via the adopter's `Approved` applications whose associated `Animal.status` is `Reserved` or `Adopted`** (`FR-20`, `422 Unprocessable Content` on violation; *closes OI-02*). |
| **BR-09** | Every animal and application status transition must be immutably recorded (`AnimalStatusHistory` / `ApplicationStatusHistory`) with `changedBy` user ID and `changedAt` UTC timestamp (`FR-21`). Animal listings are archived (`Archived`) rather than permanently deleted. |

---

## 3. Functional Requirements

### 3.1 User Authentication & Access Control Requirements

#### FR-01: User Account Registration, Login, and Logout
- **Source User Story:** `US-01`
- **Actor:** Prospective Adopter, Shelter Owner, System Administrator (login/logout)
- **Preconditions:** For registration (`registerUser`), `User.email` is not yet registered. For login (`loginUser`), the user account exists. For logout (`logoutUser`), the user holds an active authenticated session.
- **Requirement:** The system **shall** allow a prospective Adopter or Shelter Owner to register a new user account (`registerUser`) by providing `fullName`, `email`, password (stored as `passwordHash`), and `role` (`ADOPTER` or `SHELTER_OWNER`; `ADMIN` accounts are not publicly registered per `A-03`), **shall** authenticate users via `email` and password credentials (`loginUser`) to issue a signed JWT bearer token, and **shall** allow authenticated users to log out and terminate their session (`logoutUser`).
- **Postconditions & Error Handling:** Registration returns `201 Created` with the `User` entity (`banned: false`; `passwordHash` and JWT token excluded), `409 Conflict` on duplicate email, or `400 Bad Request` on invalid input. Login returns `200 OK` (`LoginResponse`) or `401 Unauthorized` on invalid credentials. Logout returns `204 No Content`.

#### FR-02: Role-Based Access Control (RBAC)
- **Source User Story:** `US-01`
- **Actor:** All Users (`ADMIN`, `SHELTER_OWNER`, `ADOPTER`)
- **Preconditions:** User sends a request to any protected API endpoint.
- **Requirement:** The system **shall** enforce role-based access control across all protected operations based on the authenticated user's `UserRole` (`ADMIN`, `SHELTER_OWNER`, `ADOPTER`) and account state, ensuring that each actor can invoke only the operations authorized for their role.
- **Postconditions & Error Handling:** Requests lacking a valid JWT bearer token are rejected with `401 Unauthorized`. Authenticated requests attempting an action outside the caller's permitted `UserRole`, or from a `SHELTER_OWNER` whose `ShelterProfile.status != Approved` on gated operations (`K2`), are rejected with `403 Forbidden`.

---

### 3.2 Shelter Onboarding & Administration Requirements

#### FR-03: Shelter Registration Request & Review
- **Source User Story:** `US-02`, `US-03`
- **Actor:** Shelter Owner (submission), System Administrator (review)
- **Preconditions:** Authenticated `SHELTER_OWNER` with valid JWT (for `createShelterProfile`) or authenticated `ADMIN` with valid JWT (for `listShelterRegistrationRequests`).
- **Requirement:** The system **shall** allow a registered Shelter Owner to submit a `ShelterProfile` registration request (`createShelterProfile` with `name`, `address`, `contactEmail`, `phone`, optional `operatingHours`, and optional `description`), initializing `ShelterProfile.status = Pending`, and **shall** allow an Administrator to list and inspect shelter registration requests (`listShelterRegistrationRequests`).
- **Decision K2 Condition (OI-04):** Until an Admin transitions `ShelterProfile.status` to **`Approved`** (`FR-04`), the Shelter Owner is strictly blocked with **`403 Forbidden`** from executing **`FR-08`, `FR-09`, `FR-10`, `FR-11`, `FR-12`, `FR-19`, and `FR-20`**.

#### FR-04: Admin Shelter Registration Decision (Approve / Reject)
- **Source User Story:** `US-03`
- **Actor:** System Administrator (`ADMIN`)
- **Preconditions:** Authenticated `ADMIN` with valid JWT; target `ShelterProfile.status` is `Pending`.
- **Requirement:** The system **shall** allow an Administrator to approve (`approveShelterRegistration`, setting `status = Approved`) or reject (`rejectShelterRegistration`, setting `status = Rejected`) a pending shelter registration request.
- **Postconditions & Error Handling:** Approving transitions `ShelterProfile.status` to `Approved` (unlocking `FR-08`–`FR-12`, `FR-19`, `FR-20` per Decision K2). Rejecting transitions `ShelterProfile.status` to `Rejected`. Non-admin callers receive `403 Forbidden`.

#### FR-05: Admin Animal Listing Moderation
- **Source User Story:** `US-05`
- **Actor:** System Administrator (`ADMIN`)
- **Preconditions:** Authenticated `ADMIN` with valid JWT; target `Animal.status` is `Available`.
- **Requirement:** The system **shall** allow an Administrator to moderate inappropriate or non-compliant animal listings by archiving them (`Animal.status = Archived` via `archiveAnimal`), excluding them from available-animal search queries and recording an `AnimalStatusHistory` entry (`BR-09`). *(Note: Any separate "remove" semantics remain tracked under `OI-06`).*
- **Postconditions & Error Handling:** Returns `200 OK` upon archival; returns `403 Forbidden` for non-authorized roles.

#### FR-06: Admin User Ban and Unban Management
- **Source User Story:** `US-04`
- **Actor:** System Administrator (`ADMIN`)
- **Preconditions:** Authenticated `ADMIN` with valid JWT; target `User` account exists.
- **Requirement:** The system **shall** allow an Administrator to list users (`listUsers`) and ban (`banUser`, setting `User.banned = true`) or unban (`unbanUser`, setting `User.banned = false`) a user account. In accordance with **`BR-07`**, when an Adopter is banned, the system **shall** atomically transition all of that Adopter's `Pending` adoption applications to `Rejected` within the same database transaction and insert a corresponding `ApplicationStatusHistory` record for each rejected application (`BR-09`), while leaving any `Approved` applications unchanged.
- **Postconditions & Error Handling:** Returns `200 OK`. Banned users are subsequently blocked from submitting applications (`FR-16` $\rightarrow$ `403 Forbidden`).

---

### 3.3 Shelter Profile, Animal Inventory & Application Handling Requirements

#### FR-07: Shelter Profile Management
- **Source User Story:** `US-06`
- **Actor:** Shelter Owner (`SHELTER_OWNER`)
- **Preconditions:** Authenticated `SHELTER_OWNER` with valid JWT.
- **Requirement:** The system **shall** allow a Shelter Owner to retrieve (`getMyShelterProfile`) and update (`updateMyShelterProfile`) their `ShelterProfile` attributes, including `name`, `address`, `contactEmail`, `phone`, optional `operatingHours`, and optional `description`, updating `updatedAt`.
- **Postconditions & Error Handling:** Returns `200 OK` with the `ShelterProfile` details.

#### FR-08: Create Animal Listing via Image URL
- **Source User Story:** `US-07`
- **Actor:** Approved Shelter Owner (`SHELTER_OWNER`)
- **Preconditions (Decision K2 / OI-04):** Authenticated `SHELTER_OWNER` with valid JWT whose `ShelterProfile.status` is **`Approved`**. If `ShelterProfile.status` is not `Approved`, the system **shall** reject the request with **`403 Forbidden`**.
- **Requirement:** The system **shall** allow an approved Shelter Owner to create a new `Animal` listing (`POST /animals`, `createAnimal`) with `name`, `species`, `breed`, `ageYears`, `gender`, optional `description`, and one or more valid external HTTP/HTTPS Image URLs (stored in `AnimalImage` and returned as `imageUrls` per `A-02`). New listings **shall** initialize with `status = Available`, `ownerAdopterId = null`, and an initial `AnimalStatusHistory` record (`previousStatus = null`, `newStatus = Available`, `BR-09`).
- **Postconditions & Error Handling:** Returns `201 Created`. Malformed or non-HTTP/HTTPS image URLs return `400 Bad Request`. Unapproved shelters return `403 Forbidden`.

#### FR-09: Update and Archive (Soft-Delete) Animal Listing
- **Source User Story:** `US-08`
- **Actor:** Approved Shelter Owner (`SHELTER_OWNER`)
- **Preconditions (Decision K2 / OI-04):** Authenticated `SHELTER_OWNER` with valid JWT and an **`Approved`** `ShelterProfile` who owns the target animal listing (`Animal.shelterId == shelterProfile.id`; **`403 Forbidden` otherwise**).
- **Requirement:** The system **shall** allow an approved Shelter Owner to partially update (`PATCH /animals/{id}`, `updateAnimal`) an animal listing's descriptive attributes (without creating a status history entry when `status` does not change) or soft-delete (`POST /animals/{id}/archive`, `archiveAnimal`) an `Available` listing by transitioning `Animal.status` from `Available` to `Archived` and recording an `AnimalStatusHistory` entry (`BR-09`). Permanent deletion of animal records shall not be permitted.
- **Postconditions & Error Handling:** Returns `200 OK`. Attempting to modify another shelter's listing or calling with an unapproved shelter returns `403 Forbidden`. Attempting to archive an animal in `Reserved`, `Adopted`, or `Archived` status returns `409 Conflict`.

#### FR-10: View and Inspect Shelter Adoption Applications
- **Source User Story:** `US-14`
- **Actor:** Approved Shelter Owner (`SHELTER_OWNER`)
- **Preconditions (Decision K2 / OI-04):** Authenticated `SHELTER_OWNER` with valid JWT and an **`Approved`** `ShelterProfile` (**`403 Forbidden` otherwise**).
- **Requirement:** The system **shall** allow an approved Shelter Owner to list (`GET /applications?animalId={id}`, `listApplications`) and inspect full details (`GET /applications/{applicationId}`, `getApplication`, including `livesInApartment`, optional `note`, attached `ApplicationDocument` links, and `statusHistory`) of adoption applications submitted for animals belonging to their own shelter.
- **Postconditions & Error Handling:** Returns `200 OK` with the application list or `ApplicationDetail`. Requesting applications for another shelter's animal or calling without an `Approved` shelter profile returns `403 Forbidden`.

#### FR-11: Approve or Reject Adoption Applications
- **Source User Story:** `US-14`
- **Actor:** Approved Shelter Owner (`SHELTER_OWNER`)
- **Preconditions (Decision K2 / OI-04):** Authenticated `SHELTER_OWNER` with valid JWT whose `ShelterProfile.status` is **`Approved`** and who owns the animal associated with the application (**`403 Forbidden` otherwise**). Target `Application.status` is `Pending` and target `Animal.status` is `Available`. All eligibility conditions in `FR-20` are satisfied.
- **Requirement:** The system **shall** allow an approved Shelter Owner to approve (`POST /applications/{id}/approve`, `approveApplication`) or reject (`POST /applications/{id}/reject`, `rejectApplication`) a `Pending` adoption application for their shelter's animal:
  - **On Approval:** Transitions the selected `Application.status` from `Pending` to `Approved`, transitions the target `Animal.status` from `Available` to `Reserved` (`BR-04`, leaving `Animal.ownerAdopterId = null` until physical handover in `FR-12`), triggers automatic rejection of competing `Pending` applications (`FR-19`), and records immutable `AnimalStatusHistory` and `ApplicationStatusHistory` entries (`BR-09`).
  - **On Manual Rejection:** Transitions the `Application.status` from `Pending` to `Rejected`, keeps `Animal.status` as `Available`, and records an `ApplicationStatusHistory` entry (`BR-09`).
- **Postconditions & Error Handling:** Returns `200 OK`. Returns `403 Forbidden` if `ShelterProfile.status != Approved` (`K2`) or the shelter does not own the animal; returns `409 Conflict` if the application is not `Pending` or the animal is already `Reserved`/`Adopted`; returns `422 Unprocessable Content` if `FR-20` eligibility checks fail.

#### FR-12: Record Physical Animal Handover
- **Source User Story:** `US-09`
- **Actor:** Approved Shelter Owner (`SHELTER_OWNER`)
- **Preconditions (Decision K2 / OI-04):** Authenticated `SHELTER_OWNER` with valid JWT and an **`Approved`** `ShelterProfile` owning the target animal (**`403 Forbidden` otherwise**). The target animal must be in **`Reserved`** status with an `Approved` application.
- **Requirement:** The system **shall** allow an approved Shelter Owner to record the physical handover of a `Reserved` animal (`POST /animals/{id}/handover`, `recordHandover`), transitioning `Animal.status` from `Reserved` to `Adopted`, populating **`Animal.ownerAdopterId`** with the `adopterId` of the approved `Application`, and inserting an `AnimalStatusHistory` record (`previousStatus = Reserved`, `newStatus = Adopted`, `BR-04`, `BR-09`).
- **Postconditions & Error Handling:** Returns `200 OK`. If the animal is not in `Reserved` status (`Available`, `Adopted`, or `Archived`), the system returns `409 Conflict`. If the shelter profile is not `Approved`, returns `403 Forbidden`.

---

### 3.4 Animal Discovery Requirements

#### FR-13: Browse Available Animals Catalog
- **Source User Story:** `US-10`
- **Actor:** Authenticated User (primarily `ADOPTER`)
- **Preconditions:** Authenticated user with a valid JWT bearer token (`401 Unauthorized` if missing or invalid).
- **Requirement:** The system **shall** allow authenticated users to browse a catalog of animals (`GET /animals`, `listAnimals`) currently in `Available` status, returning summary attributes and external `imageUrls` (with client-side fallback placeholders if an external URL is unreachable).
- **Postconditions & Error Handling:** Returns `200 OK` with matching `Available` animals, excluding `Reserved`, `Adopted`, and `Archived` records by default. Returns `401 Unauthorized` for unauthenticated requests.

#### FR-14: Filter Available Animals by Species and Age
- **Source User Story:** `US-10`
- **Actor:** Authenticated User (primarily `ADOPTER`)
- **Preconditions:** Authenticated user with a valid JWT bearer token (`401 Unauthorized` if missing or invalid).
- **Requirement:** The system **shall** allow authenticated users to filter the available animals catalog (`GET /animals`, `listAnimals`) by `species` and inclusive `ageYears` bounds (`minAge`, `maxAge`).
- **Postconditions & Error Handling:** Returns `200 OK` with the filtered list of `Available` animals; returns `400 Bad Request` on invalid query parameter types; returns `401 Unauthorized` if unauthenticated.

#### FR-15: View Animal Detail and Current Status
- **Source User Story:** `US-11`
- **Actor:** Authenticated User (primarily `ADOPTER`)
- **Preconditions:** Authenticated user with a valid JWT bearer token (`401 Unauthorized` if missing or invalid). Target `Animal.id` exists.
- **Requirement:** The system **shall** display the complete profile of a specific animal (`GET /animals/{id}`, `getAnimal`), including `name`, `species`, `breed`, `ageYears`, `gender`, `description`, `shelterId`, `imageUrls`, and current `status` (`Available`, `Reserved`, `Adopted`, `Archived`).
- **Postconditions & Error Handling:** Returns `200 OK` if found; returns `404 Not Found` if the animal ID does not exist; returns `401 Unauthorized` if unauthenticated.

---

### 3.5 Adoption Application Rules & Audit Requirements

#### FR-16: Submit Adoption Application with Document Links
- **Source User Story:** `US-12`
- **Actor:** Authenticated Adopter (`ADOPTER`)
- **Preconditions:** Authenticated `ADOPTER` with valid JWT and `User.banned == false` (`BR-07`); target `Animal.status` is `Available`.
- **Requirement:** The system **shall** allow a non-banned Adopter to submit an `Application` (`POST /applications`, `submitApplication`) for an `Available` animal, recording `adopterId`, `animalId`, `livesInApartment` (`bool`), optional `note`, and associated `ApplicationDocument` entries (`type`, `url`), supporting multiple concurrent applicants per animal and multiple applications per adopter (`BR-01`). When `livesInApartment == true` and the target animal has `species == "Dog"`, the system **shall** require a document entry `{ type: "APARTMENT_PERMISSION", url: "<uri>" }` (`BR-06`).
- **Postconditions & Error Handling:** Persists `Application` with `status = Pending`, inserts an initial `ApplicationStatusHistory` record (`previousStatus = null`, `newStatus = Pending`, `BR-09`), and returns `201 Created`. Returns `403 Forbidden` if user is banned; `422 Unprocessable Content` if required `APARTMENT_PERMISSION` document is missing; `409 Conflict` if animal is in `Reserved`, `Adopted`, or `Archived` status; `404 Not Found` if animal does not exist.

#### FR-17: Track Adoption Application Statuses
- **Source User Story:** `US-13`
- **Actor:** Authenticated Adopter (`ADOPTER`)
- **Preconditions:** Authenticated `ADOPTER` with valid JWT.
- **Requirement:** The system **shall** allow an Adopter to list and track the current statuses (`Pending`, `Approved`, `Rejected`, `Withdrawn`) of all adoption applications they have submitted (`GET /applications`, `listApplications`).
- **Postconditions & Error Handling:** Returns `200 OK` with the adopter's applications.

#### FR-18: Withdraw Pending Adoption Application
- **Source User Story:** `US-13`
- **Actor:** Authenticated Adopter (`ADOPTER`)
- **Preconditions:** Authenticated `ADOPTER` with valid JWT who owns the application (`Application.adopterId == user.id`); `Application.status` is `Pending` (`BR-03`).
- **Requirement:** The system **shall** allow an Adopter to withdraw their own `Pending` application (`POST /applications/{id}/withdraw`, `withdrawApplication`), transitioning `Application.status` to `Withdrawn` and recording an `ApplicationStatusHistory` entry (`BR-09`).
- **Postconditions & Error Handling:** Returns `200 OK`. If `Application.status` is already `Approved`, `Rejected`, or `Withdrawn`, returns `409 Conflict`. If the application belongs to another adopter, returns `403 Forbidden`.

#### FR-19: Automatic Rejection of Competing Pending Applications on Approval
- **Source User Story:** `US-14`
- **Actor:** System (triggered during `approveApplication` by an Approved `SHELTER_OWNER`)
- **Preconditions (Decision K2 / OI-04):** Authenticated `SHELTER_OWNER` with valid JWT and an **`Approved`** `ShelterProfile` owning the target animal (**`403 Forbidden` otherwise**). An approval operation (`FR-11`) is invoked on a `Pending` application for an `Available` animal that passes `FR-20` eligibility checks.
- **Requirement:** In accordance with **`BR-02`**, only one application shall ever be approved for a given animal. When a Shelter Owner approves one `Pending` application (`POST /applications/{id}/approve`, `approveApplication`), the system **shall** automatically and atomically transition all other `Pending` applications for that same `animalId` to `Rejected` within the same database transaction, and **shall** insert an immutable `ApplicationStatusHistory` record (`previousStatus = Pending`, `newStatus = Rejected`, `changedBy` set to the approving Shelter Owner's user ID per `A-07`, `BR-09`) for every automatically rejected application.
- **Postconditions & Concurrency Error Handling:** All competing `Pending` applications become `Rejected` in the same transaction as the single `Approved` application. If two concurrent approval requests arrive simultaneously for the same animal, row-level locking ensures only the first commits (`200 OK`), while the second aborts with **`409 Conflict`**.

#### FR-20: Enforce Adoption Eligibility & BR-08 Quota Before Approval
- **Source User Story:** `US-14`
- **Actor:** System / Approved Shelter Owner (`SHELTER_OWNER`)
- **Preconditions (Decision K2 / OI-04):** Authenticated `SHELTER_OWNER` with valid JWT whose `ShelterProfile.status` is **`Approved`** and who owns the animal associated with the application (**`403 Forbidden` otherwise**).
- **Requirement:** Before committing any application approval (`POST /applications/{id}/approve`, `approveApplication`), the system **shall** validate all mandatory eligibility conditions (`A-09`) and **shall block approval** if any condition is not met:
  1. **Mandatory Documents & Apartment Permission (`BR-05`, `BR-06`):** All required `ApplicationDocument` rows must be present on the application, specifically including a document with `type == "APARTMENT_PERMISSION"` when `Application.livesInApartment == true` and the target animal's `species` is a dog.
  2. **BR-08 3-Animal Quota via Approved Applications (Not `ownerAdopterId`):** An adopter shall not have more than **three (3)** animals in total with `Reserved` or `Adopted` status at the same time (`Available` and `Archived` do not count). Because `Animal.ownerAdopterId` remains `null` while an animal is in `Reserved` status and is only populated upon physical handover (`FR-12`), the system **shall calculate the adopter's active count via the adopter's existing `Application` records with `Application.status == Approved` whose associated `Animal.status` is currently `Reserved` or `Adopted`**. If this count is already **$\ge 3$**, the system **shall** prevent approval.
- **Postconditions & Concurrency Error Handling:** If any eligibility condition (`BR-05`, `BR-06`, or `BR-08`) fails—including when two concurrent approvals for different animals target the same adopter who already has 2 `Reserved`/`Adopted` animals—the system **shall** abort the transaction without changing any application or animal status and return **`422 Unprocessable Content`**. If `ShelterProfile.status != Approved` (`K2`), the system returns **`403 Forbidden`**.

#### FR-21: Immutable Status History and Audit Trail Recording
- **Source User Story:** `US-15`
- **Actor:** System / Authenticated Authorized Users (`ADMIN`, `SHELTER_OWNER`, `ADOPTER`)
- **Preconditions:** Any status transition occurs on an `Animal` or `Application`.
- **Requirement:** The system **shall** automatically persist an immutable record in **`AnimalStatusHistory`** (for animal transitions) or **`ApplicationStatusHistory`** (for application transitions) within the same database transaction, capturing `previousStatus` (nullable on initial creation), `newStatus`, `changedBy` (user ID), and `changedAt` (UTC datetime) (`BR-09`). In API responses, both history tables **shall** serialize using the unified **`StatusChange`** schema exposed via `getAnimalStatusHistory` (`GET /animals/{animalId}/status-history`) and `getApplication` (`ApplicationDetail.statusHistory`). No API operation shall permit modifying or deleting status history records (`A-08`).

---

### 3.6 Functional Requirements Traceability Matrix

| FR ID | Official Requirement Summary | Source US | Primary Actor | K2 Approval Gate (`403`) | OpenAPI `operationId` |
| :---: | :--- | :---: | :---: | :---: | :--- |
| **FR-01** | User Registration, Login & Logout | `US-01` | Adopter / Shelter Owner / Admin | No | `registerUser`, `loginUser`, `logoutUser` |
| **FR-02** | Role-Based Access Control (RBAC) | `US-01` | All Roles (`ADMIN`, `SHELTER_OWNER`, `ADOPTER`) | Enforces Role & K2 Gate | All authenticated operations (`BearerAuth`) |
| **FR-03** | Shelter Registration Request & Review | `US-02`, `US-03` | Shelter Owner / Admin | Defines Gate (`OI-04`/`K2`) | `createShelterProfile`, `listShelterRegistrationRequests` |
| **FR-04** | Admin Shelter Approval / Rejection | `US-03` | Admin | Grants `Approved` Status | `approveShelterRegistration`, `rejectShelterRegistration` |
| **FR-05** | Admin Animal Listing Moderation | `US-05` | Admin | No (Admin Role) | `archiveAnimal` |
| **FR-06** | Admin User Ban / Unban (`BR-07`) | `US-04` | Admin | No (Admin Role) | `banUser`, `unbanUser`, `listUsers` |
| **FR-07** | Shelter Profile Management (`operatingHours`) | `US-06` | Shelter Owner | No | `getMyShelterProfile`, `updateMyShelterProfile` |
| **FR-08** | Create Animal Listing (Image URL) | `US-07` | Shelter Owner | **Yes (`Approved` required)** | `createAnimal` |
| **FR-09** | Update (`PATCH`) & Archive Listing | `US-08` | Shelter Owner | **Yes (`Approved` required)** | `updateAnimal`, `archiveAnimal` |
| **FR-10** | View & Inspect Shelter Applications | `US-14` | Shelter Owner | **Yes (`Approved` required)** | `listApplications`, `getApplication` |
| **FR-11** | Approve or Reject Adoption Applications | `US-14` | Shelter Owner | **Yes (`Approved` required)** | `approveApplication`, `rejectApplication` |
| **FR-12** | Record Physical Handover | `US-09` | Shelter Owner | **Yes (`Approved` required)** | `recordHandover` |
| **FR-13** | Browse Available Animals | `US-10` | Authenticated User (Adopter) | No (Valid JWT required) | `listAnimals` |
| **FR-14** | Filter Animals by Species & Age | `US-10` | Authenticated User (Adopter) | No (Valid JWT required) | `listAnimals` |
| **FR-15** | View Animal Detail & Status | `US-11` | Authenticated User (Adopter) | No (Valid JWT required) | `getAnimal` |
| **FR-16** | Submit Adoption Application | `US-12` | Adopter | No (`banned = false` req.) | `submitApplication` |
| **FR-17** | Track Application Statuses | `US-13` | Adopter | No | `listApplications` |
| **FR-18** | Withdraw Pending Application | `US-13` | Adopter | No | `withdrawApplication` |
| **FR-19** | Auto-Reject Other Pending Applications on Approval (`BR-02`) | `US-14` | System / Shelter Owner | **Yes (`Approved` required)** | `approveApplication` |
| **FR-20** | Block Approval if Eligibility / `BR-08` Not Met (`422`) | `US-14` | System / Shelter Owner | **Yes (`Approved` required)** | `approveApplication` |
| **FR-21** | Immutable Status History Logging | `US-15` | System / All Roles | No | `getAnimalStatusHistory`, `getApplication` |

---

## 4. Data Requirements

The data model in this section is **100% identical** to the Domain Model specified in the System Architecture document (`docs/06-architecture.md`) and matches the field names in `api/openapi.yaml`. It consists of **8 core entities** and **4 enumerations**.

### 4.1 Enumerations (4 Enums)

| Enumeration Name | Allowed Values |
| :--- | :--- |
| **`UserRole`** | `ADMIN`, `SHELTER_OWNER`, `ADOPTER` |
| **`ShelterRegistrationStatus`** | `Pending`, `Approved`, `Rejected` |
| **`AnimalStatus`** | `Available`, `Reserved`, `Adopted`, `Archived` |
| **`ApplicationStatus`** | `Pending`, `Approved`, `Rejected`, `Withdrawn` |

### 4.2 Domain Entities and Fields (8 Entities)

| Entity | Fields & Types (`?` = Nullable) | Domain & API Mapping Notes |
| :--- | :--- | :--- |
| **`User`** | `id: int`<br>`fullName: string`<br>`email: string`<br>`passwordHash: string`<br>`role: UserRole`<br>`banned: bool`<br>`createdAt: datetime` | `passwordHash` is stored strictly in the database and is **never** exposed in any API response. Owns `0..1` `ShelterProfile`, submits `0..*` `Application`, owns `0..*` `Animal` after handover, and records status changes. |
| **`ShelterProfile`** | `id: int`<br>`ownerUserId: int`<br>`name: string`<br>`address: string`<br>`contactEmail: string`<br>`phone: string`<br>`operatingHours?: string`<br>`description?: string`<br>`status: ShelterRegistrationStatus`<br>`createdAt: datetime`<br>`updatedAt: datetime` | Associated `1:0..1` with `User` (`ownerUserId`). Includes optional `operatingHours?: string` (`US-06`, `FR-07`). Lists `0..*` `Animal` records. Must have `status == Approved` to execute `FR-08`–`FR-12`, `FR-19`, and `FR-20` (**Decision K2 / OI-04**). |
| **`Animal`** | `id: int`<br>`shelterId: int`<br>`ownerAdopterId?: int`<br>`name: string`<br>`species: string`<br>`breed: string`<br>`ageYears: int`<br>`gender: string`<br>`description?: string`<br>`status: AnimalStatus`<br>`createdAt: datetime`<br>`updatedAt: datetime` | `ownerAdopterId` is nullable (`int?`): it remains `null` when `status` is `Available` or `Reserved`, and is populated only when physical handover is recorded (`FR-12`, `status = Adopted`). |
| **`AnimalImage`** | `id: int`<br>`animalId: int`<br>`url: string` | Stores animal photo links in a separate relational table (one row per URL) associated `0..*` with `Animal`; serialized as `imageUrls` array in OpenAPI responses. |
| **`Application`** | `id: int`<br>`adopterId: int`<br>`animalId: int`<br>`livesInApartment: bool`<br>`note?: string`<br>`status: ApplicationStatus`<br>`createdAt: datetime`<br>`updatedAt: datetime` | Links `User` (`adopterId`) and `Animal` (`animalId`). `note` is nullable (`string?`). Used alongside `Animal.status` (`Reserved` or `Adopted`) to calculate the adopter's active 3-animal limit (`BR-08`) during `FR-20`. |
| **`ApplicationDocument`** | `id: int`<br>`applicationId: int`<br>`type: string`<br>`url: string` | Stores attached document links in a separate table (one row per document link, e.g., `type = "APARTMENT_PERMISSION"`) associated `0..*` with `Application`. |
| **`AnimalStatusHistory`** | `id: int`<br>`animalId: int`<br>`previousStatus?: AnimalStatus`<br>`newStatus: AnimalStatus`<br>`changedBy: int`<br>`changedAt: datetime` | Immutable audit log for `Animal` status transitions (`previousStatus` is nullable on initial creation). Exposed in the API via the unified **`StatusChange`** schema. |
| **`ApplicationStatusHistory`** | `id: int`<br>`applicationId: int`<br>`previousStatus?: ApplicationStatus`<br>`newStatus: ApplicationStatus`<br>`changedBy: int`<br>`changedAt: datetime` | Immutable audit log for `Application` status transitions (`previousStatus` is nullable on initial submission). Exposed in the API via the unified **`StatusChange`** schema. |

---

## 5. External Interface Requirements

### 5.1 User Interfaces
- Role-specific responsive web views covering **adopter catalog and application views**, **shelter management and application review views**, and **administrative moderation and approval views** (aligned with Decision K7).
- Form inputs and detail screens map directly to the 8 domain entities and attributes in Section 4.
- UI/UX wireframes, component layouts, and user-flow diagrams are specified in the Sprint 2 Figma UI/UX prototype deliverable.

### 5.2 Software and API Interfaces
- The RESTful HTTP/JSON API contract is normatively specified in **`api/openapi.yaml`** and detailed with sequence/concurrency flows in **`docs/03-api-flows.md`**.
- **Authentication & Authorization:** Bearer JWT authentication (`Authorization: Bearer <token>`) is required for all endpoints except public registration (`registerUser`) and login (`loginUser`). Role checks (`FR-02`) and **Decision K2 (`ShelterProfile.status == Approved`)** are enforced at the API layer, returning `401 Unauthorized` for missing/invalid tokens and `403 Forbidden` for insufficient role permissions or unapproved shelter profiles.
- **Standardized Error Responses:** Conforms to HTTP status codes defined in `api/openapi.yaml` (`400 Bad Request`, `401 Unauthorized`, `403 Forbidden`, `404 Not Found`, `409 Conflict`, and `422 Unprocessable Content`).

---

## 6. Non-Functional Requirements

All Non-Functional Requirements (NFRs), quality attributes, target response times, concurrency/transaction isolation guarantees, security and RBAC policies, usability targets, and deployment constraints are normatively specified and maintained in **`docs/08-nfr.md`**.
