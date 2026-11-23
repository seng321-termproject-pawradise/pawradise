# Non-Functional Requirements and Verification Plan

## Status and Scope

Status: Draft for team review.
First draft date: 18 November 2026.
Related issue: #51.
Related consistency issue: #40.

This document defines measurable quality targets and planned verification
methods. It does not report executed tests or achieved results.

Existing NFR identifiers retain their meanings. Requirements not covered
here remain applicable. NFR-10 is an approved addition for deployment portability.

The architectural decisions supplied for this draft are bcrypt password
hashing, JWT authentication, row-level locking during approval, and
Docker Compose deployment. Their implementation must be verified.

## Common Test Conditions

Approved reference environment:
- Application and database: Docker Compose.
- Available resources: at least 2 CPU cores and 4 GB RAM.
- Record the OS, hardware, Docker version, image versions, and commit SHA.
- Use synthetic data and test accounts.
- Use a clean database or reset fixtures between independent scenarios.
- Do not store real passwords, signing keys, or personal data in evidence.

Performance results from different environments must not be treated as
directly equivalent.

## NFR-01 — Performance

### Existing requirement

Standard pages shall load within 3 seconds under normal conditions.

### Proposed measurable API target

For authenticated GET /animals requests:
- Dataset: 1,000 animal records.
- Load: 20 virtual users, each waiting 1 second after each response.
- Warm-up: 1 minute.
- Measurement period: 5 minutes.
- Response-time p95: below 1,000 ms.
- Unexpected response or transport error rate: below 1%.

The test shall exercise unfiltered listing and species/age filtering.
Each request type shall meet the target separately.

Record query parameters, returned record counts, response sizes, and
cache conditions. If pagination is introduced, record the page size.

### Verification

Tool: k6.

Check response status, expected response structure, latency, and error
rate. Authentication setup is outside the timed listing requests.

Verify the existing page-loading target separately using Playwright:
perform 30 page loads per agreed standard page and require each load
to complete within 3 seconds. Completion means that the main content
is visible and the page's primary action is usable.

The standard-page list, browser version, network conditions, and
external-image behavior must be agreed before page tests are executed.

The API target supplements the page-loading requirement; it does not
replace it.

Evidence: k6 summary, Playwright results, environment details, and SHA.

## NFR-02 — Password Storage Security

### Target

- All stored user passwords shall use salted bcrypt hashes.
- Registration and password-change flows, where implemented, shall
  store no plaintext passwords.
- Passwords and password hashes shall not appear in API responses.
- Plaintext passwords shall not appear in application logs.
- Equal passwords assigned to different accounts shall produce
  different hashes, while both remain verifiable.
- Approved bcrypt work factor: 12.

### Verification

Tools: backend integration-test framework and database/log inspection.

Create 10 synthetic accounts, including two with the same password.
Inspect the stored algorithm and work factor, verify correct-password
acceptance and incorrect-password rejection, and inspect responses
and logs for the synthetic password values.

Repeat relevant checks for password changes if that flow is in scope.

Pass criterion: all checks pass; zero plaintext-password exposures
are observed in the tested flows.

Evidence: test report and redacted inspection results.

## NFR-03 — Authentication and Authorization

### Target

- Protected operations shall require a valid JWT.
- Missing, expired, malformed, or tampered tokens shall return 401.
- Authenticated users with an unauthorized role or resource ownership
  shall receive 403, consistent with the API contract.
- Failed authorization shall cause no protected data modification
  or disclosure.
- Access-token lifetime: 30 minutes.
- Configuration: ACCESS_TOKEN_EXPIRE_MINUTES=30.
- Issued tokens shall contain iat and exp, with exp - iat = 1,800 seconds.
- Clock tolerance is 0 seconds. Tokens shall be rejected at or after exp.

### Verification

Tools: backend integration tests and Postman/Newman.

Test:
1. A valid token on an allowed operation.
2. Missing, malformed, tampered, and expired tokens.
3. Each relevant role against protected operations.
4. An adopter accessing another adopter's application.
5. A shelter owner modifying another shelter's resource.
6. Token lifetime matching the configured value.
7. Requests immediately before and at the expiration boundary.

Use a controllable test clock or generated test tokens for expiration
tests; a 30-minute real-time wait is not required.

Pass criterion: all authentication, authorization, and expiration
cases match the expected responses and database effects.

Evidence: test matrix and redacted test results.

### Confirmed session behavior

Decision K9 confirms a 30-minute access-token lifetime.
Configuration: ACCESS_TOKEN_EXPIRE_MINUTES=30.
The API expiresIn value is 1,800 seconds.

