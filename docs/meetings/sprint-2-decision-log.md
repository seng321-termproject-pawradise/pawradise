# Sprint 2 Planning and Decision Log (Online)

| Field | Value |
|---|---|
| Sprint | Sprint 2 (SRS, NFRs, UI/UX prototypes, architecture) |
| Format | Online, in the team's WhatsApp group (no separate meeting was held) |
| Period | 17–19 November 2026 |
| Decision list and task list prepared by | Işıl Gültekin (Lead Software Architect) |
| Recorded by | Şevval Sıla Çelik (Scrum Master) |
| Sprint 2 submission | 24 November 2026, 23:59 |

The decisions and the task assignment below were shared in the team group and are recorded here so that they are traceable in the repository.
Dates in the task list are internal targets and may be updated if needed.
Decision numbers follow the team's list; K5 (calendar) is intentionally not used because the dates are recorded as targets in the task list.

## Decisions

| # | Topic | Decision | Applied in |
|---|---|---|---|
| K1 | Architecture | Modular monolith with React, FastAPI, and MySQL. Layers: Router → Service → Repository. Authentication with JWT and bcrypt password hashing. Three containers with docker-compose. | `docs/06-architecture.md` (#53) |
| K2 | Shelter registration statuses (closes OI-04) | A shelter profile has the statuses Pending → Approved / Rejected. A Shelter Owner can log in; until the profile is Approved, the owner can only create, view, and edit their own profile. Animal and application operations return 403. | `docs/02-vision-boundary.md`, `docs/07-srs.md`, `api/openapi.yaml` (#40, #25) |
| K3 | Rejected shelters | A Rejected shelter cannot apply again. This is not part of the Sprint 2 scope. | `docs/07-srs.md` |
| K4 | File names | `docs/06-architecture.md` (Işıl), `docs/07-srs.md` (İlayda), `docs/08-nfr.md` (İlayda and Efekan), `docs/09-ui-ux.md` (Fatma) | All Sprint 2 documents |
| K6 | SRS | The SRS follows a simplified ISO/IEC/IEEE 29148 structure. FR-01 … FR-21 are final, and every FR references its source user story. | `docs/07-srs.md` (#55) |
| K7 | Figma prototypes | The screen list follows the OpenAPI contract. Confirmation dialogs and automatic-rejection warnings are included; there are no dashboard or user profile screens. `GET /admin/users` is added to PR #25. | Figma, `docs/09-ui-ux.md`, `api/openapi.yaml` (#25) |
| K8 | Deployment | Only Nginx (port 80) is exposed. Requests to `/api/` are forwarded to FastAPI. Backend and MySQL ports are not exposed. Secrets are kept in a `.env` file; only `.env.example` is committed. MySQL uses a named volume. | Deployment plan, `docs/06-architecture.md` |
| K9 | Access token | Access token lifetime is 30 minutes; there is no refresh token. After expiry the API returns 401 and the user logs in again. | `docs/08-nfr.md` (#52), `api/openapi.yaml` (`expiresIn: 1800`) |

## Task Assignment

### Işıl Gültekin – Lead Software Architect

| Task | Location | Target |
|---|---|---|
| Domain model | `docs/06-architecture.md` | 18 Nov |
| Complete architecture document (ADRs, C4, database schema, UML, traceability) | `docs/06-architecture.md` (#53, PR #54) | 21 Nov |
| Admin account provisioning note (A-03) | `docs/03-api-flows.md` (#46) | 21 Nov |
| Update A-05 and close OI-04 | `docs/02-vision-boundary.md` (#40) | 21 Nov |
| Reviews of PR #52 (NFR) and PR #25 (OpenAPI) | Pull request reviews | 19–20 Nov |
| Cross-document consistency check | All documents | 22 Nov |

### Fatma Nur Kodak – Implementation and Integration Engineer (UI/UX and API)

| Task | Location | Target |
|---|---|---|
| Add `GET /admin/users`, update FR tags, `expiresIn: 1800`, OpenAPI version 0.2.0 | `api/openapi.yaml` (PR #25, #19) | 20 Nov |
| Figma screens and user-flow diagrams | Figma | 21 Nov |
| UI/UX document (Figma link, screen–endpoint mapping, flows, error screens, images) | `docs/09-ui-ux.md` | 21 Nov |

### İlayda Erşin – Product Owner

| Task | Location | Target |
|---|---|---|
| Software Requirements Specification, including the K2 constraint | `docs/07-srs.md` (#55, PR #56) | 21 Nov |
| Create the user stories as GitHub issues | GitHub Issues | 19 Nov |
| Backlog update | `docs/04-backlog.md` (#39, #40) | 21 Nov |
| Support for the NFR document | `docs/08-nfr.md` | 20 Nov |

### Efekan Coşkun – Quality Assurance Engineer

| Task | Location | Target |
|---|---|---|
| NFR document: review corrections, 30-minute token lifetime (K9), remove `APP_KEY` | `docs/08-nfr.md` (#51, PR #52) | 20 Nov |
| Rename `image.png` to `swagger-editor-validation.png` | `docs/evidence/` (#49, PR #50) | Done (17 Nov) |

### Aslınur İlay Tekin – DevOps Engineer

| Task | Location | Target |
|---|---|---|
| Deployment plan (update token lifetime to 30 minutes) | Deployment plan | Done; update pending |
| Preparation of docker-compose and CI | – | Sprint 3 |

### Şevval Sıla Çelik – Scrum Master

| Task | Location | Target |
|---|---|---|
| Record the decisions | `docs/meetings/sprint-2-decision-log.md` | 18 Nov |
| Sprint 2 board and RACI update | Project board, `docs/05-team-raci.md` | 19 Nov |
| Update individual contributions | `CONTRIBUTIONS.md` | 23 Nov |
| Merge `develop` into `main` and create the `sprint-2` tag | Release | 23 Nov |

### All Members

| Task | Target |
|---|---|
| Cross-review: every member reads another member's document | 22 Nov |
| Final pull requests and merges | 23 Nov |

## Status on 19 November 2026

| Item | Status |
|---|---|
| Architecture document (PR #54) | Open, waiting for review |
| SRS (PR #56) | Open, changes requested |
| NFR document (PR #52) | Open, reviewed with comments |
| OpenAPI update (PR #25) | Open, reviewed with comments |
| Swagger evidence rename (PR #50) | Merged |
