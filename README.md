# ⚡ Zeus: The Agentic Cloud & Infrastructure Harness

> **Autonomous Cloud Orchestration, Diagram-to-IaC Synthesis, Unified Multi-Cloud VM Control, and Agentic CI/CD.**

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Terraform](https://img.shields.io/badge/Terraform-1.5+-623CE4?logo=terraform)](https://www.terraform.io/)
[![Jenkins](https://img.shields.io/badge/Jenkins-CI%2FCD-D33833?logo=jenkins)](https://www.jenkins.io/)
[![Architecture](https://img.shields.io/badge/Architecture-Draw.io%20%7C%20Vision-orange)](#-drawio--png-to-terraform-pipeline)

---

## 🌟 Vision & Overview

**Zeus** is an agentic AI harness engineered specifically for Cloud and DevOps engineers. It bridges the gap between high-level architectural design, infrastructure as code (IaC), CI/CD pipelines, and active fleet operations.

Instead of jumping between AWS Console, Alibaba Cloud, GCP, Jenkins dashboards, terminal SSH windows, and Terraform repositories, Zeus provides a **single conversational and command-driven agentic harness** inspired by:
- [**Pi (`earendil-works/pi`)**](https://github.com/earendil-works/pi): Minimalist, resilient, extensible agent loop, **token-minimal task reading**, and terminal ergonomics.
- [**Hermes Agent (`NousResearch/hermes-agent`)**](https://github.com/NousResearch/hermes-agent): Persistent cross-session project memory, infrastructure topology constraints (on-prem, hybrid, Direct Connect, VPN), and context retention.
- [**OpenCode (`anomalyco/opencode`)**](https://github.com/anomalyco/opencode): Multi-agent workflow, decoupled **Plan** vs **Build** modes, and robust workspace interaction.
- [**Herdr (`herdrdev/herdr`)**](https://github.com/herdrdev/herdr): Persistent multi-machine session manager and agent-driven terminal multiplexing.
- [**ec2s (`PYTHON01100100/ec2s`)**](https://github.com/PYTHON01100100/ec2s) & [**a1s (`PYTHON01100100/a1s`)**](https://github.com/PYTHON01100100/a1s): High-speed, terminal-native instance navigation (`k9s` style) across AWS and Alibaba Cloud.

---

## 🧭 Dual Operational Modes: `Plan` vs `Build`

Inspired by cutting-edge agentic workflows, Zeus operates in two distinct operational modes to guarantee safety and clarity:

```
┌────────────────────────────────────────────────────────────────────────┐
│                              ZEUS MODES                                │
├──────────────────────────────────┬─────────────────────────────────────┤
│ 🔍 PLAN MODE (Read-Only / Safe)  │ 🔨 BUILD MODE (Execution / Apply)   │
├──────────────────────────────────┼─────────────────────────────────────┤
│ • Natural language infra queries │ • Terraform apply & provisioning    │
│ • Inspect VPCs, subnets, vSwitches│ • Create, update, or stop resources │
│ • Review ECS & EC2 instances     │ • Execute SSH commands on VMs       │
│ • Consult on-prem & VPN memory   │ • Trigger Jenkins CI/CD builds     │
│ • Generate & inspect Terraform HCL│ • Strict Permission Guardrails      │
│ • Export audit reports (infra.md)│ • Human approval required to delete │
│ • ZERO mutation risk             │                                     │
└──────────────────────────────────┴─────────────────────────────────────┘
```

- **`PLAN` Mode**: The default safe mode. Ask anything about your running infrastructure, design architecture, simulate costs, and preview `terraform plan` outputs without altering any cloud state.
- **`BUILD` Mode**: Active execution mode. Applies changes, creates infrastructure, and runs tasks—**always gated by explicit human authorization** for any destructive or high-impact actions.

---

## 🚀 Core Capabilities

```
                  ┌──────────────────────────────────────────────┐
                  │                 ZEUS HARNESS                 │
                  │   Agentic Core (Planning • Tooling • Ops)    │
                  └───────┬──────────────┬──────────────┬────────┘
                          │              │              │
         ┌────────────────┴──────┐       │       ┌──────┴────────────────┐
         ▼                       ▼       ▼       ▼                       ▼
  ┌──────────────┐      ┌─────────────┐ ┌─────────────────┐     ┌────────────────┐
  │  Terraform   │      │   Jenkins   │ │   Visual IaC    │     │  Unified VMs   │
  │  Provisioner │      │  Automator  │ │ Draw.io & Image │     │  Multi-Cloud   │
  │ (Plan/Apply) │      │  (Pipelines)│ │  to Terraform   │     │  (//VMs & SSH) │
  └──────────────┘      └─────────────┘ └─────────────────┘     └────────────────┘
```

### 1. 🔍 Natural Language Cloud Discovery & `infra.md` Exporter
- **Ask Your Cloud Anything**:
  - *"Show all my VPCs and their CIDR ranges across AWS and Alibaba Cloud."*
  - *"List all subnets and vSwitches with their IP allocations."*
  - *"What ECS services/clusters or EC2 instances are currently running?"*
  - *"Which instances have public IPs exposed to the internet?"*
- **One-Click `infra.md` Export**:
  - Run `//export infra.md` or prompt *"Export my infrastructure to infra.md"*.
  - Generates a single, comprehensive Markdown audit documentation file capturing:
    - Overall topology overview and multi-cloud breakdown.
    - VPC tables (VPC IDs, CIDR blocks, regions).
    - Subnet & vSwitch tables (Zone, CIDR, available IPs, route table links).
    - Compute inventory (Instance/ECS names, IDs, Public & Private IPs, state, instance types).
    - Security Group & exposed port summaries.
    - Custom notes, tags, and ownership details.

### 2. 🧠 Persistent Project Memory & Network Constraints (Hermes Philosophy)
- **Avoid Costly Architectural Mistakes**:
  - Remembers whether your environment is **On-Premise**, **Hybrid**, or **Multi-Cloud**.
  - Stores existing **AWS Direct Connect (DX)** circuits, **Site-to-Site VPN** tunnels, **Transit Gateways**, and reserved on-prem IP subnets.
- **Natural Language & Command Memory Input**:
  - *"Zeus, remember that our on-prem datacenter uses 192.168.0.0/16 and connects to AWS via Direct Connect with ASN 65000."*
  - *"Remember: All database instances must be isolated in private subnets with no internet gateway route."*
  - Command: `//memory` or `//memory add "<rule>"`.
- **Pre-Execution Guardrails**:
  - Before generating or applying Terraform, Zeus automatically checks proposed CIDRs and routes against saved memory.
  - Automatically alerts: `⚠️ Overlap Warning: Proposed CIDR 192.168.1.0/24 conflicts with your on-prem Direct Connect range (192.168.0.0/16) stored in project memory.`

### 3. ⚡ Minimal-Token Tool Pipeline & Smart Error Diagnostics (Pi Philosophy)
- **Dual-Stream Execution**:
  - Streams full live logs (ANSI colors, progress bars) to the terminal for the human operator.
  - **Compresses and extracts high-signal summaries** for the LLM tool response to conserve context tokens and prevent context degradation.
- **Task-Focused Reading**:
  - Instead of dumping entire 5,000-line cloud state files or multi-megabyte build logs into prompt memory, specialized reading filters (`summarize_state`, `extract_tf_plan`, `filter_jenkins_tail`) extract only the exact lines pertinent to the current task.
- **Smart Terraform Error Parser**:
  - Automatically intercepts Terraform failures (`terraform validate`, `terraform plan`, `terraform apply`).
  - Strips noisy telemetry, stack dumps, and ANSI escapes.
  - Extracts pinpoint diagnostics:
    - **File & Line**: `main.tf:34`
    - **Offending Code Snippet**: `instance_type = "t3.micr"`
    - **Root Cause & Suggestion**: `"t3.micr" is invalid; did you mean "t3.micro"?`
  - Explains the exact remediation step directly to the user and feeds clean context to the agent for instant self-correction.
- **Smart Jenkins Error Parser & Build Windowing**:
  - Intercepts failed Jenkins builds via the Pipeline Graph API (`/wfapi/describe`).
  - Automatically isolates the **exact stage that failed** (e.g. `[Stage: Docker Build]` or `[Stage: Unit Tests]`).
  - Windows only the relevant 25 lines of the error stack instead of dumping a 10MB console log.
  - Generates a clean diagnostic card:
    ```text
    🚨 JENKINS BUILD FAILURE
    Job: deploy-production-backend #42
    Failed Stage: [Docker Build] (Duration: 1m 12s)

       > Step: sh 'docker build -t app:v2.1 .'
       > Error: Dockerfile:14: COPY failed: file not found in build context: config/prod.json

    ❌ Cause: Missing build artifact 'config/prod.json'.
    💡 Suggested Fix: Ensure config/prod.json is committed or generated in a previous pipeline stage.
    ```

### 4. 🛡️ Strict Permission Gates & Blast-Radius Protection
- **No Silent Deletions**: Zeus enforces a mandatory human-in-the-loop checkpoint before **any** destructive cloud action:
  - Deleting or terminating any ECS task, service, or EC2/Alibaba Cloud instance.
  - Destroying infrastructure via `terraform destroy` or `terraform apply` when resource replacements/deletions are detected.
  - Modifying critical security groups or routing tables.
- **Blast-Radius Inspection**: Zeus parses the exact diff, highlights resources marked for destruction in red, and demands explicit typed confirmation:
  ```
  ⚠️  DESTRUCTIVE ACTION WARNING:
      Zeus detected that the following 2 resources will be DESTROYED:
      - aws_ecs_service.api_gateway
      - aws_instance.legacy_worker_01

  Type 'CONFIRM-DELETE' to proceed or press [Esc] to cancel.
  ```

### 5. 🏗️ Agentic Terraform Engine
- **Preflight Environment Validation**: Automatically validates that `terraform` binary is installed in `$PATH`. If missing, Zeus outputs an actionable remediation diagnostic and install instructions.
- **Agentic HCL Generation & Modification**: Generates modular Terraform code following HashiCorp best practices (`main.tf`, `variables.tf`, `outputs.tf`, `providers.tf`).
- **Interactive Guardrail Loop**:
  1. HCL Code Generation / Modification
  2. Syntax & Linting (`terraform fmt`, `terraform validate`)
  3. Execution Plan (`terraform plan -out=tfplan`) with parsed blast-radius analysis
  4. User confirmation & approval checkpoint
  5. Controlled execution (`terraform apply tfplan`)

### 6. 🚦 Jenkins Automation Hub
- **Connectivity & Credential Gate**: Verifies Jenkins connectivity (CLI or REST API via URL + User API Token). Clear error notification with configuration prompts if unreachable.
- **Token-Minimal Pipeline Control**: Trigger parameterized builds, inspect running pipeline stages, and stream progressive logs with automated windowing.
- **DevOps Loop**: Connects Terraform provisioning output directly into Jenkins pipeline parameters for downstream application deployment.

### 7. 🎨 Diagram-to-Terraform (Draw.io & Vision Pipeline)
- **Draw.io (`.drawio`, `.xml`) Native Parser**:
  - Parses mxGraph XML hierarchies (VPCs, Subnets, Security Groups, EC2/ECS instances, Load Balancers, Route Tables, Gateways).
  - Preserves structural relationships (e.g., node inside a subnet container inherits CIDR and subnet placement).
- **Multimodal Diagram Vision Parser (PNG, JPEG, WebP)**:
  - Uses multimodal AI vision to decompose architecture diagrams into cloud components, ingress/egress relationships, and network boundaries.
- **Full Transparency & Review**:
  - Displays generated Terraform code side-by-side or in structured preview files before touching the disk.
  - Automatically executes `terraform init`, `terraform plan`, and prompts for user approval before `terraform apply`.

### 8. 🖥️ Unified Multi-Cloud VM & Compute Manager (`//VMs`)
- **Multi-Cloud Provider Abstraction**:
  - **AWS**: EC2 instances, ECS services/tasks (drawing from `ec2s`).
  - **Alibaba Cloud**: ECS instances, vSwitches (drawing from `a1s`).
  - **GCP / Azure / DigitalOcean / Hetzner**: Via cloud APIs.
  - **Generic / Custom VPS / On-Premise**: Pure SSH (IP/Hostname + SSH Key / Password).
- **Metadata & Notes**: Annotate any VM with custom notes, environment tags (`prod`, `staging`, `dev`), cloud provider flags, owner, and purpose.
- **Remote Operations & SSH Access**:
  - Direct one-click or command-line SSH session attachment (`//VMs ssh <id>`).
  - Non-interactive batch command execution across one or multiple targets (`//VMs exec --tag=web "uptime"`).
  - Cloud provider API actions (Start, Stop, Restart, Terminate, Snapshot).
- **The `//VMs` Command Experience**:
  - Fast CLI/slash-command mode for quick scripting (`//VMs list`, `//VMs add`, `//VMs edit`, `//VMs connect`).
  - Optional Interactive TUI dashboard inspired by `k9s` / `ec2s` / `a1s`.

---

## 💻 The `//VMs` Command Design

When the user enters `//VMs` in the Zeus harness, the system provides both an interactive prompt and command arguments:

```bash
# Display the interactive VM inventory dashboard
//VMs

# Quick command options
//VMs list [--provider=aws|alicloud|generic] [--status=running]
//VMs add  [--provider=<name>] [--ip=<ip>] [--key=<path>] [--notes="web server"]
//VMs edit <vm-id> [--notes="new notes"] [--tags="prod,api"]
//VMs ssh  <vm-id>
//VMs exec <vm-id> -- <command>
//VMs sync [--all]
```

### VM Unified Schema:
```json
{
  "id": "vm-us-east-web-01",
  "name": "Production-API-Gateway",
  "provider": "aws",
  "native_id": "i-0a1b2c3d4e5f67890",
  "region": "us-east-1",
  "vpc_id": "vpc-0123456789abcdef0",
  "subnet_id": "subnet-0123456789abcdef0",
  "public_ip": "54.210.12.34",
  "private_ip": "10.0.1.50",
  "state": "running",
  "ssh": {
    "user": "ubuntu",
    "key_path": "~/.ssh/zeus_prod.pem",
    "port": 22
  },
  "notes": "Main FastAPI Gateway behind ALB - Managed by Zeus",
  "tags": {
    "env": "production",
    "tier": "frontend",
    "project": "ecommerce"
  }
}
```

---

## 🛠️ Prerequisites & Error Handling Philosophy

Zeus treats external dependencies with strict preflight verification. When an essential tool is absent, Zeus stops gracefully and offers exact instructions:

| Dependency | Purpose | Failure Mode & Remediation |
|---|---|---|
| **Terraform** (`terraform`) | IaC Generation, Plan, Apply | **Error:** `[ZEUS-ERR-101] Terraform binary not detected in PATH.`<br>Displays platform-specific installation command (e.g., `winget install HashiCorp.Terraform` or `brew install terraform`) with direct link to [HashiCorp Terraform Installation Guide](https://developer.hashicorp.com/terraform/install). |
| **Jenkins** (`jenkins-cli` / API) | CI/CD Trigger & Status Polling | **Error:** `[ZEUS-ERR-102] Jenkins server unreachable or credentials unset.`<br>Prompts user to configure `JENKINS_URL`, `JENKINS_USER`, and `JENKINS_API_TOKEN` or review [Jenkins Installation Documentation](https://www.jenkins.io/doc/book/installing/). |
| **Cloud Credentials** | AWS CLI (`~/.aws`), Alicloud (`~/.aliyun`), etc. | Notifies if profile or environment variables (`AWS_ACCESS_KEY_ID`, etc.) are unconfigured before initiating provider calls. |
| **SSH Client** | Interactive VM Shells | Uses system `ssh` or embedded SSH client library (Go `golang.org/x/crypto/ssh` / Node `ssh2`). |

---

## 📂 Implementation Roadmap

The project is structured into incremental, battle-tested milestones detailed in [`PLAN.md`](./PLAN.md):

1. **Phase 1: Agentic Core, Dependency Harness & Token-Minimal Pipeline** (Terraform & Jenkins preflight, error handling, tool harness, Plan/Build mode switch, Hermes-style persistent memory, Pi-style minimal-token output filter & smart Terraform/Jenkins error parsers).
2. **Phase 2: Visual IaC Engine** (Draw.io XML parser + PNG vision prompt -> HCL generator -> Review -> `init`/`plan`/`apply`).
3. **Phase 3: Cloud Discovery, Unified Multi-Cloud VM Manager & Discovery** (`//VMs`, AWS/Alicloud VPCs/Subnets/vSwitches/ECS discovery, `infra.md` export).
4. **Phase 4: Strict Permission Guardrails & Agentic Ops** (Natural language query engine, destructive action confirmation gates, SSH sessions).
5. **Phase 5: Extensibility & Packaging** (Plugins, CLI distribution, persistent SQLite state).

---

## 🤝 Inspirations & Acknowledgments

- [earendil-works/pi](https://github.com/earendil-works/pi) - Lightweight agent harness architecture & token-minimal tool philosophy.
- [NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent) - Persistent project memory and environmental constraint retention.
- [anomalyco/opencode](https://github.com/anomalyco/opencode) - Multi-agent planning, Plan/Build mode separation, & developer experience.
- [herdrdev/herdr](https://github.com/herdrdev/herdr) - Agent-driven multi-server session persistence.
- [PYTHON01100100/ec2s](https://github.com/PYTHON01100100/ec2s) - Terminal UI for Amazon EC2.
- [PYTHON01100100/a1s](https://github.com/PYTHON01100100/a1s) - Terminal UI for Alibaba Cloud ECS.
- [HashiCorp Terraform](https://developer.hashicorp.com/terraform) - Standard for Infrastructure as Code.
- [Jenkins](https://www.jenkins.io/) - Open source automation server.
