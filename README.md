# OnHandThoroughfare

English | [简体中文](README.zh-CN.md)

**OnHandThoroughfare is the connection core of the OnHand external Skill matrix: one entry point connects the matrix, keeps Skills portable, and supports automatic attachment when a Host starts a session.**

- **One entry point**: connect external Skills that have been reviewed and formally attached, without copying each implementation into the Host.
- **External management and portability**: use a portable SSD, switch Hosts, and formally manage third-party Skills outside the Host.
- **Startup hook integration**: on a supported, correctly configured Host, new sessions can connect automatically without a manual connection prompt each time.

A real SessionStart automatic-attachment E2E test has passed on WorkBuddy 5.8.3. After a one-time optional local adapter installation and explicit registration, new sessions can attach automatically. This does not imply zero configuration, compatibility with every Host, or automatic execution of work Skills.

## One entry point, distinct responsibilities

The OnHand external Skill matrix uses OnHandThoroughfare as its shared connection and mechanical transport core. Through this entry point, the Host can use formally attached logic-assistance Skills and general-purpose work Skills. Each Skill retains its own responsibilities and operates under its own public contract.

- **OnHandThoroughfare handles WHERE + HOW**: validate Skill locations against formal wiring, transport calls mechanically, and return results to the Host under the original contract. It does not select Skills, plan work, or reason about business tasks.
- **The Host retains decision authority**: understand the task, select Skills (WHO), assess authorization and execution conditions, choose the final executor, and decide whether to continue. BestFit can supply candidate-matching evidence in a combined setup; the Host makes the final selection.
- **Skills can be external, portable, and added or removed as needed**: Skill bodies, runtimes, and their respective state remain in the Skill area, which may reside in a local directory or on a portable SSD. The Host adds, replaces, and removes Skills through their formal lifecycles with user authorization.
- **Switching Hosts is supported**: after changing devices, Hosts, or drive letters, supply the actual current entry point and validate attachment again. Reuse valid connections within the same session; never reuse credentials from another session. Each Skill's runtime and licensing requirements still apply.

“Connection core” describes the matrix's shared connection model. It does not mean every OnHand Skill depends on OnHandThoroughfare to run independently; each Skill's own contract and runtime requirements determine that.

Insufficient permissions, an invalid connection, or failed contract validation must result in a stop and a report. Moving the project, broadening permissions, or bypassing the formal entry point is not a remedy.

## An open logic-assistance system

OnHandThoroughfare's logic-assistance system is not limited to the OnHand family. In principle, any logic-assistance Skill that uses `SKILL.md` as its formal entry point may be a compatible integration target. This describes the potential compatibility scope: the presence of `SKILL.md` alone does not establish eligibility or callability.

For a logic-assistance Skill that meets the actual integration requirements, the applicable attachment mode depends on its capabilities, public contract, and user authorization:

| Attachment mode | Meaning and boundaries |
|---|---|
| `ON_DEMAND` | The Host initiates a call when needed, under the formal contract. |
| `SESSION_RESIDENT` | Establish valid attachment within the current Host session for subsequent tasks. This does not imply a continuously running background process or automatic execution of work. |

The attachment mode must be supported by the formal contract and pass a wiring review. It cannot be assigned merely from a directory name, Skill description, or Host preference. Three distinct levels must be kept separate:

- **Potential compatibility**: logic-assistance Skills with a formal `SKILL.md` entry point that may meet the integration requirements. This establishes neither formal attachment nor runtime verification.
- **Formally integrated Skills**: Skills that have completed the required review, registration, and wiring under the formal contract, with valid attachment in the required session. Directory presence or Host discovery alone is not formal integration.
- **Skills verified in real operation**: Skills supported by real execution evidence for the stated Skill, Host, version, environment, and invocation scenario. Conclusions cover only that evidence's scope. Formal integration does not imply that every business task or cross-Host acceptance test has passed.

This README does not present potential compatibility as a claim that all third-party Skills have passed real tests, and it offers no guarantee that arbitrary Skills are plug-and-play. The startup-hook verification below covers only the listed Host attachment scenarios; it does not replace each Skill's own runtime verification.

