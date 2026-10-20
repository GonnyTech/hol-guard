# Functional Specification & PR Guide: Server Guardian Command-Source for `hol-guard`

## 1. Project Overview & Objective
This specification outlines the technical requirements for creating a community command-source contribution for **Server Guardian** (`ServerManager`) to integrate with **`hol-guard`** (`hashgraph-online/hol-guard`). 

The objective is to provide a static descriptor file that tells `hol-guard` how to classify Server Guardian's own commands when executed by an agent on a machine. **No code integration or coupling exists**; Server Guardian never calls `hol-guard`, and `hol-guard` never calls Server Guardian. The JSON descriptor file is the entire interface.

---

## 2. Maintenance Agreement & Scope
* **Type of Contribution:** One-time community contribution with **no ongoing maintenance expected** from the Server Guardian maintainer.
* **Target Repository:** `hashgraph-online/hol-guard` (Branch: `main`)
* **File Naming Convention:** `contributions/command-sources/command.serverguardian.json`

---

## 3. Reference Blueprints & Existing Files
To match the exact schema and syntax required by `hol-guard`, Claude Code / the developer should inspect these existing reference files in the repository:
* `contributions/command-sources/command.container-runtime.json`
* `contributions/command-sources/command.backup.restic.json`
* *Reference Documentation:* [`CONTRIBUTING.md`](https://github.com/hashgraph-online/hol-guard/blob/main/CONTRIBUTING.md)

---

## 4. Command Classification Rules (Gated vs Free)

### A. Gated / Restricted Bucket (Requires Human Approval)
These commands execute administrative mutations and land in the gated bucket to ensure human oversight:
1. **Nightly System Package Upgrades (`apt`):**
   * *Command/Action Pattern:* `apt update` / `apt upgrade` paths.
2. **Docker Container Lifecycle Management:**
   * *Command/Action Pattern:* `docker pull` and container recreate/run paths (leveraging Server Guardian's native Pending-queue pause as the human-approval outcome).

### B. Free Bucket (Unrestricted / Read-Only Telemetry)
These commands involve non-mutating system telemetry and readouts, keeping them free:
1. **TUI & System Metrics:**
   * *Command Pattern:* `server-guardian` binary startup, CPU/RAM/Network/Process metrics polling (`psutil`).
2. **Email Telemetry:**
   * *Command Pattern:* SMTP notification dispatches (`mailer.py`).

---

## 5. Execution Workflow for Claude Code / Developer

1. **Scaffold via Extension Builder:**
   * Use the `hol-guard` Extension Builder utility referenced in `CONTRIBUTING.md` to scaffold the new command source and its associated test fixture.
2. **Generate Descriptor:**
   * Create `contributions/command-sources/command.serverguardian.json`, following the shape of `command.container-runtime.json` and `command.backup.restic.json`.
3. **Validate & Test:**
   * Run local test suites to ensure gated paths trigger policy controls and read-only telemetry paths pass freely.
4. **Open Pull Request:**
   * Submit the PR targeting `hashgraph-online/hol-guard` using their standard *Command extension PR template*.