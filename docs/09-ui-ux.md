# Pawradise – UI/UX Design and User Flows

> **Document status:** Sprint 2 UI/UX handoff – finalized for the agreed 18-screen scope  
> **OpenAPI version:** 0.2.0  
> **Figma prototype:** https://www.figma.com/design/YkGpE92LK1feJznGD0ni8T/pawradise?node-id=0-1&t=Aatil3Gr8kyKeVbG-0

## 1. Purpose and scope

This document describes the Pawradise interface, user journeys, screen-to-requirement traceability, API operations, confirmation dialogs, and error behavior. It covers the agreed 18 main Figma screens for Guest, Adopter, Shelter Owner, and Admin. Dashboard and separate user-profile screens are outside this UI/UX scope.

## 2. User roles and access

| Role | Main interface responsibilities |
|---|---|
| Guest | Register and log in |
| Adopter | Browse animals, submit adoption applications, view application status/history, and withdraw a Pending application |
| Shelter Owner | Manage own shelter profile; after approval, manage own animal listings and review adoption applications |
| Admin | Moderate users and shelter registrations; use Animal Management for moderation with role-based permissions |

### 2.1 Access rules

- Public registration is available only for Adopter and Shelter Owner. Admin accounts are provisioned separately.
- After login, route the user according to `LoginResponse.user.role`.
- A Shelter Owner with no profile or a Pending/Rejected profile is directed to Screen 8. An Approved Shelter Owner can access animal and application management.
- Pending and Rejected Shelter Owners may view/update their own shelter profile but cannot manage animals or applications; those operations are forbidden.
- A Rejected Shelter Owner cannot submit another shelter registration request in the current scope.
- A banned Adopter may log in but cannot submit a new adoption application.
- Protected screens require authentication. The access token lifetime is 30 minutes (1800 seconds). An expired session returns the user to Login after a 401 response.
- Screen 9 is shared by Shelter Owner and Admin. The available actions and data are role-scoped: Shelter Owner manages their own shelter's animals; Admin performs moderation/archive actions according to Admin permissions. This reflects the accepted FR-05 decision.

## 3. Visual and interaction conventions

The supplied screens use a consistent green-and-white visual language, light backgrounds, rounded cards, sidebar navigation, status badges, and differentiated primary/secondary actions.

1. State-changing or destructive actions open a confirmation dialog before the request is sent.
2. Cancel closes the dialog and sends no API request.
3. The UI changes a resource status only after the API confirms success.
4. Validation/server errors appear near the relevant form or in the corresponding error state. Preserve entered data when safe.
5. Empty states are provided for lists that may contain no records, including Screens 6 and 12.
6. Actions unavailable for the current role or resource status must not be presented as available.

## 4. User-flow diagrams

The four diagrams below are the canonical user-flow set for this handoff.

### 4.1 Authentication and role-based routing

![Authentication flow](assets/ui-ux/flows/flow-authentication.png)

### 4.2 Adopter flow

![Adopter flow](assets/ui-ux/flows/flow-adopter.png)

### 4.3 Shelter Owner flow

![Shelter Owner flow](assets/ui-ux/flows/flow-shelter-owner.png)

### 4.4 Admin flow

![Admin flow](assets/ui-ux/flows/flow-admin.png)

## 5. Screen descriptions and traceability

The following table maps the agreed screen set to functional requirements and user stories. Screen 9 is shared by Shelter Owner and Admin with role-based permissions.

