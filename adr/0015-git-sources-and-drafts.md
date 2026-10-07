# ADR-0015: Git-based sources using the user's own git; local-git drafts; immutable published versions

- Status: proposed
- Date: 2026-10-07
- Tasks: M9.0, M9.1, M10.1

## Context

Packs and agent configs come from git, and the Studio needs to author drafts that end up in git. Users already have git set up with their own keys and credential helpers.

## Decision

Krama runs the `git` binary without a shell, uses the user's own configuration and credentials, never stores or logs credentials, and fails rather than prompts. It keeps a bare mirror per remote, a read-only worktree per **pinned commit**, and a lock file. Drafts are local work trees on a draft branch with commits authored by the acting principal; a remote is optional. Publishing pushes a branch and an annotated tag and never force-pushes; a published tag that has moved is refused and reported. An optional allow-list of remotes and optional signed-commit verification are available. No host CLI or API is used.

## Consequences

- Works wherever the user's git works, on all three operating systems.
- Pull-request creation is the user's own workflow on their host.
- Pinned commits make installs reproducible and diffs reviewable.
