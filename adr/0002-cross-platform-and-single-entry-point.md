# ADR-0002: Windows, Linux and macOS are first-class; one entry point

- Status: accepted
- Date: 2026-10-03
- Tasks: M0.5, M7.1

## Context

Users run Krama on their own machines and in their own organisations. A tool that works only on Linux, or needs a compiler or shell scripts, loses most of them.

## Decision

Everything must work on all three operating systems and CI runs on all three (Node 22 and 24). No bash scripts and no native modules. Paths use `node:path` and `URL`, never string concatenation, and must cope with spaces and non-ASCII. The single entry point is `npx kramahq`.

## Consequences

- Install friction stays low and there is no toolchain requirement.
- Some conveniences are given up, such as `lsof`-style process cleanup, in favour of portable approaches.
- CI is slower and a change that is green only on Linux is not done.