| No. | Screen | Role | FR | User Story | Purpose / behavior |
|---:|---|---|---|---|---|
| 1 | Login | All | FR-01, FR-02 | US-01 | Authenticate with email/password and route by returned role; show invalid-credentials feedback. |
| 2 | Register | Guest | FR-01, FR-02 | US-01 | Register as Adopter or Shelter Owner; Admin registration is not public. |
| 3 | Animal Listing | Adopter | FR-13, FR-14 | US-10 | Browse/filter available animals and open a selected animal. |
| 4 | Animal Detail | Adopter | FR-15 | US-11 | Show animal details; Apply is available only while status is Available. |
| 5 | Adoption Application Form | Adopter | FR-16 | US-12 | Collect application data and document URLs. Apartment permission URL is required for an apartment-dwelling Adopter applying for a dog. No file upload is used. |
| 6 | My Applications | Adopter | FR-17, FR-18 | US-13 | List own applications and statuses; include populated and empty states. |
| 7 | Application Detail | Adopter | FR-17, FR-18, FR-21 | US-13, US-15 | Show application details/status history; Withdraw is available only while Pending. |
| 8 | Shelter Profile | Shelter Owner | FR-07 | US-06 | Create/view/update own profile; designs cover New, Pending, Approved, Rejected. |
| 9 | Animal Management | Shelter Owner, Admin | FR-05, FR-09 | US-05, US-08 | Shared screen with role-based permissions. Shelter Owner manages own listings; Admin performs moderation. Archive only Available animals; archive is not permanent deletion. |
| 10 | Add Animal | Shelter Owner | FR-08 | US-07 | Create an animal listing with characteristics and image URLs; invalid URLs show validation feedback. |
| 11 | Edit Animal | Shelter Owner | FR-09 | US-08 | Update editable fields of an existing animal; editing does not itself change status. |
| 12 | Application Management | Shelter Owner | FR-10 | US-14 | List/filter applications for the shelter's animals; include empty state. |
| 13 | Application Review | Shelter Owner | FR-10, FR-11, FR-19, FR-20 | US-14 | Review Pending applications and approve/reject; approval enforces eligibility, quota, and single-approval rules. |
| 14 | Animal Status History | Shelter Owner | FR-21 | US-15 | Show previous/new status, actor user ID, and timestamp. API does not provide actor display name. |
| 15 | Handover | Shelter Owner | FR-12 | US-09 | Record physical handover for a Reserved animal; only then it becomes Adopted. |
| 16 | User Management | Admin | FR-06 | US-04 | List users and manage ban/unban. Banning auto-rejects Pending applications; previously Approved applications remain unchanged. |
| 17 | Shelter Registration Requests | Admin | FR-03, FR-04 | US-02, US-03 | List shelter registration requests and open a selected request. |
| 18 | Shelter Registration Review | Admin | FR-03, FR-04 | US-02, US-03 | Review selected request; approve/reject only while Pending. |

### 5.1 FR coverage

- FR-01–FR-02: Screens 1–2
- FR-03–FR-04: Screens 17–18
- FR-05: Screen 9 (Admin moderation, shared role-based screen)
- FR-06: Screen 16
- FR-07: Screen 8
- FR-08: Screen 10
- FR-09: Screens 9 and 11
- FR-10–FR-11 and FR-19–FR-20: Screens 12–13
- FR-12: Screen 15
- FR-13–FR-14: Screen 3
- FR-15: Screen 4
- FR-16: Screen 5
- FR-17–FR-18: Screens 6–7
- FR-21: Screens 7 and 14

## 6. Screen-to-API mapping

The operation names below match the `operationId` values in `api/openapi.yaml` (OpenAPI 0.2.0). All 25 expected operation IDs were checked against the `develop` branch OpenAPI file; no expected operation was missing.

