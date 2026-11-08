# 📋 Zeus: Master Implementation & Architecture Plan

> **Project Target:** Build an autonomous, agentic AI tool and harness for cloud infrastructure management, Terraform visual synthesis, Jenkins pipeline orchestration, and multi-cloud VM management.

---

## 🏗️ 1. Architecture & Technology Stack Strategy

### 1.1 Technical Foundations Evaluation

Based on the inspirations ([`pi`](https://github.com/earendil-works/pi), [`hermes-agent`](https://github.com/NousResearch/hermes-agent), [`opencode`](https://github.com/anomalyco/opencode), [`herdr`](https://github.com/herdrdev/herdr), [`ec2s`](https://github.com/PYTHON01100100/ec2s), [`a1s`](https://github.com/PYTHON01100100/a1s)):

| Component | Recommended Technology | Why It Fits |
|---|---|---|
| **Core Language** | **Go (Golang)** or **TypeScript / Node.js** | • **Go (Recommended)**: User has deep proven experience building high-performance terminal cloud tools (`ec2s`, `a1s`). Go provides single static binaries, native concurrency for multi-cloud queries, rich TUI libraries (`bubbletea`, `tview`), and native SSH libraries (`golang.org/x/crypto/ssh`).<br>• **TypeScript (Alternative)**: Matches `pi` and `opencode` harnesses, fast prompt iterations, native JSON handling. |
| **Agentic Loop & Modes** | **Plan & Build Split Harness (with Pi Token Efficiency)** | Decouples safe exploration and questioning (`PLAN` mode) from active execution and provisioning (`BUILD` mode). Implements Pi's minimal-token philosophy to keep agent context fast, clean, and hallucination-free. |
| **Persistent Memory** | **Hermes-Style Project Context Engine** | Stores infrastructure constraints (on-prem, hybrid, Direct Connect, VPN, CIDR reservations) in `.zeus/memory.yaml` to prevent architectural collisions. |
| **TUI / CLI Engine** | **Charm Bubbletea / Lipgloss** (Go) or **Ink** (TypeScript) | Enables seamless switching between conversational chat, interactive fuzzy-search tables, and command execution. |
| **Local State Store** | **SQLite (via modernc.org/sqlite or SQLite3) or Encrypted JSON** | Stores VM metadata, notes, SSH profiles, cached credentials, and execution history locally at `~/.zeus/`. |

---

## 🧭 2. Operational Modes: `Plan` vs `Build`

Zeus enforces a strict separation between inquiry and mutation:

```
                      ┌────────────────────────────┐
                      │    ZEUS AGENT HARNESS      │
                      │  Mode: [ PLAN ] | [ BUILD ]│
                      └─────────────┬──────────────┘
                                    │
             ┌──────────────────────┴──────────────────────┐
             ▼                                             ▼
  ┌─────────────────────────────────┐     ┌─────────────────────────────────┐
  │         🔍 PLAN MODE            │     │         🔨 BUILD MODE           │
  │     (Read-Only / Inquiry)       │     │     (Execution / Mutation)      │
  ├─────────────────────────────────┤     ├─────────────────────────────────┤
  │ • Ask about VPCs, Subnets, IPs  │     │ • Provision via Terraform apply │
  │ • Query ECS clusters & services │     │ • Start / Stop / Reboot VMs     │
  │ • Query Alibaba Cloud vSwitches │     │ • Trigger Jenkins CI/CD jobs    │
  │ • Consult on-prem & VPN memory  │     │ • Run remote SSH exec commands  │
  │ • Dry-run terraform plan        │     │ • STRICT PERMISSION GATE:       │
  │ • Export full infra.md report   │     │   Requires user approval for    │
  │ • ZERO mutation / zero deletion │     │   destroying/deleting resources │
  └─────────────────────────────────┘     └─────────────────────────────────┘
```

### Mode Rules:
- **`PLAN` Mode (Default)**:
  - Tool calls are restricted to read-only queries (`describe_*`, `list_*`, `get_*`, `terraform_validate`, `terraform_plan`, `export_infra_md`, `read_memory`).
  - Perfect for questioning: *"What subnets do I have in us-east-1?"*, *"Show all public IPs"*, *"Does our plan overlap with on-prem Direct Connect?"*.
- **`BUILD` Mode**:
  - Mutating tools become available (`terraform_apply`, `terraform_destroy`, `delete_ecs`, `stop_instance`, `ssh_exec`).
  - **Permission Checkpoint**: Any destructive action prompts the user with a blast-radius summary before calling cloud APIs.

---

## 🗺️ 3. Segmented Implementation Phases

```
┌────────────────────────────────────────────────────────────────────────┐
│ PHASE 1: Harness Foundation, Preflight Gates, Minimal-Token Pipeline   │
│         & Persistent Project Memory (Hermes Engine)                   │
└──────────────────────────────────┬─────────────────────────────────────┘
                                   │
┌──────────────────────────────────▼─────────────────────────────────────┐
│ PHASE 2: Visual Architecture to IaC (Draw.io & Vision PNG -> HCL)     │
│         (XML Parser, Vision Pipeline, HCL Review & Apply)             │
└──────────────────────────────────┬─────────────────────────────────────┘
                                   │
┌──────────────────────────────────▼─────────────────────────────────────┐
│ PHASE 3: Cloud Discovery, Unified VM Manager (`//VMs`) & infra.md      │
│         (VPCs, Subnets, vSwitches, ECS, Public IPs, infra.md Exporter)│
└──────────────────────────────────┬─────────────────────────────────────┘
                                   │
┌──────────────────────────────────▼─────────────────────────────────────┐
│ PHASE 4: Strict Permission Gates & Agentic Ops Guardrails              │
│         (Delete Confirmations, Blast-Radius Detection, SSH Sessions)  │
└──────────────────────────────────┬─────────────────────────────────────┘
                                   │
┌──────────────────────────────────▼─────────────────────────────────────┐
│ PHASE 5: Distribution, Documentation & Extension Ecosystem             │
└────────────────────────────────────────────────────────────────────────┘
```

---

## 📍 Phase 1: Harness Foundation, Preflight Gates & Persistent Memory

The foundation of Zeus is an agent harness that enforces strict environment safety, token efficiency, and architectural memory retention.

### 1.1 Preflight Environment Checks & Diagnostic Engine
- [ ] **Terraform Binary Detector**:
  - Check `$PATH` for executable `terraform`.
  - Run `terraform version -json` to inspect semantic version and provider capabilities.
  - **Graceful Failure Handler**:
    ```
    ❌ [ZEUS-ERR-101] Terraform is not installed or not in PATH.
       Zeus requires Terraform (>= 1.5.0) to generate and apply IaC plans.
       
       👉 Quick Install:
          • Windows: winget install HashiCorp.Terraform   (or choco install terraform)
          • macOS:   brew tap hashicorp/tap && brew install hashicorp/tap/terraform
          • Linux:   sudo apt-get install terraform (via HashiCorp official repo)
       📖 Documentation: https://developer.hashicorp.com/terraform/install
    ```
- [ ] **Jenkins Connectivity & Auth Checker**:
  - Read configuration from environment or `~/.zeus/config.yaml`:
    `JENKINS_URL`, `JENKINS_USER`, `JENKINS_API_TOKEN` / `JENKINS_PASSWORD`.
  - Validate connection via HTTP `GET /api/json` with basic auth.
  - Verify CSRF crumb issuer (`/crumbIssuer/api/json`).
  - **Graceful Failure Handler**:
    ```
    ❌ [ZEUS-ERR-102] Jenkins server is unreachable or credentials are missing.
       Zeus cannot trigger or monitor CI/CD pipelines without a configured instance.
       
       👉 How to resolve:
          1. Set your environment variables:
             export JENKINS_URL="http://your-jenkins-host:8080"
             export JENKINS_USER="your-username"
             export JENKINS_API_TOKEN="your-api-token"
          2. Or run: //config jenkins
       📖 Documentation: https://www.jenkins.io/doc/book/installing/
    ```

### 1.2 The Agentic Harness (Plan/Build Split)
- [ ] **Core Agent Loop**:
  - Context manager maintaining conversation history, active workspace path, and loaded tool schemas.
  - Mode Controller: `Mode = ModePlan | ModeBuild`.
  - Slash command `//mode plan` and `//mode build` to switch states.
  - Multi-model LLM abstraction (OpenAI, Anthropic Claude, Google Gemini, Ollama/local).
  - Streaming token output with live terminal spinners and formatted markdown blocks.
- [ ] **Terraform Tool Suite**:
  - `terraform_init(dir)`: Run `terraform init` and capture provider plugin downloads.
  - `terraform_validate(dir)`: Run `terraform validate -json` to check syntax and schemas.
  - `terraform_plan(dir, out)`: Run `terraform plan -detailed-exitcode -out=tfplan` and parse summary:
    - `Resources: X to add, Y to change, Z to destroy`.
  - `terraform_apply(dir, plan_file)`: Run `terraform apply` **only in BUILD mode with human approval**.
- [ ] **Jenkins Tool Suite**:
  - `jenkins_list_jobs()`: Fetch all available jobs/pipelines.
  - `jenkins_build_job(job_name, params)`: Trigger job with parameters (BUILD mode).
  - `jenkins_get_build_status(job_name, build_number)`: Inspect status (SUCCESS, FAILURE, BUILDING).
  - `jenkins_stream_console(job_name, build_number)`: Stream progressive console logs with log windowing.

### 1.3 Minimal-Token Tool Pipeline (Pi Philosophy)
Inspired by [`pi`](https://github.com/earendil-works/pi), Zeus prevents context bloat and token exhaustion:
- [ ] **Dual-Stream Architecture**:
  - **Human Stream**: Streams full interactive stdout/stderr (colors, progress spinners, detailed logs) directly into the operator terminal.
  - **Agent Stream**: Filters, condenses, and formats execution results into lean semantic summaries (< 250 tokens per tool result).
- [ ] **Task-Focused Read Tools**:
  - Never dump 5,000-line raw cloud dumps or huge `.tfstate` files into LLM memory.
  - Tools extract strictly relevant slices:
    - `read_task_summary(task_id)`: Extracts status, elapsed time, and exit status.
    - `filter_tf_plan(plan_json)`: Extracts only modified resources, omitting unchanged state.
    - `tail_error_lines(log, max_lines=20)`: Reads only the terminal segment of build logs.

### 1.4 Smart Terraform Error Parsing & Diagnostic Engine
When Terraform encounters a failure, raw CLI output can be noisy, confusing, and full of ANSI codes. Zeus intercepts errors and parses them into clear, actionable diagnostics:
- [ ] **Parser Implementation (`parse_tf_diagnostics`)**:
  - Intercepts stderr and `terraform validate -json` output.
  - Strips ANSI color escapes and verbose HashiCorp telemetry banners.
  - Extracts 4 key elements:
    1. **Severity & Error Type**: (e.g. `SyntaxError`, `MissingRequiredArgument`, `ResourceNotFound`, `ProviderAuthFailure`, `CycleDependency`).
    2. **Exact File & Line**: (e.g. `main.tf:42`).
    3. **Code Snippet**: Exact offending block with context lines.
    4. **Actionable Remediation**: Specific fix recommendations (e.g., `Attribute "instanc_type" is invalid. Did you mean "instance_type"?`).
- [ ] **Clean Diagnostic Card Format**:
  ```
  🚨 TERRAFORM VALIDATION ERROR
  File: compute.tf:18
  Block: resource "aws_instance" "web"

     17 |   ami           = "ami-0c55b159cbfafe1f0"
  >  18 |   instanc_type  = "t3.micro"
     19 |   subnet_id     = aws_subnet.public.id

  ❌ Cause: Argument named "instanc_type" is not expected here.
  💡 Suggested Fix: Did you mean "instance_type"?
  ```
- [ ] **Zero-Hallucination Self-Healing Loop**:
  - Feeds the exact parsed diagnostic card directly to the LLM agent.
  - Agent immediately repairs the offending line in `compute.tf` without burning thousands of tokens on irrelevant log lines.

### 1.5 Smart Jenkins Error Parsing & Log Summarization Engine
Jenkins builds often produce massive console logs (often 10,000+ lines). Dumping these into prompt memory destroys context and wastes tokens. Zeus intercepts failed builds and parses them into concise diagnostic cards:
- [ ] **Failure Stage Detection**:
  - Queries Jenkins Pipeline Workflow API (`GET /job/{jobName}/{buildNumber}/wfapi/describe`).
  - Traverses stages to pinpoint the exact failed node (e.g., `Stage: "Docker Build"`, `Stage: "Unit Tests"`).
- [ ] **Log Windowing & Extraction**:
  - Extracts only the log chunk for the failed stage (`GET /job/{jobName}/{buildNumber}/execution/node/{nodeId}/wfapi/log`).
  - Scans for failure triggers: `ERROR:`, `FATAL:`, `Exception:`, `exit status 1`, or `failed with exit code`.
  - Windows only the surrounding 20-30 lines.
- [ ] **Jenkins Diagnostic Card Format**:
  ```
  🚨 JENKINS BUILD FAILURE
  Job: deploy-production-backend #42
  Failed Stage: [Docker Build] (Duration: 1m 12s)

     > Step: sh 'docker build -t app:v2.1 .'
     > Error: Dockerfile:14: COPY failed: file not found in build context: config/prod.json

  ❌ Cause: Missing build artifact 'config/prod.json'.
  💡 Suggested Fix: Ensure config/prod.json is committed or generated in a previous pipeline stage.
  ```
- [ ] **Token-Minimal Agent Response**:
  - Sends a concise $< 300$ token summary payload to the LLM agent instead of megabytes of raw console text.

### 1.6 Persistent Project Memory Engine (Hermes Architecture)
Inspired by [`hermes-agent`](https://github.com/NousResearch/hermes-agent), Zeus retains long-term infrastructure facts across sessions to avoid dangerous routing, CIDR, or compliance collisions:
- [ ] **Storage Location & Schema**:
  - Persisted in `.zeus/memory.yaml` (project root) or user global store `~/.zeus/memory.yaml`.
  - Structured fields:
    ```yaml
    deployment_type: "hybrid" # on_prem | hybrid | multi_cloud
    interconnects:
      - type: "direct_connect"
        provider: "aws"
        asn: 65000
        remote_ip_range: "192.168.0.0/16"
        notes: "Primary 10G link to Riyadh On-Prem Datacenter"
      - type: "site_to_site_vpn"
        provider: "alicloud"
        remote_ip_range: "172.16.0.0/16"
        notes: "IPsec tunnel to backup DC"
    reserved_cidrs:
      - "192.168.0.0/16" # Riyadh DC
      - "10.200.0.0/16"  # Corporate Office VPN
    policies:
      - "Never expose database instances (MySQL/Postgres) with public IPs"
      - "All egress traffic from production VPCs must route via on-prem firewall"
    ```
- [ ] **Natural Language & Command Memory Input**:
  - Natural language ingestion:
    - *"Zeus, remember that our on-prem datacenter uses 192.168.0.0/16 and connects to AWS via Direct Connect."*
  - Command: `//memory` (displays memory card) and `//memory add "<rule>"`.
- [ ] **Pre-Generation Safety Validation**:
  - Before writing or applying Terraform, Zeus cross-checks planned VPCs/Subnets against `memory.yaml`.
  - Automatically raises collision warnings:
    ```
    ⚠️  ARCHITECTURAL COLLISION WARNING:
        Planned Subnet: 192.168.1.0/24
        Conflicts with Stored Memory: On-Prem Direct Connect range (192.168.0.0/16)
        
        Fix: Allocating non-overlapping CIDR block (e.g. 10.10.1.0/24) instead.
    ```

---

## 📍 Phase 2: Visual Architecture to IaC (Draw.io & Vision PNG -> HCL)

Transform visual designs into validated, deployable Terraform modules with human review.

```
┌─────────────────────────┐
│ Input Architecture      │
│ • Draw.io (.xml/.drawio)│
│ • PNG / JPG / Screenshot│
└────────────┬────────────┘
             │
             ▼
┌─────────────────────────────────────────┐
│ Parser / Ingestion Engine               │
│ • mxGraph XML AST Extractor (Draw.io)   │
│ • Multimodal Vision Prompt (Images)     │
└────────────┬────────────────────────────┘
             │
             ▼ Intermediate Schema (Topology JSON)
┌─────────────────────────────────────────┐
│ HCL Synthesis & Module Generator        │
│ • main.tf, variables.tf, outputs.tf     │
│ • Provider & CIDR calculation           │
│ • Check against Project Memory          │
└────────────┬────────────────────────────┘
             │
             ▼
┌─────────────────────────────────────────┐
│ Review & Guardrail Execution            │
│ 1. Show complete HCL code to user       │
│ 2. Run `terraform init`                 │
│ 3. Run `terraform plan` & show diff     │
│ 4. Require explicit user approval       │
│ 5. Execute `terraform apply` (BUILD)    │
└─────────────────────────────────────────┘
```

### 2.1 Draw.io (`.drawio`, `.xml`) Structural Parser
- [ ] Parse mxGraph XML models:
  - Extract `mxCell` elements with geometry (`x`, `y`, `width`, `height`).
  - Extract container relationships: detect parent-child boundaries (e.g., node enclosed in a VPC/Subnet box).
  - Extract edges/connectors: map arrow sources and targets (e.g., Internet Gateway -> ALB -> Target Group -> EC2/ECS).
  - Extract resource labels, tags, and annotations.

### 2.2 Multimodal Vision Pipeline (PNG, JPEG, Diagrams)
- [ ] Vision Prompt Engineering:
  - Structured system prompt instructing the vision model to extract cloud infrastructure topology.
  - Return standardized intermediate topology JSON (VPCs, Subnets, EC2, ECS, Gateways, CIDRs).

### 2.3 HCL Code Generation & Execution Pipeline
- [ ] Generate standard Terraform files into target workspace:
  - `providers.tf`, `vpc.tf`, `compute.tf`, `security.tf`, `variables.tf`, `outputs.tf`.
- [ ] **Interactive User Review & Action Cycle**:
  1. Print generated files with file tree preview and syntax highlighting.
  2. Prompt user: `Proceed with terraform init & plan? [Y/n/edit]`.
  3. Execute `terraform init`.
  4. Execute `terraform plan` and display clean formatted diff.
  5. In `BUILD` mode, prompt user: `Apply this plan to cloud? [yes/abort]`.
  6. Execute `terraform apply` with real-time progress.

---

## 📍 Phase 3: Cloud Discovery, Unified VM Manager (`//VMs`) & `infra.md`

Enables users to ask about their cloud infrastructure, manage multi-cloud instances, and export audit reports.

### 3.1 Cloud Discovery Engine (Natural Language Infra Queries)
- [ ] **Multi-Provider Discovery Tools**:
  - `discover_vpcs()`:
    - AWS: Fetch VPC IDs, CIDR blocks, names, state, default VPC flags.
    - Alibaba Cloud: Fetch VPC IDs, CIDR blocks, VPC status.
  - `discover_subnets_and_vswitches()`:
    - AWS Subnets: Subnet ID, VPC ID, CIDR block, Availability Zone, Available IPv4 count, Public IP auto-assign flag.
    - Alibaba Cloud vSwitches: vSwitch ID, VPC ID, Zone, CIDR block, Available IP count.
  - `discover_compute_and_ecs()`:
    - AWS EC2: Instance ID, Name tag, Instance Type, State, Private IP, Public IP, KeyName, Subnet/VPC.
    - AWS ECS: Clusters, Services, Running Task count, Task Definitions, Launch Type (Fargate/EC2).
    - Alibaba Cloud ECS: Instance ID, Instance Name, Status, Public/EIP, Private IP, vSwitch ID, VPC ID.
    - Generic SSH VMs: Stored in local inventory (`~/.zeus/inventory.json`).
- [ ] **Natural Language Query Handler**:
  - Allows questions like:
    - *"Show all VPCs and their CIDRs across AWS and Alibaba Cloud"*
    - *"List all subnets and vSwitches with remaining IP capacity"*
    - *"Show ECS services that have public IPs"*
    - *"What is the public IP of the production database server?"*

### 3.2 One-Click `infra.md` Exporter
- [ ] Feature `export infra.md` (or command `//export infra.md`):
  - Queries all connected cloud providers and local inventory.
  - Incorporates stored on-prem/hybrid memory context.
  - Generates a well-formatted Markdown document in the workspace:
    ```markdown
    # 🌐 Cloud Infrastructure Snapshot (infra.md)
    *Generated by Zeus on 2026-11-08 17:35 UTC*

    ## 1. Networks & VPCs
    | Provider | VPC ID | Name | CIDR Block | Region | Subnets / vSwitches |
    |---|---|---|---|---|---|
    | AWS | `vpc-01ab` | Main-VPC | `10.0.0.0/16` | us-east-1 | 4 Subnets |
    | Alibaba | `vpc-88cd` | Ali-VPC | `172.16.0.0/12` | me-central-1 | 2 vSwitches |

    ## 2. Subnets & vSwitches
    | Provider | Subnet / vSwitch ID | Name | Zone | CIDR | Available IPs |
    |---|---|---|---|---|---|
    | AWS | `subnet-0a1` | public-1a | us-east-1a | `10.0.1.0/24` | 251 |
    | Alibaba | `vsw-0b2` | ali-vsw-1 | me-central-1a | `172.16.1.0/24` | 250 |

    ## 3. Compute Fleet & ECS
    | ID | Name | Provider | Type | Private IP | Public IP | Status | Notes |
    |---|---|---|---|---|---|---|---|
    | `i-01` | api-gateway | AWS (EC2) | t3.medium | `10.0.1.15` | `54.21.90.11` | RUNNING ● | FastAPI Gateway |
    | `ecs-02` | order-svc | AWS (ECS) | Fargate | `10.0.2.80` | - | RUNNING ● | Order Processing |
    | `i-88` | db-master | Ali (ECS) | ecs.g6 | `172.16.1.10` | `47.88.12.30` | RUNNING ● | MySQL Cluster |
    | `vm-03` | hetzner-ci | Generic | VPS | `10.10.0.5` | `159.69.4.12` | RUNNING ● | Jenkins Runner |
    ```

### 3.3 The `//VMs` Unified Manager
- [ ] Dual Hybrid Interface:
  - Interactive TUI (`//VMs`) with fuzzy search table inspired by `ec2s` and `a1s`.
  - Command mode: `//VMs list`, `//VMs add`, `//VMs edit`, `//VMs ssh`, `//VMs exec`.
- [ ] Persistent User Notes & Metadata:
  - Add/edit custom notes for any instance regardless of cloud provider.
  - Persisted in `~/.zeus/inventory.json`.

---

## 📍 Phase 4: Strict Permission Gates & Agentic Ops Guardrails

Enforces absolute human control over cloud mutations, especially resource destruction.

### 4.1 Strict Permission Gate Engine (Delete / Destroy Protection)
- [ ] **Middleware Interceptor for Destructive Actions**:
  - Applies to:
    - `delete_ecs_service` / `delete_ecs_cluster`
    - `terminate_ec2_instance` / `delete_alicloud_instance`
    - `terraform_destroy`
    - `terraform_apply` where parsed plan contains deletions ($Z > 0$ resources to destroy)
    - Security group ingress removal or Route table deletion
- [ ] **Blast-Radius Interactive Gate**:
  - Halts execution and presents a formatted warning card:
    ```
    🚨 DESTRUCTIVE OPERATION DETECTED
    Action: Terminate Compute / Destroy Infrastructure
    Impacted Resources (Blast Radius: 2):
      • [AWS EC2]   i-0a1b2c3d4e5f (Production-API-Gateway, Public IP: 54.210.12.34)
      • [AWS ECS]   service/order-processing-service (Tasks: 3)

    ⚠️  This action CANNOT be undone.
    Type 'CONFIRM-DELETE' to authorize this deletion or press [Esc] to cancel.
    ```
  - **No automated LLM bypass**: The user must explicitly type the authorization string.

### 4.2 Interactive & Remote SSH Execution
- [ ] Native terminal attachment (`//VMs ssh <id>`).
- [ ] Non-interactive remote command exec (`//VMs exec <id> -- "<command>"`).
- [ ] Output capture for agentic root-cause troubleshooting.

---

## 📍 Phase 5: Distribution, Documentation & Extension Ecosystem

- [ ] **CLI Binary Distribution**: Single standalone binary for Windows (`.exe`), macOS, Linux.
- [ ] **Slash Command System**: `//mode`, `//vms`, `//export`, `//memory`, `//terraform`, `//jenkins`, `//config`.
- [ ] **End-to-End Documentation & Examples**.

---

## 📅 Step-by-Step Execution Sequence

1. ✅ **Milestone 1**: Project Vision (`README.md`) & Master Architecture (`PLAN.md`) with Plan/Build modes, Permission Gates, and Hermes Persistent Memory.
2. 🔄 **Milestone 2**: Project Scaffolding, Mode Switcher, Persistent Memory Store, Preflight Verifiers (Terraform & Jenkins), and Minimal-Token Tool Pipeline.
3. 🔄 **Milestone 3**: Cloud Discovery Engine (VPCs, Subnets, vSwitches, ECS, Public IPs) & `infra.md` Exporter.
4. 🔄 **Milestone 4**: Visual IaC Module (Draw.io XML parser + Multimodal Vision converter + HCL Generator with Memory cross-checks).
5. 🔄 **Milestone 5**: Unified `//VMs` Multi-Cloud & SSH Management Engine (AWS, Alibaba Cloud, Generic SSH, Notes & CLI/TUI).
6. 🔄 **Milestone 6**: Strict Human-in-the-Loop Permission Gates & Blast-Radius Engine.
