# Manage packs and agents from Git

Open **Packs** or **Agents** in the library to browse, search and inspect the definitions available on your server. The **Installed** pack filter and **From Git** agent filter help find entries you manage. A server-provided entry may be read-only; its detail page explains when Git management is unavailable.

## Preview and install

1. Choose **Add pack** or **Add agent**. Enter the repository URL, Git ref and directory inside the repository. Krama fetches with the server's own Git configuration and credentials.
2. For an agent, enter a `role/variant` ID such as `reviewer/default`. Optional secret bindings map environment variable names to references configured on the server. Enter reference names, never credential values.
3. Choose **Preview**. Review the pinned commit, trust status, validation results, declared team or detected agent backend, and secret availability. The expandable source details include the review digest.
4. Allow each requested permission individually. Install stays unavailable until every request is allowed and the preview is valid, compatible and unexpired.
5. Choose **Install**. The detail page shows the installed source and version history. Registering an agent does not start a process or verify that its backend is installed and signed in.

Authors and operators can preview sources. Owners and administrators can install and manage them. Viewers can browse. The API enforces these roles as well as the UI controls.

A changed source or expired review requires a new preview. The UI clears consent when you edit or refresh a review, so it applies to the version you actually reviewed.

## Updates and version history

Choose **Check for updates** on an installed entry, select the ref and preview it. Review the configuration and permission changes before applying the update. Agent updates can also change secret-reference bindings. Unchanged permissions are retained by the server; the preview identifies permission changes.

**Version history** lists installed revisions newest first. Choose **Restore** and confirm to restore a recorded revision and its consent. Agent restores include the configuration digest, so two configurations at the same commit can be distinguished. Cached revisions can be restored while their remote is unavailable.

Packs can be disabled to prevent new runs and enabled again later. **Remove** requires confirmation. The server can refuse removal or a revision change while active runs still reference it; the page displays the reason and retains the entry.

## Current limits

Install consent is checked and recorded. Per-tool enforcement and revocation during a run remain separate work. Only access reported by the preview is shown; a backend that the server has not resolved is labelled accordingly. Missing secret references must be configured on the server before running the agent.

The sample-data UI simulates lifecycle actions without fetching remote repositories or starting wrappers. Use a real server to install actual sources. See [agent definitions](./agent-definitions.md) for the HTTP lifecycle and source format.