General-purpose work Skills can still be managed externally under the existing `OnHandThoroughfare/GeneralSkills/<SkillName>/` path rule, with explicit user selection and completion of the formal management lifecycle. Logic-assistance Skills retain the locations required by their own contracts and current wiring. This description introduces no shared directory hierarchy for them and does not require moving them all into `GeneralSkills/`. OnHandThoroughfare does not scan directories for automatic registration.

## Attach at startup or on first use

A startup hook is a Host integration. The Host calls the formal attachment flow when a session starts; OnHandThoroughfare validates the entry point, root identity, and current session, then establishes or reuses a valid connection. It is not a background service that starts itself, and it does not automatically execute unauthorized Skills.

| Host / environment | Verified scope | Requirements and limitations |
|---|---|---|
| Windows Codex integration | Existing records support attachment through a configured startup hook | Compatibility with every Codex version is not guaranteed; attachment and invocation remain subject to actual permissions |
| WorkBuddy 5.8.3 / Windows | Adapter v1.0.1 passed a real new-session SessionStart E2E test; the Host directly triggered Node → Python → ESTABLISH_SESSION, and existing hooks continued to run | Requires a one-time optional local installation and explicit registration; only one real new-session sample, plus controlled repeat probes in the same session; automatic renewal of the 30-minute TTL is unverified |
| Other Hosts, versions, systems, or devices | Startup hooks were not verified in this round | Verify independently on the target Host; without a hook, perform formal attachment on first actual use |

### WorkBuddy: install once, connect in new sessions

This distribution includes a separate optional component in `optional/WorkBuddy/`. Install OnHandThoroughfare first, then select the actual `SKILL.md` entry point and Node.js path and explicitly run the component's `install.py install`. A normal OnHandThoroughfare installation does not silently change Host settings. See the [component installation guide](optional/WorkBuddy/README.md). Requirements are Windows, WorkBuddy 5.8.3, a separately obtained Node.js 18 or later, and the product's CPython 3.12.x environment. Other versions have not been verified.

The installer supports checks, repeat installation, uninstallation that preserves other hooks, and rollback protected by digests. It attaches only on a new session's `startup`; it does not automatically execute BestFit, Drudge, or third-party work Skills, and adds no background process.

**Long sessions**: the session carrier has a 30-minute TTL. Formal calls can refresh the carrier while it is valid. After expiry, the Host can explicitly reattach through the existing formal entry point using the actual current entry and real session identity, then use the new correlations. Expiry rejection and explicit reconstruction have been verified in isolation for this distribution. The startup adapter skips `resume`; automatic renewal in a long WorkBuddy session has not been verified, and connections are not promised to remain valid indefinitely.

Startup attachment requires the external Skill area to be online, a valid entry point, Host support for local tools, and the necessary permissions. Disconnecting the drive makes the connection unavailable. Reconnection requires the Host to validate an explicitly supplied path; there is no background scanning or automatic reconnection. Automatic attachment does not mean every Skill has passed cross-Host acceptance testing.

## Whether the Host chooses external Skills

Successfully connecting OnHandThoroughfare does not guarantee that the Host will proactively invoke its Skills.

AI Agent Hosts have their own discovery, selection, and invocation mechanisms. In actual tasks, some Hosts may favor their native Skills or built-in capabilities and may not proactively select external Skills already connected through OnHandThoroughfare.

A Skill not being selected therefore does not necessarily indicate incorrect installation, a failed connection, or a malfunction.

Users can explicitly ask the Host to query or invoke a particular external Skill. Successful invocation still depends on Host support, the current attachment state, and the Skill's own requirements.

OnHandThoroughfare provides shared connection and mechanical transport. It does not alter the Host's Skill-selection weights or guarantee priority for external Skills.

## Connect with a single prompt

After installation, give the Host the actual path. `X:` is an example drive letter only:

```text
Please connect OnHandThoroughfare using this entry point:
X:\ONHAND_SKILL_AREA\OnHandThoroughfare\SKILL.md
```

An environment with a correctly configured and verified startup hook does not require this prompt each time. Without a hook, the Host performs the same attachment on first actual use in the session and reuses it thereafter; a new session establishes its own connection. A prompt does not replace installation, permissions, or third-party registration.

## Layout and installation

The current path contract locates the product through the actual `OnHandThoroughfare/SKILL.md`; the parent of the product directory is the current Skill area. The following illustrates a deployed layout, not the complete download contents:

