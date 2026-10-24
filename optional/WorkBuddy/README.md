# Optional WorkBuddy startup hook

English | [简体中文](README.zh-CN.md)

Scope: Windows / WorkBuddy 5.8.3. Adapter v1.0.1 and its installer passed a real SessionStart → Node → Python → ESTABLISH_SESSION E2E test on 2026-10-24 (Node v22.22.2 / CPython 3.12.14). Evidence covers one real new session and controlled repeat probes in that session. A one-time installation and registration is required. This does not establish out-of-the-box operation on other machines; automatic renewal of the 30-minute TTL remains unverified.

## Install once

First install OnHandThoroughfare and its wheel and create the product's `.venv`. Obtain CPython 3.12.x and Node.js 18 or later separately. Node 18 is the component's minimum supported version, not a claim that every Node version has been tested. Node, Python, and WorkBuddy are not bundled. Use the actual version shown in the target WorkBuddy interface: the installer accepts only 5.8.3; other versions need independent verification.

Replace every placeholder with your own absolute path. The user must select `--entry`; do not copy another person's drive letter. Set `--node` to the actual node.exe. The hook records its full path and does not depend on WorkBuddy's PATH.

```powershell
& '<full-path-to-Python3.12>' .\install.py install --entry '<skill-area>\OnHandThoroughfare\SKILL.md' --node '<full-Node-directory>\node.exe' --workbuddy-version 5.8.3
& '<full-path-to-Python3.12>' .\install.py check
```

Only an explicit `install` command registers the user-level `hooks.SessionStart`; installing OnHandThoroughfare itself does not change the Host. The default location is the current user's `.workbuddy/hooks/onhand-startup-portable`, preserving other settings and hooks. Use `--config-dir '<target-config-directory>'` for an actual custom configuration location or a test directory, and use the same argument for every subsequent command. Close programs that may write settings during installation. The tool detects read/write drift but has no transaction lock shared with other programs.

Repeating an identical installation leaves settings unchanged. Different arguments or files cause STOP rather than overwriting the old adapter. The tool does not recognize or remove older experimental adapters. If OnHand startup wiring already exists for the target user, review and disable old entries first to avoid duplicate triggers. Existing valid wiring does not need another installation of this component.

## Check, uninstall, and roll back

`check` inspects only configuration and files. It does not create a simulated session or connect to the product. After a new session, consult `logs/last-status.json` in the installation directory for the actual attachment result; PASS requires a successful product receipt. Failures record STOP/FAIL, while the adapter exits 0 so that the WorkBuddy session can continue. Failure to write a log must not be treated as success.

```powershell
& '<full-path-to-Python3.12>' .\install.py uninstall
# Alternatively, restore the exact pre-installation bytes if settings still match the installed state:
& '<full-path-to-Python3.12>' .\install.py rollback
```

Uninstallation removes only the separate hook group identified by this component's unique description marker, preserving other hooks and subsequent settings. Repeated uninstallation is safe. Rollback checks settings and backup digests and refuses if later edits exist; use uninstall to preserve those edits. Uninstall and rollback retain the adapter, logs, and backups for review. Once auditing is no longer needed, you may manually remove this component's dedicated directory and the backups identified by `backups/onhand-startup-install.json`. Do not delete the entire hooks directory or restore an old full configuration over later changes.

## Attachment, relocation, and long sessions

The adapter handles only SessionStart/startup and session_id supplied by the Host itself. It calls the existing `ESTABLISH_SESSION` with `startup_hook=true`; it creates no identities, executes no work Skills automatically, and starts no background service. It writes the product's own legitimate session carrier at runtime: “no Core modification” does not mean state write permissions are unnecessary. Reviewed resident mappings match the original adapter. Other Skills are not included; the formal entry point may STOP if required products are missing.

Moving the Skill area or Node invalidates the old absolute paths. Explicitly select the new entry point and uninstall the hook first; review and move aside the dedicated old adapter directory before reinstalling. The component does not scan drives or locate replacement entry points itself.

The carrier TTL is 1800 seconds. Formal calls can refresh a valid carrier. After expiry, old correlations must stop. Using the actual current entry point and the same real session identity, the Host may explicitly call the formal ESTABLISH_SESSION, bootstrap again, and use new attachment/resident correlations. Do not replay old business requests or reuse old handles. The original startup adapter skips resume and does not renew automatically. Existing isolated tests verified expiry rejection and explicit reconstruction; there is no PASS evidence for automatic renewal in a real WorkBuddy session lasting 30 minutes.

The readable adapter and installer are covered by the included [LICENSE](LICENSE). Core remains distributed in its original bytecode wheel. Obtain Node, Python, and WorkBuddy separately and comply with their respective licenses.
