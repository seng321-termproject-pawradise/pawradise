**Course:** SENG 321 Software Engineering \| **Role:** Product Owner & Requirements Engineer **Deliverable:** docs/01-proposal.md (Version 1.4 - Final Sprint 1) \| **License:** Apache License 2.0

# 1. Problem Statement & Supporting Evidence

Across municipal and non-profit animal welfare ecosystems, companion animal shelters encounter severe operational and administrative bottlenecks. Stray, surrendered, and abandoned domestic animal populations consistently outpace municipal shelter intake capacities and volunteer staffing. Many small-to-midsize shelters rely on fragmented workflows such as paper logbooks, unlinked spreadsheets, and ad-hoc social media posts. This creates four problems that the proposed system addresses:

**1. Slow and inconsistent application handling:** When several people apply for the same animal, manual screening is slow and it is easy to approve more than one applicant or to lose track of who applied.

**2. Informal housing checks:** Landlord or apartment permission for dog adoption is often checked informally or after the adoption, although housing is a known reason for relinquishment (see Applebaum et al., 2024 below).

**3. No reliable audit trail:** Manual records do not show reliably who listed an animal, who approved or rejected an application, or when an animal was handed over.

**4. Outdated availability information:** Adopters cannot see the current status of an animal and may apply for animals that are already reserved or adopted.

## Citations & Verifiable Supporting Evidence:

• Shelter Animals Count (SAC, 2023): Full Year 2023 Report (https://www.shelteranimalscount.org/stats). In 2023, approximately 6.5 million cats and dogs entered shelters and rescues across the United States. The figure refers to U.S. intake, not North America as a whole.

• Scarlett, J. M., Salman, M. D., New, J. G., Jr., & Kass, P. H. (1999): Reasons for relinquishment of companion animals in U.S. animal shelters: Selected health and personal issues. Journal of Applied Animal Welfare Science, 2(1), 41–57. https://doi.org/10.1207/s15327604jaws0201_4. The study examines health- and personal-related reasons for relinquishment; it does not establish a platform-specific administrative delay.

• Applebaum, J. W., Loney, L., Horecka, K., & Graham, T. M. (2024): Housing-related companion animal relinquishment across 21 animal shelters in the United States from 2019–2023. Frontiers in Veterinary Science, 11, 1430388. https://doi.org/10.3389/fvets.2024.1430388. Housing-related issues accounted for approximately 14% of intakes in the study dataset (21 U.S. shelters); this is not a nationwide estimate.

# 2. Stakeholder Identification and Analysis

|                                     |                         |                                                                                                                                                                  |                        |
|-------------------------------------|-------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------|
| **Stakeholder Group**               | **Classification**      | **Key Responsibilities & Needs**                                                                                                                                 | **Impact / Influence** |
| **System Administrator (Admin)**    | Internal Primary        | Platform governance, auditing shelter registration requests, issuing approvals/rejections, moderating inappropriate listings, managing user suspensions.         | High / High            |
| **Shelter Owners & Staff**          | External Primary        | Managing shelter profiles, publishing animal listings with public Image URLs, evaluating adoption applications, executing single approvals, recording handovers. | High / High            |
| **Prospective Adopters**            | External Primary        | Browsing/filtering available animals, submitting applications with document links, tracking application statuses, managing voluntary withdrawals.                | Medium / High          |
| **Sheltered Animals**               | Indirect Beneficiaries  | Beneficiaries of shortened shelter stays, verified apartment suitability, and protection against hoarding through the 3-pet limit.                               | Low / High             |
| **Course Instructors & Evaluators** | Institutional Secondary | Evaluating software engineering rigor, Git-Flow discipline, architectural consistency, test coverage, and automated deployment.                                  | High / Medium          |

# 3. Proposed Solution & Strict State Transition Model

**• Strict Animal Transitions:** Available -\> Reserved -\> Adopted (via physical handover) OR Available -\> Archived (via soft-delete/moderation). Reserved -\> Archived is strictly not permitted. The procedure for a Reserved animal whose handover does not take place (for example, a return to Available) is not yet defined and is tracked as OI-01 (Open); no such transition is assumed in Sprint 1.

**• Strict Application Transitions:** Pending -\> Approved OR Pending -\> Rejected OR Pending -\> Withdrawn.

**• BR-08 Pet Quota Rule:** An adopter shall not have more than three animals in total with Reserved or Adopted status at the same time. Available and Archived animals do not count. Strictly evaluated at approval time; aborts with 422 Unprocessable Content if violated. *(Team decision, 3 Nov 2026; recorded in System Vision §5.7 and the Sprint 1 decision log, L10; closes OI-02.)*

**• BR-07 User Ban Rule:** When an adopter is banned, all of their Pending applications are automatically rejected; Approved applications remain unchanged. The ban and the automatic rejections run in the same database transaction. *(Team decision, 3 Nov 2026; recorded in System Vision §5.7 and the Sprint 1 decision log, L10; closes OI-03.)*

**• Atomic Single Approval & Handover:** Approving one application marks the animal Reserved and auto-rejects competing pending applications. Final transition to Adopted occurs strictly upon physical handover.

# 4. Measurable Goals and Expected Outcomes

1\. Streamline the review workflow to enable shelter evaluation decisions within 48 hours of application submission. 2. Achieve 0% double-approval error rate guaranteed by atomic database transactions. 3. 100% compliance verification on apartment permission documents for dog adoptions. 4. 100% enforcement of BR-08: an adopter shall not have more than three animals in total with Reserved or Adopted status at the same time. 5. 100% audit traceability for animal inventory and application state transitions. 6. API response latency strictly under 3 seconds for animal catalog search and filtering requests (aligned with NFR-01).

# 5. Feasibility Assessment

**• Technical Feasibility:** API-first RESTful architecture specified in OpenAPI 0.1.0 with containerized execution and ACID relational database.

**• Operational Feasibility:** Directly mirrors standard shelter workflows (intake -\> listing -\> review -\> reservation -\> handover).

**• Economic & Schedule Feasibility:** 100% free open-source stack. Structured across a 10-week sprint timeline aligned with course milestones.

# 6. Initial Project Scope & Official Out-of-Scope List

**In-Scope:** RBAC authentication, Shelter registration and approval, Animal inventory CRUD with image URLs and archiving, Animal discovery & filtering (species and age), Adoption application lifecycle, Business rules enforcement (BR-07, BR-08, apartment permission), Physical handover logging, and status history tracking.

**Official Out-of-Scope List (8 Core Items):** 1. Live chat / real-time messaging, 2. Online payment / donation processing, 3. AI-based animal recommendations, 4. GPS tracking, 5. Native mobile applications (responsive web only), 6. Social media integration, 7. Advanced analytics / BI reporting, 8. Direct image file uploads (external public URLs only). (Hardware microchip integration and permanent DB deletions are likewise excluded).

# 7. Assumptions (A-01 - A-09) and Potential Risks

Project Assumptions (aligned with System Vision; unresolved items remain assumptions):  
A-01: Users access the system through a modern web browser with an internet connection.  
A-02: Animal image URLs point to external image hosts; the system stores URLs rather than uploading image files.  
A-03: Admin accounts are provisioned by the team (e.g., seeded), not through public registration (to be confirmed).  
A-04: Shelter Owners publish listings only after Admin approval of shelter registration (to be confirmed).  
A-05: Adopters declare apartment residence for BR-06; whether this is stored in the profile or each application remains to be confirmed.  
A-06: Required documents are submitted as document links or references because direct uploads are out of scope (to be confirmed).  
A-07: Automatic rejections under BR-02 record the approving Shelter Owner as the responsible actor in status history (to be confirmed).  
A-08: Status history records cannot be edited or deleted.  
A-09: FR-20 eligibility covers BR-05 required documents, BR-06 apartment permission, and BR-08 adoption limit. BR-07 blocks banned adopters from applying and requires their Pending applications to be rejected upon banning; Approved applications stay unchanged. BR-08 counts Reserved and Adopted animals toward the limit.

|             |                                                                                                                                                               |              |                |            |                                                                                                                                                                                                              |
|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------|----------------|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Risk ID** | **Risk Description**                                                                                                                                          | **Category** | **Likelihood** | **Impact** | **Mitigation Strategy**                                                                                                                                                                                      |
| **RSK-01**  | Concurrent Approval Race Condition: Multiple shelter staff attempt to approve different applications for the same animal concurrently.                        | Technical    | Medium         | High       | Implement atomic database transactions with row-level locking. First commit succeeds; subsequent attempts fail with 409 Conflict.                                                                            |
| **RSK-02**  | Broken or Inaccessible Image URLs: Users supply malformed or expired external image links.                                                                    | Technical    | High           | Low        | Validate URL syntax against the OpenAPI `uri` format (returning 400 Bad Request on malformed inputs) and provide graceful fallback placeholder images on client UI.                                                     |
| **RSK-03**  | Hoarding & Quota Bypass (BR-08 Violation): Adopters submitting multiple simultaneous applications across different shelters attempting to exceed 3-pet quota. | Functional   | Medium         | High       | Strictly enforce BR-08 at the approval stage within the approval transaction: recalculate the adopter's Reserved and Adopted animals; abort approval with 422 Unprocessable Content if quota would exceed 3. |
| **RSK-04**  | Scope Creep & Feature Bloat: Team members introducing payment gateways, live chat, or binary upload pipelines.                                                | Process      | Medium         | High       | Maintain rigorous Product Owner scope control; enforce the eight official Out-of-Scope boundaries defined in Section 6.                                                                                    |
| **RSK-05**  | Branch Desynchronization & PR Conflicts: Simultaneous divergent changes to shared schemas or endpoints.                                                       | Process      | High           | Medium     | Enforce Git-Flow with branch protection rules requiring at least 1 approving review before merging into develop.                                                                                             |

# 8. Open Issues (Cross-Referenced with System Vision Baseline)

|           |                                                                                                                                                                     |                                                             |
|-----------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------|
| **ID**    | **Issue Description**                                                                                                                                               | **Status**                                                  |
| **OI-01** | Undefined Exit from Reserved Status: If an approved adopter abandons the adoption prior to physical handover, the procedure for canceling reservation is undefined. | **Open**                                                    |
| **OI-02** | Count of Reserved Animals in Adoption Quota: Clarification on whether animals in Reserved status count toward the adopter's 3-animal limit.                         | Closed: Reserved animals count toward limit (BR-08)         |
| **OI-03** | Status of Pending Applications for Post-Banned Users: Handling policy for existing pending applications submitted by a user who is subsequently banned.             | Closed: Pending applications automatically rejected (BR-07) |
| **OI-04** | Shelter Registration Statuses: Clarification of full state machine and allowed transitions for shelter registration requests.                                       | **Open**                                                    |
| **OI-05** | Supported Species: Determination of the complete enumeration of supported animal species beyond dogs and cats.                                                      | **Open**                                                    |
| **OI-06** | Interpretation of 'Remove' in FR-05: Clarification on whether administrative action signifies soft archival, de-listing, or record deletion.                        | **Open**                                                    |
| **OI-07** | Mandatory Documents List: Specification of definitive list of mandatory documents required prior to approval beyond apartment authorization.                        | **Open**                                                    |

**Additional Open Issues (Pending Team Discussion):** Document MIME type validation, Reservation holding period expiration timeout, Multi-shelter staff relationships.

# 9. License Decision: Apache License 2.0

**Selected License:** Apache License 2.0 (OSI Approved)

**1. Explicit Patent Rights Protection (Section 3):** Includes an explicit contributor patent grant and patent retaliation clause, protecting downstream adopters, universities, and non-profit animal shelters from patent infringement claims.

**2. Trademark Protection (Section 6):** Explicitly reserves trademark rights, preventing external parties from commercializing the software using the project's name or shelter brand assets without authorization.

**3. Attribution and Modification Notices (Section 4):** Requires redistributions to keep copyright and license notices and to mark modified files. Individual contributions in the course are traced through the Git history (commits, issues, and pull requests).

**4. Enterprise & Non-Profit Suitability:** Widely adopted by enterprise foundations and municipalities, eliminating legal barriers for real-world municipal animal shelter deployment.