```text
ONHAND_SKILL_AREA/
├─ ONHAND_ROOT.json                  # Skill-area identity, initialized independently on first installation
├─ OnHandThoroughfare/
│  ├─ SKILL.md                       # Formal entry point and public contract
│  ├─ UNIVERSAL_THIN_HANDOFF.md
│  ├─ GENERALSKILLS_PORTABILITY_PUBLIC_CONTRACT.md
│  ├─ LICENSE
│  ├─ scripts/invoke.py              # Required public launcher
│  ├─ .venv/                        # Runtime environment you create
│  ├─ AVAILABLE_SKILLS.json          # Available-Skill projection
│  ├─ SESSION_RESIDENT_LINES.json    # Session-resident attachment projection
│  ├─ GeneralSkills/<SkillName>/     # Third-party Skills explicitly selected and formally registered
│  ├─ state/                        # OnHandThoroughfare's own state
│  └─ workspace/                    # Task state, staging, and evidence
└─ <other OnHand products>/         # Separate top-level directories, if installed
```

`GeneralSkills/` is the current external third-party path; the historical `通用skill` layout is not the current installation guidance. Host discovery links point only to the formal product and are not a second product or state authority. `workspace/` does not require moving your project repository.

This distribution requires **CPython 3.12.x**. Core is distributed as bytecode and is incompatible with other Python minor versions. The companion wheel version is `1.0.0+rc20261024`; the historical internal version is `1.0.0`. Core bytecode and runtime contracts retain their verified bytes. Only the wheel license, descriptive METADATA, and RECORD integrity table have been updated for this distribution. The bilingual distribution revision is `FIRST_PUBLIC_BILINGUAL_20261024`, distinguished by the file manifest and SHA256 digests. The retained `rc20261024` wheel version is build provenance; the distribution identity is defined by this revision and its checksums.

1. Verify file sizes and SHA256 digests against `RELEASE_MANIFEST.json` and `SHA256SUMS.txt` in the outer distribution. After extracting the Agent Skill ZIP, its own `RELEASE_MANIFEST.json` covers the product files. Manifests exclude themselves; each checksum file covers its corresponding manifest.
2. Extract the Agent Skill ZIP into your selected Skill area to obtain `OnHandThoroughfare/SKILL.md`.
3. Use a separately obtained CPython 3.12 to create the product's `.venv` and install the companion wheel:

```powershell
py -3.12 -m venv '<skill-area>\OnHandThoroughfare\.venv'
& '<skill-area>\OnHandThoroughfare\.venv\Scripts\python.exe' -m pip install --no-index --no-deps '<distribution-directory>\onhandthoroughfare-1.0.0+rc20261024-cp312-none-any.whl'
```

4. Have the Host initialize a unique root identity for the new Skill area under the path contract, then attach through the actual entry point. Do not copy someone else's `ONHAND_ROOT.json`, registrations, Bindings, or session state.

Empty projections on first installation do not mean other Skills are installed. Never overwrite an existing Skill area's formal state with empty templates; upgrades require separate arrangements. Python, pip, other OnHand products, and third-party Skills are not included.

WorkBuddy startup attachment has passed verification on Windows / WorkBuddy 5.8.3 / Node v22.22.2 / CPython 3.12.14. Adapter v1.0.1 has only one real new-session sample; controlled repeat probes in the same session do not count as a second new session. Clean machines, other versions, SSD use across devices, and automatic renewal of the 30-minute TTL have not been verified.

## License and redistribution

Copyright (c) 2026 OnHand Core

The OnHand family follows a unified closed-source distribution policy. The first-party programs, bytecode, required launcher, contracts, and documentation in this distribution are covered by the included [LICENSE](LICENSE). Complete, unmodified official distributions may be freely redistributed. Modification and public release of repackaged versions are not authorized, and first-party source code is not proactively published.

Core remains a bytecode distribution. Alongside the required public `scripts/invoke.py`, the optional Host adapter and installer are supplied as readable code for local review and configuration. Bytecode is not encryption and does not guarantee protection against analysis or decompilation. Independent third-party licenses and genuinely existing lawful grants remain respected.

The Chinese text in LICENSE governs; the included English translation is for convenience. If they differ, the Chinese text prevails. The translation grants no additional rights.
