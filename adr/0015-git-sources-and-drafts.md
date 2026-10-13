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

## Update (2026-10-13): the authored flow — drafts, revisions and production

This settles what happens to a pack or an agent that a person creates or edits (in the Studio or by hand), as opposed to one they install from someone else. There are two kinds of source:

- **Installed** (as above): a pinned, read-only checkout of someone else's repository.
- **Authored**: a repository Krama manages on the person's machine for what they make. They own it. It is ordinary git, so it works with their tools and can be pushed to a remote they have write access to.

**Saving.** Every save is a commit, authored by the acting person, on a draft branch (`draft/<name>`). They can save as often as they like; each save is a step in the history, and nothing is squashed or rewritten. Each draft has its own work tree, so editing a draft never changes what the main work tree, or a running run, sees.

**Revisions.** A *revision* is an immutable, annotated tag `rev/<n>` on a commit, numbered upwards in that repository, with a message that says what it is and an optional label for people to read. Krama makes it when the person asks for one and never moves or deletes one. Tags are what "this exact version" means everywhere else: a revision can be tried in a run, compared with another, installed, or promoted.

**Production is `main`.** `main` only ever moves forward, and only to a revision (a fast-forward to a tagged commit). So whatever runs as production is always a named, immutable revision, and a draft that has not been given a revision cannot reach it. Rolling back adds a new commit on `main` that restores the files of an earlier revision, and tags it as a new revision; history is never rewritten, so the record of what ran and when stays intact.

**Remote and access.** A remote is optional. If there is one and the person can write to it, Krama pushes `main`, drafts and revision tags with their own git credentials (the app never holds them). It never forces a push. If the remote refuses (no access, a diverged branch, a tag that already exists elsewhere), Krama reports git's reason and changes nothing local. A fetched tag that has moved is refused and recorded as a finding.

**Audit.** Each save, revision, promotion, rollback and push is a control-audit record naming the person.

Nothing here changes installed sources, the allow-list of remotes, the signed-commit option, or the rule that no host CLI or API is used. Any later host integration (an app registration for private repositories or pull requests) sits behind a credentials interface and would be its own decision.
