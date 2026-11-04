# Sprint 1 Decision Log (Online)

Besides the kickoff meeting, the team coordinated daily in its WhatsApp group.
This log records the decisions and agreements taken there. Where the exact date
of a message is not recorded, the date range is given.

Recorded by: Şevval Sıla Çelik (Scrum Master)

## Decisions

| # | Date | Topic | Decision | Agreed by | Where it is applied |
|---|---|---|---|---|---|
| L1 | 29 Oct – 2 Nov 2026 | Team roles | Işıl: Lead Software Architect; İlayda: Product Owner; Sıla: Scrum Master; Efekan: QA; İlay: DevOps; Fatma: Implementation and Integration | All members | `docs/05-team-raci.md` |
| L2 | 29 Oct – 2 Nov 2026 | Project name | **Pawradise** (proposed by Fatma Nur Kodak, selected by the team) | All members | All documents |
| L3 | 29 Oct – 2 Nov 2026 | Other applications after an approval | Other Pending applications for the same animal are **automatically rejected** (a waiting list is not part of the first release) | All members | BR-02, FR-19 |
| L4 | 29 Oct – 2 Nov 2026 | Open-source license | **Apache License 2.0** | All members | `LICENSE`, Project Proposal |
| L5 | 29 Oct – 2 Nov 2026 | User roles | The system has exactly three roles: **Admin, Shelter Owner, Adopter**. A Volunteer role was discussed but is not part of the first release. | All members | Official Requirements Reference |
| L6 | 29 Oct – 2 Nov 2026 | Requirements | The **Official Requirements Reference** (FR-01 … FR-21, NFR-01 … NFR-09, BR-01 … BR-09) was shared by Işıl and accepted by the team as the single source of truth | İlay, İlayda, Sıla confirmed in the group | All documents |
| L7 | 2 Nov 2026 | Repository | İlay created the GitHub organization and repository and invited all members | All members | Repository |
| L8 | 3 Nov 2026 | Timeline | All work is pushed to GitHub by Wednesday 4 Nov, 18:00–19:00, followed by a final team review; submission on Thursday 5 Nov | All members | Sprint 1 plan |
| L9 | 3 Nov 2026 | QA tasks | QA responsibilities for Sprint 1 were listed: requirements review, acceptance criteria review, test scenario planning, API contract review, traceability review, and PR reviews | Fatma, Efekan | `docs/05-team-raci.md` §3.4 |
| L10 | Sprint 1 | BR-07 and BR-08 clarifications | Pending applications of a banned adopter are automatically rejected (BR-07). Animals with status Reserved or Adopted count toward the three-animal limit (BR-08). | Proposed by Işıl in the System Vision; approved through PR #5 review | `docs/02-vision-boundary.md` §5.7, `api/openapi.yaml` |

## Raised Concerns

| Date | Raised by | Concern | Response |
|---|---|---|---|
| 3 Nov 2026 | Efekan Coşkun | Testing cannot be done if the work arrives in the last hour before submission. | The team agreed to finish by Wednesday evening so that there is time for review and testing (L8). |

## Open Questions

Open requirement questions are tracked in `docs/02-vision-boundary.md` §11 (OI-01 … OI-07) and are not changed without a team decision.
