# Non-Functional Requirements and Verification Plan

## 1. Status and Scope

Status: Draft for team review.
First draft date: 18 November 2026.
Related issue: #51.
Related consistency issue: #40.

This document defines measurable quality targets and planned verification
methods. It does not report executed tests or achieved results.

Existing NFR identifiers retain their meanings. Requirements not covered
here remain applicable. NFR-10 is a proposed addition whose identifier
must be confirmed before approval.

The architectural decisions supplied for this draft are bcrypt password
hashing, JWT authentication, row-level locking during approval, and
Docker Compose deployment. Their implementation must be verified.

## 2. Common Test Conditions

Proposed reference environment:
- Application and database: Docker Compose.
- Available resources: at least 2 CPU cores and 4 GB RAM.
- Record the OS, hardware, Docker version, image versions, and commit SHA.
- Use synthetic data and test accounts.
- Use a clean database or reset fixtures between independent scenarios.
- Do not store real passwords, signing keys, or personal data in evidence.

The architect and DevOps engineer must confirm the reference environment.
Performance results from different environments must not be treated as
directly equivalent.

## 3. NFR-01 — Performance

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

## 4. NFR-02 — Password Storage Security

### Target

- All stored user passwords shall use salted bcrypt hashes.
- Registration and password-change flows, where implemented, shall
  store no plaintext passwords.
- Passwords and password hashes shall not appear in API responses.
- Plaintext passwords shall not appear in application logs.
- Equal passwords assigned to different accounts shall produce
  different hashes, while both remain verifiable.
- Proposed bcrypt work factor: 12, pending architect approval and
  measurement on the reference environment.

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

## 5. NFR-03 — Authentication and Authorization

### Target

- Protected operations shall require a valid JWT.
- Missing, expired, malformed, or tampered tokens shall return 401.
- Authenticated users with an unauthorized role or resource ownership
  shall receive 403, consistent with the API contract.
- Failed authorization shall cause no protected data modification
  or disclosure.
- Proposed access-token lifetime: 30 minutes.
- Proposed configuration: ACCESS_TOKEN_EXPIRE_MINUTES=30.
- Issued tokens shall contain iat and exp, with exp - iat = 1,800 seconds.
- Tokens shall be rejected at or after exp when clock tolerance is zero.

Any nonzero clock tolerance must be explicitly agreed, documented, and
included in the boundary tests.

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

### Open decisions

The team must confirm the proposed 30-minute access-token lifetime.
Refresh tokens are not supported. After the access token expires,
the user must log in again to obtain a new access token.

After agreement, align configuration, API expiresIn examples, and
documentation. A 30-minute token corresponds to 1,800 seconds.

## 6. NFR-08 — Concurrent Approval Consistency

### Target

Approval shall be atomic and preserve the documented business rules.
Row-level locking is the proposed implementation mechanism.

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

## 7. NFR-10 — Deployment Portability (Proposed)

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

## 8. Decisions Required Before Approval

- Confirm performance load, reference hardware, and page-test conditions.
- Confirm bcrypt work factor.
- Confirm access-token lifetime and clock tolerance.
- Confirm supported deployment environments.
- Confirm the proposed NFR-10 identifier.
- Identify the purpose of any APP_KEY setting before defining its
  security requirements.

Owner: Quality Assurance Engineer.
Reviewers: Software Architect, DevOps Engineer, and Product Owner.

## 9. Execution Record

For each executed test, record:
- NFR ID and test-case ID.
- Commit SHA and environment.
- Input data and procedure.
- Expected and actual results.
- Pass/fail status.
- Evidence link and related defect issue.

Initial status for all tests in this document: Not executed.
