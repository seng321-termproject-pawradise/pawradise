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

### develop
- Serves as the integration branch for ongoing development.
- Feature branches are created from `develop`.
- Changes are merged into `develop` through reviewed pull requests.

### feature/*
- Used for individual development tasks.
- Each feature branch must be associated with a GitHub Issue.
- Feature branches should use descriptive names.

Example branch names:
- `feature/repository-setup`
- `feature/animal-listings`
- `feature/adoption-applications`

## 3. Development Workflow

All tasks must follow this workflow:

**Issue → Feature Branch → Pull Request → Review → Merge**

1. Create or select a GitHub Issue describing the task.
2. Create a feature branch from `develop`.
3. Implement the changes on the feature branch.
4. Commit changes with clear and meaningful commit messages.
5. Open a pull request targeting `develop`.
6. Request a review from another team member.
7. Address any requested changes.
8. Merge only after the required approval is received.

For releases, an approved pull request is used to merge `develop` into `main`.

## 4. Pull Request Rules

- Every change must be submitted through a pull request.
- Direct pushes to `main` and `develop` are prohibited.
- At least one approval is required before merging.
- Pull requests must reference the related GitHub Issue.
- Pull request descriptions must explain the changes made.
- Documentation must be written in English.
- Changes must be consistent with the approved project requirements.

## 5. Team Participation Requirements

Every team member must:

- Create at least two pull requests.
- Review at least two pull requests submitted by other team members.
- Participate in the Issue → Branch → PR → Review → Merge workflow.
- Follow the agreed branch protection rules.

Self-approval does not count as an independent pull request review.

## 6. Branch Protection

Both `main` and `develop` are protected.

The repository requires:

- Pull requests before merging.
- At least one approval before merging.
- Force pushes to be blocked.
- Branch deletion restrictions.

Branch protection settings must not be bypassed.

## 7. Commit Message Guidelines

Commit messages should be clear and describe the change.

Examples:

- `Add pull request template`
- `Create initial API specification`
- `Update project documentation`
- `Configure repository structure`

## 8. Documentation Guidelines

All repository documentation must be written in English.

The following artifacts must remain consistent:

- Project Proposal
- System Vision and Boundary
- OpenAPI Specification
- Product Backlog
- Requirements Documentation

Any proposed change to the approved project scope or business rules must be discussed with the team before implementation.

## 9. Review Guidelines

Reviewers should verify that:

- The changes match the related Issue.
- The implementation follows approved requirements.
- Documentation is clear and consistent.
- No unrelated features have been introduced.
- Relevant tests or verification steps have been completed.

Reviewers may approve the pull request or request changes.

## 10. Sprint 1 Delivery

The official Sprint 1 deadline is November 5, 2026, at 23:59.

The team's internal completion target is November 5, 2026, at 12:00.

Sprint 1 deliverables include:

1. Project Proposal
2. System Vision and Boundary
3. OpenAPI Draft (YAML)
4. Team Roles and RACI Matrix
5. GitHub Repository and Git-Flow Workflow
6. Initial Product Backlog

The DevOps Engineer is responsible for repository configuration, branch protection, Git workflow support, and release preparation.
