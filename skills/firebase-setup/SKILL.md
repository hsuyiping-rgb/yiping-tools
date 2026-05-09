---
name: firebase-setup
description: Install and verify Firebase integration for Codex classroom tools. Use when the user asks to connect Firebase, set up Firebase MCP, create firebase.json/.firebaserc/firestore.rules, deploy Firestore rules, verify Firebase CLI login, or troubleshoot Firebase setup on Windows PowerShell.
---

# Firebase Setup

Use this skill to connect a classroom-tools project to Firebase and prepare Firestore access for Codex workflows.

## Workflow

1. Read any user-provided setup handout first. Treat the handout as the source of truth.
2. Check prerequisites:
   - `node --version`
   - `npx.cmd --version` on Windows
   - `npx.cmd -y firebase-tools@latest projects:list`
3. If Firebase CLI is not logged in, explain that `npx.cmd -y firebase-tools@latest login` may need an interactive terminal.
4. Choose the Firebase project ID from `projects:list`; ask the user if more than one plausible project exists.
5. Create or update the project files:

```text
firestore.rules
firebase.json
.firebaserc
```

6. Before deploying Firestore rules, explain the effect clearly:
   - Deploying rules does not overwrite Firestore data.
   - It does replace the active security rules.
   - Narrow rules may block an existing site from reading or writing other collections.
7. Deploy only after explicit user approval:

```powershell
npx.cmd -y firebase-tools@latest deploy --only firestore:rules
```

8. Verify the deploy output includes `Deploy complete!`.
9. If Firebase MCP is part of the request, inspect `~/.codex/config.toml` and prefer `npx.cmd` on Windows:

```toml
[mcp_servers.firebase]
command = "npx.cmd"
args = ["-y", "firebase-tools@latest", "mcp"]
startup_timeout_sec = 60
tool_timeout_sec = 120
```

10. Tell the user to fully restart Codex after MCP config changes.

## Default Firestore Rules Template

Use this only when the user accepts public read/write for the named teaching collection.

```text
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /wordcloud_words/{document} {
      allow read, write: if true;
    }
    match /{document=**} {
      allow read, write: if false;
    }
  }
}
```

## Windows Notes

- Use `npx.cmd`, not bare `npx`, to avoid PowerShell execution-policy problems with `npx.ps1`.
- If sandboxed reads of Google Drive or Downloads fail, retry with approval instead of assuming the file is broken.
- Node versions newer than Firebase tooling officially supports may show warnings; warnings alone do not mean the deploy failed.

## Safety

- Never deploy Firestore rules without explicit user approval.
- Never commit Firebase Admin credentials, service account keys, `.env`, or local Codex/Claude settings.
- Frontend Firebase config is usually public by design, but private admin credentials are not.