Refresh tokens are not supported. After expiry, protected operations
return 401 and the user must log in again.

JWT signing uses JWT_SECRET. Real signing keys must not be committed
or included in test evidence.

## NFR-04 — Usability

### Official requirement

The system shall provide a simple and user-friendly interface.

### Measurable targets

For each critical flow, at least 4 out of 5 test users shall complete
the task without assistance:
- Registration and login.
- Submitting an adoption application.
- Approving an adoption application.
- Submitting a shelter profile for registration review.

Before withdrawal, archiving, approval, rejection or banning, the UI
shall display a confirmation dialog explaining the action and its
consequences. Cancelling shall leave the affected data unchanged.

Navigation labels, placement and behavior shall remain consistent
across screens within each role. Role-specific options may differ
according to permissions.

The interface shall follow the agreed designs in docs/09-ui-ux.md.

### Verification

Tools: moderated usability sessions, an observation checklist,
screen recordings with participant consent, and Playwright UI tests.

Use five representative test users and synthetic data. Provide each
user with the appropriate role and prerequisites for each task.
Give the same task instructions without explaining the interface.

For each user and flow, record:
- Whether the task was completed correctly.
- Whether assistance was needed.
- Errors, abandonment and completion time.

A task requiring a hint or intervention counts as assisted.
Completion time is recorded for analysis; no time threshold is imposed.

Pass criterion: at least 4 of 5 users complete each flow correctly
without assistance. Results shall not be averaged across flows.

Test confirmation and cancellation for every listed action.
Verify that cancellation sends no mutation request and changes no data.
For approval and banning, check that the dialog explains automatic
rejection of affected pending applications.

Inspect navigation across every implemented screen against the
agreed UI/UX designs.

Pass criterion: all confirmation, cancellation and navigation checks pass.

Evidence: anonymized usability results, checklist, UI test report
and screenshots.
Execution status: Not executed.

## NFR-05 — Consistent Error Handling and User Feedback

### Target

All API error responses shall follow the shared ErrorResponse schema:
- status: integer matching the HTTP response status.
- error: non-empty error code.
- message: non-empty, understandable explanation.

Errors shall not expose passwords, JWT signing keys, database credentials,
SQL queries, or stack traces.

The UI shall display an understandable outcome for successful and failed
operations. An unsuccessful operation shall not display a success message.

### Verification

Tools: pytest integration tests, Postman/Newman, and Playwright.

Create an endpoint-to-error-case matrix covering every documented error
response in the OpenAPI contract, including 400, 401, 403, 404, 409 and 422
where applicable. Validate the HTTP status, body schema and message.

Use controlled failures in the test environment to check that unexpected
server errors do not expose implementation details.

For each implemented user flow, test one successful operation and each
distinct user-visible failure outcome. Verify that feedback matches the
actual API result.

Pass criterion: every case in the agreed matrix passes, with no sensitive
information exposed in the inspected responses.

Evidence: case matrix, automated test report and UI screenshots.
Execution status: Not executed.

## NFR-06 and NFR-07 — Browser Compatibility and Responsive UI

### Scope

The interface shall support commonly used browsers and remain usable
on desktop, tablet and mobile screens.

The browser and viewport matrix below is proposed for team approval.
These are planned verification conditions, not completed test results.

### Proposed measurable targets

Browser matrix:
- Google Chrome, Microsoft Edge and Mozilla Firefox on Windows.
- Safari on macOS.
- Record the exact browser and operating-system versions at execution.

Viewport matrix, in CSS pixels:
- Mobile: 360 × 800.
- Tablet: 768 × 1024.
- Desktop: 1440 × 900.

For each browser at the desktop viewport, all agreed critical user-flow
tests shall pass without browser-specific functional failures.

For each viewport:
- Required content and controls shall remain visible and accessible.
- Text and controls shall not overlap or be clipped.
- The page shall not require horizontal scrolling.
- Wide tables may scroll within their own clearly accessible container.
- Forms, validation messages and confirmation dialogs shall remain usable.
- Navigation shall allow users to reach every authorized screen.

### Verification

Tools: Playwright, browser developer tools and manual browser testing.

Test the following flows with authorized synthetic accounts:
- Login and logout.
- Browse and filter animals.
- View animal details and submit an application.
- Track and withdraw an application.
- Review applications and approve or reject them.
- Manage animal listings and record handover.
- Review shelter registrations and ban or unban users.

Run the applicable flows on each desktop browser. Repeat the flows at
all three viewport sizes using Playwright's Chromium, Firefox and WebKit
engines.

WebKit automation supports compatibility testing but does not replace
the manual test on an actual Safari browser.

