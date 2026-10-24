# Temporary Codex overrides

Use this workflow only when the user requests applying or restoring a temporary instruction override. It is a controlled file change, not a way to discard higher-priority instructions or to hot-reload this chat.

## Prepare the exact change

1. Identify the target directory and the source file that is currently selected there. Inspect both `AGENTS.md` and `AGENTS.override.md`, including empty files, if present. A global override changes guidance for all new runs sharing that Codex home. Do not present it as isolated to one chat. A project override cannot remove the global file from the discovery chain.
2. Build a non-empty replacement containing the applicable rules for that level. Retain unconditional requirements unless the user has authorized the specific change in scope. Do not copy lower-level project rules into a global profile. Leave the underlying `AGENTS.md` intact.
3. Show the destination, a concise before/after diff, what requirements change, and the restore plan. If the requested mutation and scope are already authorized, proceed; otherwise ask for the missing authorization after this concrete preview. Use normal filesystem approval mechanisms for protected paths.
4. Check for an existing active override or a recorded lease for another task. Do not stack or take over another task's temporary override automatically. An existing manually maintained override can be replaced only within the user's authorized scope and with its original bytes preserved. If multiple sessions need different global rules, recommend conditional permanent rules or separate profiles instead of racing on one global file.

## Save a recoverable snapshot

Before writing, create a unique private state directory outside public repository content. Record:

- Target absolute path and task identifier.
- Whether the target existed, its SHA-256 digest, and its original permissions.
- A byte-for-byte copy of the original override when it existed, including an empty original.
- SHA-256 of the underlying `AGENTS.md`, when present, for detecting later changes.
- The proposed replacement bytes and their SHA-256 digest.

Use local file and hashing tools available in the environment. Keep backups out of chat output and public commits. Do not copy credentials or unrelated settings. If the target is a symlink, an unexpected file type, or outside the authorized path, explain the situation and resolve it before modifying anything.

Immediately before applying, re-read the target and compare its existence and digest to the snapshot. If it changed, stop and recompute the diff; do not overwrite the other writer. When creating a new target, use exclusive creation so an existing file cannot be silently replaced. For a replacement, use a temporary sibling file and atomic rename after the recheck, preserving permissions. This guards against common accidental clobbers, but is not a lock against arbitrary concurrent writers; do not apply while another process is known to be updating the file.

Write the non-empty profile to `<target-directory>/AGENTS.override.md`, read it back, and verify its digest. Save the applied digest and state-directory location. Keep the base `AGENTS.md` unchanged.

Report **“written; effective on a fresh Codex run after instruction discovery”**, not “this chat's old rules were removed.” Verify the loaded source list in the new run when possible; otherwise distinguish file verification from runtime verification. Do not create a new chat or launch another agent run unless the user requested it.

## Restore without losing later edits

At the end of a workflow that authorized both apply and restore, restore it as agreed. Otherwise restore when requested. If no later turn or process is available to perform cleanup, state that the override remains active and give the exact state-directory path; never promise unattended restoration.

1. Locate this task's saved state and verify the target path.
2. Require the current override to match the saved applied digest. If it differs, stop and show a concise conflict; retain both the current file and backup. Do not force restoration over a user's edit or an installer's update.
3. Restore the original override bytes and permissions if one existed. If none existed, remove only this task's unchanged override.
4. Read back and verify the restored bytes, or verify absence. If the underlying `AGENTS.md` changed while the override was active, report it: removing the override exposes the current base, not the historical base. Do not roll back that base automatically.
5. Record restoration in the private state. Keep the backup until restoration is verified; remove only task-owned temporary files when cleanup is requested or agreed.

If state is missing, do not guess or delete a shared override. Inspect the current file and ask only for the information needed to recover safely.

Reference: [OpenAI's AGENTS.md discovery and temporary override behavior](https://learn.chatgpt.com/docs/agent-configuration/agents-md).