| Screen | Role | OpenAPI operations |
|---|---|---|
| 1 – Login | All | `loginUser` |
| 2 – Register | Guest | `registerUser` |
| 3 – Animal Listing | Adopter | `listAnimals` |
| 4 – Animal Detail | Adopter | `getAnimal` |
| 5 – Adoption Application Form | Adopter | `submitApplication` |
| 6 – My Applications | Adopter | `listApplications` |
| 7 – Application Detail + Withdraw | Adopter | `getApplication`, `withdrawApplication` |
| 8 – Shelter Profile | Shelter Owner | `getMyShelterProfile`, `createShelterProfile`, `updateMyShelterProfile` |
| 9 – Animal Management | Shelter Owner, Admin | `listAnimals`, `archiveAnimal` |
| 10 – Add Animal | Shelter Owner | `createAnimal` |
| 11 – Edit Animal | Shelter Owner | `getAnimal`, `updateAnimal` |
| 12 – Application Management | Shelter Owner | `listApplications` |
| 13 – Application Review | Shelter Owner | `getApplication`, `approveApplication`, `rejectApplication` |
| 14 – Animal Status History | Shelter Owner | `getAnimalStatusHistory` |
| 15 – Handover | Shelter Owner | `recordHandover` |
| 16 – User Management | Admin | `listUsers`, `banUser`, `unbanUser` |
| 17 – Shelter Registration Requests | Admin | `listShelterRegistrationRequests` |
| 18 – Shelter Registration Review | Admin | `approveShelterRegistration`, `rejectShelterRegistration` |
| All protected screens | All | `logoutUser` |

### 6.1 Application history vs. animal history

- Screen 7 uses `getApplication`; its `ApplicationDetail.statusHistory` represents the application's Pending → Approved / Rejected / Withdrawn transitions.
- Screen 14 uses `getAnimalStatusHistory`; it represents the animal's own status transitions.
- Both histories expose the actor as a user ID and include a timestamp; the current API does not provide an actor display name.

## 7. Confirmation dialogs and state-changing actions

| Dialog | Trigger | Confirm action | Cancel behavior |
|---|---|---|---|
| Withdraw Application | Adopter withdraws a Pending application | `withdrawApplication`; status becomes Withdrawn | Close; no request |
| Archive Animal | Shelter Owner/Admin archives an Available animal within their permissions | `archiveAnimal`; status becomes Archived | Close; no request |
| Approve Application | Shelter Owner approves a Pending application | `approveApplication`; application Approved, animal Reserved | Close; no request |
| Reject Application | Shelter Owner rejects a Pending application | `rejectApplication`; application Rejected | Close; no request |
| Record Handover | Shelter Owner records handover for a Reserved animal | `recordHandover`; animal becomes Adopted | Close; no request |
| Ban User | Admin bans a user | `banUser`; pending applications of a banned Adopter auto-rejected | Close; no request |
| Unban User | Admin unbans a user | `unbanUser` | Close; no request |
| Approve Shelter | Admin approves a Pending shelter request | `approveShelterRegistration`; shelter Approved | Close; no request |
| Reject Shelter | Admin rejects a Pending shelter request | `rejectShelterRegistration`; shelter Rejected | Close; no request |

## 8. Validation and error states

Error responses use the shared structure `{status, error, message}`.

| HTTP | Meaning | Expected UI behavior |
|---:|---|---|
| 400 | Invalid input, including invalid image URL where specified | Show field/form validation and allow correction. |
| 401 | Invalid credentials or expired/invalid authentication | Show login error or end expired session and return to Login. |
| 403 | Authenticated user is not permitted to perform the operation | Show access denied; do not proceed. Examples: banned Adopter submits application; non-Approved Shelter Owner manages animals/applications. |
| 404 | Resource not found | Show not-found state and safe route back to relevant list. |
| 409 | State conflict (e.g. application no longer Pending, animal no longer Available, concurrent approval) | Explain that data changed and refresh relevant resource/list. |
| 422 | Adoption eligibility requirements not met | Explain the specific missing document, apartment permission, or three-animal limit issue. |

### 8.1 Adoption-specific rules

- `submitApplication` may return `403` for a banned Adopter, `409` if the animal is no longer Available, or `422` for a missing required document/apartment permission.
- The three-animal rule is checked during **approval**, not submission. An Adopter may have at most three animals with Reserved or Adopted status combined.
- `approveApplication` may return `422` for missing mandatory documents, missing apartment permission when applicable, or exceeding the three-animal limit.
- `approveApplication` may return `409` if the application is no longer Pending, the animal is no longer Available, or another approval completed concurrently.
- Only one application can be approved for an animal. Other Pending applications for that animal are automatically rejected when one application is approved.
- An animal becomes Adopted only after physical handover is recorded.