Inspect every implemented screen for clipping, overlap, overflow,
unreachable controls and unusable dialogs at each viewport.

Pass criterion: 100% of the agreed flow and layout checks pass across
the approved matrix. Any unavailable browser environment is recorded
as Not executed, rather than Passed.

Evidence: browser/version matrix, test report, screenshots and defect links.
Execution status: Not executed.

## NFR-08 — Concurrent Approval Consistency

### Target

Approval shall be atomic and preserve the documented business rules.
Row-level locking is the agreed architectural mechanism.
Its implementation must be verified.

For one Available animal with 20 eligible Pending applications:
- Submit 20 concurrent approval requests.
- Exactly one request shall return 200.
- The other 19 shall return 409.
- Exactly one application shall become Approved.
- The animal shall become Reserved.
- The other Pending applications shall become Rejected.
- Required status-history records shall be committed consistently.

Repeat with fresh fixtures 100 times.
Pass criterion: zero double approvals or partial transactions.

The adopter quota shall also remain valid across different animals:
with two Reserved/Adopted animals and two eligible Pending applications,
two concurrent approvals shall produce one 200 and one 422.
The final Reserved + Adopted total shall be three.

Repeat the quota scenario 100 times with fresh fixtures.

### Verification

Tools: k6 or a synchronized backend integration-test harness,
plus SQL assertions.

Synchronize request starts and verify that requests overlap.
Inspect final database state after every run. HTTP responses alone
are insufficient evidence of consistency.

Evidence: concurrency results, SQL assertions, and test fixtures.

## NFR-09 — Status History and Audit Trail

### Target

Every committed Animal or Application status transition shall create
exactly one corresponding history record containing:
- The affected entity identifier.
- The responsible user ID (changedBy).
- previousStatus and newStatus.
- A UTC timestamp (changedAt).

previousStatus may be null for initial creation.

Automatic rejection after approval shall record the approving shelter
owner. Automatic rejection caused by a ban shall record the banning admin.

Status changes and their history records shall commit in the same
transaction. Rolled-back operations shall leave neither a status change
nor a corresponding committed history record.

History records shall not be editable or deletable through the API.
History access shall respect the documented role and ownership rules.

### Verification

Tools: pytest integration tests, SQL assertions and Postman/Newman.

Cover creation, approval, automatic rejection, manual rejection,
withdrawal, handover and archiving, plus rejection caused by a ban.

For each transition, compare the database and API history with the
expected actor, entity, old status and new status. Check that changedAt
falls within the operation interval measured by the controlled test clock.

Inject a failure before transaction commit and verify rollback of both
business data and history. Test unauthorized history access and confirm
that no API operation permits history modification or deletion.

Pass criterion: all tested transitions have the expected history record;
zero missing, duplicate or incorrectly attributed records; all rollback
and access-control checks pass.

Evidence: transition matrix, test report and redacted SQL assertion results.
Execution status: Not executed.

## NFR-10 — Deployment Portability

### Target

After completing documented prerequisites and environment configuration,
the system shall start using:

docker compose up --build

- No manual database creation, migration, or schema editing shall be
  required outside the documented startup process.
- Verify on Linux with Docker Engine and Windows with Docker Desktop.
- Required services shall become healthy and the application shall
  pass login and animal-list smoke tests.
- Perform three clean deployments on each supported environment.
- Pass criterion: all six deployments succeed.

Required environment variables and secret-generation steps shall be
documented. Real secrets shall not be committed to the repository.

The one-command claim applies after prerequisite setup; it does not
mean that environment configuration is unnecessary.

### Verification

Tools: Docker Compose, service health checks, and Postman/Newman.

Use disposable test environments with empty application database
volumes. Record the command, startup logs, health status, and smoke-test
results. Never reset shared or production data for this test.

Evidence: deployment checklist and results from both operating systems.

## Decisions Required Before Approval

- Confirm the proposed API performance load and thresholds.
- Confirm the standard-page list, browser versions, network conditions,
  and external-image behavior for page-loading tests.
- Confirm the supported deployment operating systems.
- Confirm the deployment health-check and startup procedure with DevOps.
- Confirm the browser, operating-system and viewport matrix for
  NFR-06/NFR-07 compatibility and responsive-layout testing.

Owner: Quality Assurance Engineer.
Reviewers: Software Architect, DevOps Engineer, and Product Owner.

## Execution Record

For each executed test, record:
- NFR ID and test-case ID.
- Commit SHA and environment.
- Input data and procedure.
- Expected and actual results.
- Pass/fail status.
- Evidence link and related defect issue.

Initial status for all tests in this document: Not executed.
