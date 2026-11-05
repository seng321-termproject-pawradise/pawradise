# Contributing to pawradise

## 1. Project Overview

pawradise is a web-based Animal Shelter & Adoption System developed for the SENG 321 Software Engineering course.

The platform connects animal shelters with people who want to adopt animals.

This document defines the contribution guidelines, Git workflow, pull request process, and code review requirements for the project.

## 2. Branch Strategy

The repository follows a Git-Flow-based branching strategy.

### main
- Contains stable, release-ready versions.
- Direct pushes to `main` are prohibited.
- Changes must be merged through approved pull requests.
- Each sprint release on `main` is marked with a tag (see Section 11).

### develop
- Serves as the integration branch for ongoing development.
- It is the default branch of the repository.
- Feature branches are created from `develop`.
- Changes are merged into `develop` through reviewed pull requests.

### feature/*
- Used for individual development tasks.
- Each feature branch must be associated with a GitHub Issue.
- Feature branches are created from the latest `develop` and merged back into `develop`.
- Feature branches are deleted after their pull request is merged.

## 3. Branch Naming Conventions

**Format:**

```
feature/<issue-number>-<short-description>
```

Rules:

- The issue number is mandatory. A branch without an issue is not allowed.
- Use lowercase letters and separate words with `-` (kebab-case).
- Keep the description short (3–6 words).
- One issue per branch.

Examples:

- `feature/4-vision-boundary`
- `feature/6-openapi-draft`
- `feature/10-team-raci`

## 4. Development Workflow

All tasks must follow this workflow:

**Issue → Feature Branch → Pull Request → Review → Merge**

1. Create or select a GitHub Issue describing the task.
2. Create a feature branch from `develop`.
3. Implement the changes on the feature branch.
4. Commit changes with clear and meaningful commit messages (see Section 6).
5. Open a pull request targeting `develop`.
6. Request a review from another team member.
7. Address any requested changes.
8. Merge only after the required approval is received.

For releases, an approved pull request is used to merge `develop` into `main` (see Section 11).

## 5. Creating Issues

Every piece of work starts with an issue. No issue, no branch, no pull request.

Use the issue templates in `.github/ISSUE_TEMPLATE/` (feature request, bug report).

Every issue should have:

- A clear title and description.
- Acceptance criteria or a clear definition of the expected result.
- Related requirement IDs (FR / NFR / BR) when applicable.
- Labels (type and sprint, for example `type: documentation` and `sprint: 1`).
- An assignee.
- A link to the project board (Pawradise – Sprint Board).

If a requirement seems incomplete or contradictory, it must not be changed silently. Open an issue and discuss it with the team.

## 6. Commit Message Guidelines

Commit messages should be clear and describe the change. The team uses the Conventional Commits style:

```
<type>(<scope>): <short summary>

Refs #<issue-number>
```

- **Types:** `feat`, `fix`, `docs`, `test`, `refactor`, `ci`, `chore`
- **Scope (optional):** `api`, `backend`, `frontend`, `docs`, `process`, `ci`
- Write in English and in the imperative mood ("add", not "added").
- Keep the summary line short (at most 72 characters).
- Reference the related issue in the commit body (`Refs #12`).

Examples:

- `docs(process): add team roles and RACI matrix`
- `feat(api): add POST /applications endpoint`
- `fix(backend): return 409 when animal is already reserved`

## 7. Pull Request Rules

- Every change must be submitted through a pull request.
- Direct pushes to `main` and `develop` are prohibited.
- At least one approval is required before merging.
- Pull requests must reference the related GitHub Issue with `Closes #<issue-number>`.
- Pull request descriptions must explain the changes made (use the pull request template).
- Documentation must be written in English.
- Changes must be consistent with the approved project requirements.
- Keep pull requests small and focused on one issue.

## 8. Team Participation Requirements

Every team member must:

- Create at least two pull requests.
- Review at least two pull requests submitted by other team members.
- Participate in the Issue → Branch → PR → Review → Merge workflow.
- Follow the agreed branch protection rules.

Self-approval does not count as an independent pull request review.

## 9. Review Guidelines

Reviews are submitted with the GitHub **Review changes** feature (Approve, Comment, or Request changes). Approvals given in chat applications do not count.

Reviewers should verify that:

- The changes match the related Issue.
- The implementation follows approved requirements.
- Documentation is clear and consistent.
- No unrelated features have been introduced.
- Relevant tests or verification steps have been completed.

Reviewers should leave at least one meaningful comment and may approve the pull request or request changes.

## 10. Merge Rules

- A pull request can be merged only after it has the required approval.
- **Merge method:** Create a merge commit. Squash and rebase merges are not used, so that the history and individual contributions remain visible.
- The author of the pull request merges it after approval.
- The feature branch is deleted after merging.
- Force pushes to `main` and `develop` are not allowed.

## 11. Releases and Tags

At the end of every sprint:

1. A release pull request is opened from `develop` into `main` with the title `release: Sprint <n>`.
2. After approval, it is merged with a merge commit.
3. A tag and a GitHub Release are created on `main`.

| Sprint | Tag |
|---|---|
| Sprint 1 | `sprint-1` |
| Sprint 2 | `sprint-2` |
| Sprint 3 | `sprint-3` |
| Final submission | `v1.0.0` |

## 12. Branch Protection

Both `main` and `develop` are protected.

The repository requires:

- Pull requests before merging.
- At least one approval before merging.
- Force pushes to be blocked.
- Branch deletion restrictions.

Branch protection settings must not be bypassed. If a direct push happens by mistake, it is reported to the Scrum Master and resubmitted through a pull request.

## 13. Documentation Guidelines

All repository documentation must be written in English.

The following artifacts must remain consistent:

- Project Proposal
- System Vision and Boundary
- OpenAPI Specification
- Product Backlog
- Requirements Documentation

Any proposed change to the approved project scope or business rules must be discussed with the team before implementation. Team decisions are recorded in `docs/meetings/`.

## 14. Project Board

All issues are tracked on the GitHub Projects board **Pawradise – Sprint Board**.

| Status | Meaning |
|---|---|
| Backlog | Accepted item, not planned for the current sprint |
| To Do | Planned for the current sprint, not started |
| In Progress | Someone is working on it (a branch exists) |
| In Review | A pull request is open and waiting for review |
| Done | Pull request merged and issue closed |

## 15. Sprint 1 Delivery

The official Sprint 1 deadline is November 5, 2026, at 23:59.

The team's internal completion target is November 5, 2026, at 12:00.

Sprint 1 deliverables include:

1. Project Proposal
2. System Vision and Boundary
3. OpenAPI Draft (YAML)
4. Team Roles and RACI Matrix
5. GitHub Repository and Git-Flow Workflow
6. Initial Product Backlog

The DevOps Engineer is responsible for repository configuration, branch protection, and Git workflow support. The Scrum Master opens the release pull request and prepares the submission document.