### 8.2 Ban and status-history behavior

- When an Adopter is banned, all their Pending adoption applications are automatically rejected. Previously Approved applications remain unchanged.
- Automatic rejections are recorded in status history with the Admin as responsible user.
- Unbanning does not restore automatically rejected applications.
- Status history records actor user ID, previous status, new status, and timestamp.

## 9. Exported assets and repository paths

All 18 main-screen PNG exports supplied for this handoff are included under `docs/assets/ui-ux/screens/` with stable filenames. Four user-flow diagrams are included under `docs/assets/ui-ux/flows/`. Additional dialog and error-state exports are included where they were available in the prepared design assets.

### 9.1 Main screens

| No. | Asset |
|---:|---|
| 1 – Login | `docs/assets/ui-ux/screens/01-login.png` |
| 2 – Register | `docs/assets/ui-ux/screens/02-register.png` |
| 3 – Animal Listing | `docs/assets/ui-ux/screens/03-animal-listing.png` |
| 4 – Animal Detail | `docs/assets/ui-ux/screens/04-animal-detail.png` |
| 5 – Adoption Application Form | `docs/assets/ui-ux/screens/05-adoption-application-form.png` |
| 6 – My Applications | `docs/assets/ui-ux/screens/06-my-applications.png` |
| 7 – Application Detail | `docs/assets/ui-ux/screens/07-application-detail.png` |
| 8 – Shelter Profile | `docs/assets/ui-ux/screens/08-shelter-profile.png` |
| 9 – Animal Management | `docs/assets/ui-ux/screens/09-animal-management.png` |
| 10 – Add Animal | `docs/assets/ui-ux/screens/10-add-animal.png` |
| 11 – Edit Animal | `docs/assets/ui-ux/screens/11-edit-animal.png` |
| 12 – Application Management | `docs/assets/ui-ux/screens/12-application-management.png` |
| 13 – Application Review | `docs/assets/ui-ux/screens/13-application-review.png` |
| 14 – Animal Status History | `docs/assets/ui-ux/screens/14-animal-status-history.png` |
| 15 – Handover | `docs/assets/ui-ux/screens/15-handover.png` |
| 16 – User Management | `docs/assets/ui-ux/screens/16-user-management.png` |
| 17 – Shelter Registration Requests | `docs/assets/ui-ux/screens/17-shelter-registration-requests.png` |
| 18 – Shelter Registration Review | `docs/assets/ui-ux/screens/18-shelter-registration-review.png` |

### 9.2 User-flow diagrams

- `docs/assets/ui-ux/flows/flow-authentication.png`
- `docs/assets/ui-ux/flows/flow-adopter.png`
- `docs/assets/ui-ux/flows/flow-shelter-owner.png`
- `docs/assets/ui-ux/flows/flow-admin.png`

### 9.3 Supporting states

Supporting dialog and error-state PNGs are stored in the same `screens/` directory. They are supplementary to the 18 numbered main screens; they do not increase the agreed main-screen count.

## 10. Final handoff checklist

- [x] Include all 18 numbered main-screen PNG exports.
- [x] Include the four canonical user-flow diagrams.
- [x] Document all 18 screens with FR and User Story traceability.
- [x] Map screens to OpenAPI 0.2.0 operations; all 25 expected operation IDs were checked against `develop`.
- [x] Record confirmation dialogs, validation/error behavior, and adoption business rules.
- [x] Apply the accepted FR-05 decision: Admin and Shelter Owner share Screen 9 with role-based permissions.
- [x] Add the Pawradise Figma prototype URL.
- [ ] Copy `docs/09-ui-ux.md` and `docs/assets/ui-ux/` into the project repository and verify Markdown image rendering there.

