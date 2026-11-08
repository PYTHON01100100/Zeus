# 📋 Zeus: Master Implementation & Architecture Plan

> **Project Target:** Build an autonomous, safety-first agentic AI harness for cloud infrastructure management, Terraform visual synthesis, Jenkins pipeline orchestration, multi-cloud VM fleet operations, extensible MCP/Skills plugins, and multi-channel control via CLI and Telegram.

---

## 🏗️ 1. Architecture & Technology Stack Strategy

### 1.1 Technical Foundations Evaluation

Based on the core inspirations ([`pi`](https://github.com/earendil-works/pi), [`hermes-agent`](https://github.com/NousResearch/hermes-agent), [`opencode`](https://github.com/anomalyco/opencode), [`herdr`](https://github.com/herdrdev/herdr), [`ec2s`](https://github.com/PYTHON01100100/ec2s), [`a1s`](https://github.com/PYTHON01100100/a1s)):

| Component | Recommended Technology | Why It Fits |
|---|---|---|
| **Core Language** | **Go (Golang)** or **TypeScript / Node.js** | • **Go (Recommended)**: User has deep proven experience building high-performance terminal cloud tools (`ec2s`, `a1s`). Go provides single static binaries, native concurrency for multi-cloud queries, rich TUI libraries (`bubbletea`, `tview`), and native SSH libraries (`golang.org/x/crypto/ssh`).<br>• **TypeScript (Alternative)**: Matches `pi` and `opencode` harnesses, fast prompt iterations, native JSON handling. |
| **Agentic Loop & Modes** | **Plan & Build Split Harness (with Pi Token Efficiency)** | Decouples safe exploration and questioning (`PLAN` mode) from active execution and provisioning (`BUILD` mode). Implements Pi's minimal-token philosophy to keep agent context fast, clean, and hallucination-free. |
| **LLM Provider Engine (`/provider`)** | **Multi-Provider Client Abstraction** | Seamlessly switch between local models (**Ollama**, **vLLM**) and cloud APIs (**OpenAI**, **Anthropic Claude**, **Google Gemini**, OpenRouter, DeepSeek). Dynamic hot-swapping via `/provider` or `//provider`. |
| **Extensible Tooling (MCP)** | **Model Context Protocol (MCP) Client** | First-class MCP host/client supporting `stdio` sub-processes and `sse`/HTTP endpoints. Dynamically bridges external community MCP servers into Zeus's tool registry. |
| **Modular Skills & Plugins** | **Progressive Disclosure Skills (`SKILL.md`) & Plugin Manifests** | On-demand skill indexing and redistributable plugins (`plugin.yaml`) bundling tools, MCP configs, companion scripts, and prompts without code modification. |
| **Multi-Channel Operations** | **Dual CLI & Telegram Bot Gateway** | Command and query Zeus via interactive CLI terminal or via **Telegram Bot** (`zeus serve --channel telegram`) with mobile inline keyboard approvals for blast-radius confirmations. |
| **Persistent Memory** | **Hermes-Style Project Context Engine** | Stores infrastructure constraints (on-prem, hybrid, Direct Connect, VPN, CIDR reservations) in `.zeus/memory.yaml` to prevent architectural collisions. |
| **TUI / CLI Engine** | **Charm Bubbletea / Lipgloss** (Go) or **Ink** (TypeScript) | Enables seamless switching between conversational chat, interactive fuzzy-search tables, and command execution. |
| **Local State Store** | **SQLite (via modernc.org/sqlite or SQLite3) or Encrypted JSON** | Stores VM metadata, notes, SSH profiles, cached credentials, and execution history locally at `~/.zeus/`. |

---

## 🧭 2. Operational Modes: `Plan` vs `Build`

Zeus enforces a strict separation between inquiry and mutation across all interfaces (CLI and Telegram):

```
                      ┌──────────────────────────────────────────┐
                      │           ZEUS AGENT HARNESS             │
                      │       Mode: [ PLAN ] | [ BUILD ]         │
                      │  Channels: Terminal CLI / Telegram Bot   │
                      └────────────────────┬─────────────────────┘
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
         │ • Read-only MCP tool execution  │     │   destroying/deleting resources │
         │ • ZERO mutation / zero deletion │     │   (CLI prompt / Telegram button)│
         └─────────────────────────────────┘     └─────────────────────────────────┘
```

### Mode Rules:
- **`PLAN` Mode (Default)**:
  - Tool calls are restricted to read-only queries (`describe_*`, `list_*`, `get_*`, `terraform_validate`, `terraform_plan`, `export_infra_md`, `read_memory`, read-only MCP queries).
  - Perfect for questioning: *"What subnets do I have in us-east-1?"*, *"Show all public IPs"*, *"Does our plan overlap with on-prem Direct Connect?"*.
- **`BUILD` Mode**:
  - Mutating tools become available (`terraform_apply`, `terraform_destroy`, `delete_ecs`, `stop_instance`, `ssh_exec`, mutating MCP tools).
  - **Permission Checkpoint**: Any destructive action prompts the operator with a blast-radius summary before calling cloud APIs.
  - Supports mobile authorization: When controlled via Telegram, sends an inline button `[ ✅ Confirm Delete ]` / `[ ❌ Abort ]`.

---

## 🗺️ 3. Segmented Implementation Phases

```
┌────────────────────────────────────────────────────────────────────────┐
│ PHASE 1: Harness Foundation, Preflight Gates, Minimal-Token Pipeline,  │
│         Persistent Memory (Hermes Engine) & Multi-LLM Provider Engine │
└──────────────────────────────────┬─────────────────────────────────────┘
                                   │
┌──────────────────────────────────▼─────────────────────────────────────┐
│ PHASE 2: Visual Architecture to IaC (Draw.io & Vision PNG -> HCL)     │
│         (XML Parser, Vision Pipeline, HCL Review & Apply)             │
└──────────────────────────────────┬─────────────────────────────────────┘
                                   │
┌──────────────────────────────────▼─────────────────────────────────────┐
│ PHASE 3: Cloud Discovery, Unified VM Manager, Fleet Jobs & infra.md    │
│         (VPCs, Subnets, vSwitches, ECS, Fleet Jobs with Jenkins)       │
└──────────────────────────────────┬─────────────────────────────────────┘
                                   │
┌──────────────────────────────────▼─────────────────────────────────────┐
│ PHASE 4: Strict Permission Gates & Agentic Ops Guardrails              │
│         (Delete Confirmations, Blast-Radius Detection, SSH Sessions)  │
└──────────────────────────────────┬─────────────────────────────────────┘
                                   │
┌──────────────────────────────────▼─────────────────────────────────────┐
│ PHASE 5: Extensibility Ecosystem: Model Context Protocol (MCP),       │
│         Skills Framework & Plugin Architecture                        │
└──────────────────────────────────┬─────────────────────────────────────┘
                                   │
┌──────────────────────────────────▼─────────────────────────────────────┐
│ PHASE 6: Multi-Channel Operations & Telegram Bot Gateway               │
│         (Daemon Mode, Mobile Approvals, Inline Keyboards, Dual-Stream)│
└──────────────────────────────────┬─────────────────────────────────────┘
                                   │
┌──────────────────────────────────▼─────────────────────────────────────┐
│ PHASE 7: Distribution, Documentation & Ecosystem Packaging             │
└────────────────────────────────────────────────────────────────────────┘
```

---

## 📍 Phase 1: Harness Foundation, Preflight Gates, Persistent Memory & Multi-LLM Providers

The foundation of Zeus is an agent harness that enforces strict environment safety, token efficiency, architectural memory retention, and full multi-LLM provider agility.

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
  - Slash command `//mode plan`, `//mode build` (or `/mode` in Telegram) to switch states.
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
  - **Human Stream**: Streams full interactive stdout/stderr (colors, progress spinners, detailed logs) directly into the operator terminal or Telegram file attachments.
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

### 1.7 Multi-LLM Provider Engine & The `/provider` Dynamic Switcher
Zeus is built to be 100% vendor-agnostic and privacy-friendly. It allows developers to seamlessly choose, hot-swap, and benchmark LLM providers during runtime via the `/provider` (or `//provider`) command:

```
┌────────────────────────────────────────────────────────────────────────┐
│                      ZEUS MULTI-PROVIDER ENGINE                        │
├───────────────────┬────────────────────────────────────────────────────┤
│ Local / Offline   │ • Ollama (qwen2.5-coder, deepseek-r1, llama3.3)   │
│ Private Clusters  │ • vLLM (self-hosted high-throughput OpenAI API)    │
│ Cloud Frontier    │ • OpenAI (gpt-4o, o1, o3-mini)                     │
│ Enterprise Ops    │ • Anthropic Claude (claude-3-7-sonnet, opus)       │
│ Multimodal IaC    │ • Google Gemini (gemini-2.5-pro, flash)            │
│ Aggregators & APIs│ • OpenRouter, DeepSeek API, Groq, LM Studio        │
└───────────────────┴────────────────────────────────────────────────────┘
```

- [ ] **Unified Provider Interface (`LLMProvider`)**:
  ```go
  type LLMProvider interface {
      Name() string
      Generate(ctx context.Context, req CompletionRequest) (*CompletionResponse, error)
      Stream(ctx context.Context, req CompletionRequest) (<-chan TokenChunk, error)
      ListModels(ctx context.Context) ([]string, error)
      HealthCheck(ctx context.Context) (time.Duration, error)
  }
  ```
- [ ] **Supported Provider Implementations**:
  1. **Ollama Adapter**:
     - Connects to local daemon (`http://127.0.0.1:11434`).
     - Auto-discovers locally pulled models via `/api/tags`.
     - Optimized for zero-egress offline infrastructure environments.
  2. **vLLM Adapter**:
     - Connects to self-hosted GPU clusters (`http://localhost:8000/v1`).
     - OpenAI-compatible chat completions with high batch concurrency and streaming support.
  3. **OpenAI Adapter**:
     - Official GPT-4o, o1, and o3-mini integration with structured JSON outputs and function calling.
  4. **Anthropic Claude Adapter**:
     - Direct Claude 3.5 Sonnet / Claude 3.7 Sonnet API with extended thinking and native tool use.
  5. **Google Gemini Adapter**:
     - Direct Gemini 2.5 Pro / Flash API with 1M+ token context and native multimodal vision for diagrams.
  6. **Generic OpenAI-Compatible Adapter**:
     - Connects to OpenRouter, DeepSeek API, LM Studio, LocalAI, or Groq via configurable `base_url` and `api_key`.
- [ ] **The `/provider` (and `//provider`) Command Suite**:
  - Interactive Mode: Typing `/provider` launches a terminal selector (or Telegram inline menu) showing:
    - Active provider & model
    - Configured providers with latency ping (e.g. `Ollama (local): 4ms`, `Anthropic: 120ms`)
    - Quick switch action
  - Command Sub-Options:
    - `//provider list` / `/provider list`: Formatted table of available providers, status, and active models.
    - `//provider set <provider> [model]`: e.g. `//provider set ollama qwen2.5-coder:32b` or `//provider set anthropic claude-3-7-sonnet`.
    - `//provider models [provider]`: Queries the provider's API to list all locally pulled or remotely accessible models.
    - `//provider test [provider]`: Sends a lightweight ping and token throughput test.
- [ ] **Dynamic Resilience & Fallback Chains**:
  - Supports automatic fallback in `~/.zeus/config.yaml`:
    If a primary cloud API returns rate limits (HTTP 429) or connection failure, Zeus automatically falls back to a secondary provider (e.g., Claude -> GPT-4o -> local vLLM).

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
  - Structured system prompt instructing the vision model (Gemini 2.5 Pro / GPT-4o) to extract cloud infrastructure topology.
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

## 📍 Phase 3: Cloud Discovery, Unified VM Manager (`//VMs`), Fleet Jobs & `infra.md`

Enforces discovery of running infrastructure, cross-cloud VM operations, fleet actions with Jenkins, and audit exports.

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
- [ ] Feature `export infra.md` (or command `//export infra.md`, or `/infra` in Telegram):
  - Queries all connected cloud providers and local inventory.
  - Incorporates stored on-prem/hybrid memory context.
  - Generates a structured Markdown document in the workspace and attaches it to Telegram if requested.

### 3.3 The `//VMs` Unified Manager
- [ ] Dual Hybrid Interface:
  - Interactive TUI (`//VMs`) with fuzzy search table inspired by `ec2s` and `a1s`.
  - Command mode: `//VMs list`, `//VMs add`, `//VMs edit`, `//VMs ssh`, `//VMs exec`.
- [ ] Persistent User Notes & Metadata:
  - Add/edit custom notes for any instance regardless of cloud provider.
  - Persisted in `~/.zeus/inventory.json`.

### 3.4 Multi-Cloud Fleet Jobs with Jenkins
- [ ] Automate operational tasks across heterogeneous compute instances (AWS EC2, Alibaba Cloud ECS, generic VMs).
- [ ] Plan Mode (Target Review) vs Build Mode (Execution Trigger).
- [ ] Preconfigured parameterized jobs (e.g. `fleet-install-docker`, `fleet-deploy-config`).
- [ ] Partial Success Handling & Targeted Retry: Retry only failed VMs with a single click or command (`//VMs retry <build_number>`).

---

## 📍 Phase 4: Strict Permission Gates & Agentic Ops Guardrails

Enforces absolute human control over cloud mutations, especially resource destruction.

### 4.1 Strict Permission Gate Engine (Delete / Destroy Protection)
- [ ] **Middleware Interceptor for Destructive Actions**:
  - Intercepts:
    - `delete_ecs_service` / `delete_ecs_cluster`
    - `terminate_ec2_instance` / `delete_alicloud_instance`
    - `terraform_destroy`
    - `terraform_apply` where parsed plan contains deletions ($Z > 0$ resources to destroy)
    - Security group ingress removal or Route table deletion
- [ ] **Blast-Radius Interactive Gate**:
  - **CLI Terminal**: Halts execution, formats blast-radius card, and requires the operator to type `CONFIRM-DELETE`.
  - **Telegram Bot Channel**: Sends a high-priority alert card with inline buttons `[ ✅ Confirm Delete ]` and `[ ❌ Abort ]` requiring explicit button tap.

### 4.2 Interactive & Remote SSH Execution
- [ ] Native terminal attachment (`//VMs ssh <id>`).
- [ ] Non-interactive remote command exec (`//VMs exec <id> -- "<command>"`).
- [ ] Output capture for agentic root-cause troubleshooting.

---

## 📍 Phase 5: Extensibility Ecosystem: Model Context Protocol (MCP), Skills & Plugins

To ensure Zeus scales endlessly without bloating the core binary, Phase 5 introduces a three-tier extensibility ecosystem: **MCP Servers**, **Progressive Disclosure Skills**, and **Packaged Plugins**.

```
                           ┌────────────────────────────┐
                           │      ZEUS EXTENSION HUB    │
                           └─────────────┬──────────────┘
                                         │
        ┌────────────────────────────────┼────────────────────────────────┐
        ▼                                ▼                                ▼
 ┌───────────────┐              ┌──────────────────┐             ┌─────────────────┐
 │  MCP CLIENT   │              │  SKILLS ENGINE   │             │ PLUGINS MANAGER │
 ├───────────────┤              ├──────────────────┤             ├─────────────────┤
 │ • Stdio / SSE │              │ • SKILL.md Docs  │             │ • plugin.yaml   │
 │ • Tool Bridge │              │ • Progressive    │             │ • Install/Update│
 │ • Resources   │              │   Disclosure     │             │ • Enable/Disable│
 │ • .zeus/mcp   │              │ • Project/Global │             │ • Bundled Suites│
 └───────────────┘              └──────────────────┘             └─────────────────┘
```

### 5.1 Model Context Protocol (MCP) Host/Client Engine
Zeus implements Anthropic's Model Context Protocol (MCP) client specification, allowing it to connect to any standard MCP server to acquire tools, prompts, and resources dynamically.

- [ ] **Transport Layer**:
  - **Stdio Transport**: Spawns local sub-processes (Node.js via `npx`, Python via `uvx`, or compiled binaries).
  - **SSE / HTTP Transport**: Connects to remote or internal enterprise MCP servers over HTTP/Server-Sent Events.
- [ ] **Configuration File (`.zeus/mcp.json` and `~/.zeus/mcp.json`)**:
  ```json
  {
    "mcpServers": {
      "github": {
        "command": "npx",
        "args": ["-y", "@modelcontextprotocol/server-github"],
        "env": {
          "GITHUB_PERSONAL_ACCESS_TOKEN": "${GITHUB_TOKEN}"
        }
      },
      "postgres-replica": {
        "command": "uvx",
        "args": ["mcp-server-postgres", "--connection-string", "${DATABASE_URL}"]
      },
      "corporate-k8s": {
        "url": "https://mcp.internal.infra.corp/sse",
        "transport": "sse"
      }
    }
  }
  ```
- [ ] **Dynamic Tool Registration & Mode Mapping**:
  - Automatically loads MCP tool definitions into Zeus's ReAct tool catalog on startup or reload.
  - Automatically classifies MCP tools into `Plan` (read-only tools, inspection) or `Build` (mutating tools) based on tool annotations or explicit configuration.
- [ ] **Slash Command Suite (`//mcp` / `/mcp`)**:
  - `//mcp list`: View connected MCP servers, active status, and discovered tools.
  - `//mcp add <name> <transport> <command/url>`: Interactively register an MCP server.
  - `//mcp reload [name]`: Refresh tools and schemas without restarting Zeus.
  - `//mcp remove <name>`: Disconnect and remove an MCP server.

### 5.2 Dynamic Skills Framework (Progressive Disclosure)
Inspired by modern skill standards, Zeus skills provide specialized operational knowledge and companion scripts in a modular structure.

- [ ] **Skill Structure Specification**:
  ```text
  .zeus/skills/
  └── kubernetes-troubleshoot/
      ├── SKILL.md              # Instructions, prompt guidance, YAML frontmatter
      ├── scripts/              # Optional companion scripts (bash/python/go)
      └── references/           # Runbooks, cheat-sheets, cluster policies
  ```
- [ ] **`SKILL.md` Schema**:
  ```markdown
  ---
  name: kubernetes-troubleshoot
  description: Diagnoses failing pods, CrashLoopBackOff, OOMKills, and PVC pending states.
  mode: plan
  triggers:
    - "pod failing"
    - "crashloop"
    - "oomkill"
    - "kubernetes error"
  dependencies:
    - kubectl
  ---

  # Operational Guidance
  When debugging Kubernetes pod issues:
  1. Inspect pod events via `kubectl describe pod <name>`
  2. Inspect previous container termination logs (`kubectl logs --previous`)
  ...
  ```
- [ ] **Progressive Disclosure Context Engine**:
  - Skill summaries (name, description, triggers) are indexed at low token cost (< 20 tokens per skill).
  - Full instructions from `SKILL.md` are only injected into agent memory when the user's inquiry matches the skill's triggers.
- [ ] **Slash Command Suite (`//skills` / `/skills`)**:
  - `//skills list`: Shows all project and global skills.
  - `//skills info <name>`: Displays skill description, mode, and parameters.
  - `//skills create <name>`: Scaffolds a new skill directory with template `SKILL.md`.

### 5.3 Extensible Plugin Packaging & Management
Plugins allow bundling multiple skills, MCP servers, custom providers, and hooks into redistributable packages.

- [ ] **Plugin Manifest (`plugin.yaml`)**:
  ```yaml
  name: "zeus-plugin-aws-advanced"
  version: "1.0.0"
  description: "Advanced AWS Transit Gateway, Direct Connect, and Cost Anomaly tools"
  author: "CloudOps Team"
  mcp_servers:
    - name: "aws-cost"
      command: "uvx"
      args: ["mcp-server-aws-cost"]
  skills:
    - "skills/transit-gateway-peering"
    - "skills/cost-anomaly-investigation"
  ```
- [ ] **Plugin Lifecycle Manager (`//plugin` / `/plugin`)**:
  - `//plugin list`: Lists installed and active plugins.
  - `//plugin install <git-url | local-path>`: Installs plugin to `~/.zeus/plugins/`.
  - `//plugin enable <name>` / `//plugin disable <name>`: Toggles plugin active state.
  - `//plugin update [name]`: Pulls latest releases and dependencies.

---

## 📍 Phase 6: Multi-Channel Operations & Telegram Bot Gateway

Zeus provides a headless daemon and messaging adapter that brings autonomous cloud management and approval workflows directly into **Telegram**.

```
┌────────────────────────────────────────────────────────────────────────┐
│                   ZEUS MULTI-CHANNEL ADAPTER LAYER                     │
├──────────────────────────────────┬─────────────────────────────────────┤
│ 💻 CLI Terminal Adapter          │ 📱 Telegram Bot Adapter             │
│ • Charm Bubbletea interactive    │ • Long-polling / Webhook listener   │
│ • Rich terminal ANSI formatting  │ • Mobile inline approval keyboards  │
│ • Local keystroke listeners      │ • Chat ID security whitelist        │
│ • Native shell attachment        │ • Dual-stream document delivery     │
└──────────────────────────────────┴─────────────────────────────────────┘
```

### 6.1 Headless Daemon & Channel Abstraction
- [ ] **Channel Adapter Interface (`ChannelAdapter`)**:
  ```go
  type ChannelAdapter interface {
      Start(ctx context.Context, harness *Harness) error
      SendMessage(chatID string, msg string) error
      SendDocument(chatID string, filename string, data []byte) error
      SendApprovalPrompt(chatID string, prompt ApprovalPrompt) (bool, error)
  }
  ```
- [ ] **Daemon Mode CLI Command**:
  - Run Zeus as a persistent background listener:
    ```bash
    zeus serve --channel telegram
    ```
  - Allows simultaneous CLI sessions and Telegram control on shared project state.

### 6.2 Telegram Bot Integration & Security Authentication
- [ ] **Telegram Bot Gateway**:
  - Implements the Telegram Bot API with robust long-polling and optional webhook support.
  - Configured via `~/.zeus/config.yaml` or environment variable `ZEUS_TELEGRAM_BOT_TOKEN`.
- [ ] **Strict Access Control & Whitelist Engine**:
  - Restricts bot interactions strictly to authorized Telegram user/chat IDs:
    ```yaml
    telegram:
      bot_token: "${ZEUS_TELEGRAM_BOT_TOKEN}"
      allowed_chat_ids:
        - 123456789
        - 987654321
      require_admin_pin: true
    ```
  - Unrecognized users receive a security denial notification; attempts are logged to `~/.zeus/security.log`.

### 6.3 Telegram Commands & Interactive Inline Keyboards
- [ ] **Supported Telegram Bot Slash Commands**:
  - `/start`: Initializes session, displays welcome dashboard, active provider, and quick action keyboard.
  - `/provider`: Displays interactive inline keyboard with available providers:
    ```
    ┌──────────────────────────────┐
    │ ⚙️ Select LLM Provider:       │
    ├──────────────┬───────────────┤
    │ [ 🦙 Ollama ]│ [ ⚡ vLLM ]   │
    ├──────────────┼───────────────┤
    │ [ 🧠 OpenAI ]│ [ 🎭 Claude ] │
    ├──────────────┴───────────────┤
    │ [ 💎 Google Gemini ]         │
    └──────────────────────────────┘
    ```
  - `/mode`: Toggles operational mode with buttons `[ 🔍 PLAN ]` and `[ 🔨 BUILD ]`.
  - `/vms`: Renders interactive VM fleet summary with quick status indicators.
  - `/infra`: Generates and exports the complete `infra.md` documentation file.
  - `/plan`: Initiates safe dry-run Terraform plans or fleet reviews.
  - `/build`: Switches to active build mode with guardrails.
  - `/mcp`: Lists active MCP servers and registered external tools.
  - `/help`: Detailed reference card of all Telegram commands.

### 6.4 Mobile Human-in-the-Loop Blast-Radius Approvals
- [ ] **Interactive Inline Approval Cards**:
  When Zeus detects a mutating or destructive action (e.g. `terraform apply` with deletions, `terminate_instance`, `delete_ecs_service`), it halts and pushes an immediate Telegram approval card:
  ```
  🚨 DESTRUCTIVE OPERATION DETECTED
  Action: Terminate EC2 Instance
  Target: i-0a1b2c3d4e5f (Production-API-Gateway)
  Blast Radius: 1 Compute Resource
  ⚠️ This action CANNOT be undone.

  [ ✅ Confirm Delete ]        [ ❌ Abort ]
  ```
- [ ] **Callback Handling**:
  - Tapping `[ ✅ Confirm Delete ]` verifies the user's authorization and resumes execution.
  - Tapping `[ ❌ Abort ]` immediately terminates the plan and returns a clean abort confirmation.

### 6.5 Dual-Stream Delivery for Telegram
- [ ] **Token-Minimal Chat Summaries**:
  - Natural language responses and execution outcomes are delivered directly in the Telegram chat using clean Markdown formatting.
- [ ] **Document Attachment Delivery for Large Payloads**:
  - When output exceeds Telegram's 4096-character limit (e.g., massive `terraform plan` diffs, full `infra.md` reports, or raw Jenkins build logs), Zeus packages the content into a Markdown or text document and attaches it directly to the Telegram message.

---

## 📍 Phase 7: Distribution, Documentation & Ecosystem Packaging

- [ ] **Cross-Platform CLI Binary Distribution**: Standalone compiled binaries for Windows (`.exe`), macOS, Linux.
- [ ] **Unified Slash Command System**: `//provider`, `//mode`, `//vms`, `//mcp`, `//skills`, `//plugin`, `//export`, `//memory`, `//config`.
- [ ] **End-to-End Documentation & Runbooks**: Comprehensive architecture manuals and preconfigured skills.

---

## 📅 Step-by-Step Execution Sequence

1. ✅ **Milestone 1**: Master Architecture (`PLAN.md`) & Project Vision (`README.md`) encompassing Plan/Build modes, Permission Gates, Hermes Persistent Memory, Multi-LLM Providers (`/provider`), MCP, Skills/Plugins, and Telegram Gateway.
2. 🔄 **Milestone 2**: Core Agent Harness, Multi-LLM Provider Engine (`ollama`, `vllm`, `openai`, `anthropic`, `gemini`), Preflight Verifiers (Terraform & Jenkins), and Minimal-Token Pipeline.
3. 🔄 **Milestone 3**: Cloud Discovery Engine (VPCs, Subnets, vSwitches, ECS, Public IPs) & `infra.md` Exporter.
4. 🔄 **Milestone 4**: Visual IaC Module (Draw.io XML parser + Multimodal Vision converter + HCL Generator with Memory cross-checks).
5. 🔄 **Milestone 5**: Unified `//VMs` Multi-Cloud & SSH Management Engine, Multi-Cloud Fleet Jobs with Jenkins.
6. 🔄 **Milestone 6**: Strict Human-in-the-Loop Permission Gates & Blast-Radius Engine.
7. 🔄 **Milestone 7**: Model Context Protocol (MCP) Client, Skills Framework (`SKILL.md`), and Plugin Packaging System.
8. 🔄 **Milestone 8**: Multi-Channel Daemon & Telegram Bot Gateway with Mobile Inline Approvals and Dual-Stream Delivery.
