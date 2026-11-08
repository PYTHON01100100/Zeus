# ⚡ Zeus: The Agentic Cloud & Infrastructure Harness

> **Autonomous Cloud Orchestration, Diagram-to-IaC Synthesis, Unified Multi-Cloud VM Control, Agentic CI/CD, Multi-LLM Provider Agility, MCP & Skills Plugins, and Telegram Operations.**

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Terraform](https://img.shields.io/badge/Terraform-1.5+-623CE4?logo=terraform)](https://www.terraform.io/)
[![Jenkins](https://img.shields.io/badge/Jenkins-CI%2FCD-D33833?logo=jenkins)](https://www.jenkins.io/)
[![LLM Providers](https://img.shields.io/badge/LLM_Providers-Ollama_%7C_vLLM_%7C_OpenAI_%7C_Anthropic_%7C_Gemini-blue)](#-multi-llm-provider-freedom-provider)
[![MCP](https://img.shields.io/badge/MCP-Model_Context_Protocol_Enabled-brightgreen)](#-model-context-protocol-mcp-integration)
[![Telegram](https://img.shields.io/badge/Telegram_Bot-Interactive_Approval_Ops-2CA5E0?logo=telegram)](#-telegram-bot--multi-channel-remote-operations)

---

## 🌟 Vision & Overview

**Zeus** is an agentic AI harness engineered specifically for Cloud and DevOps engineers. It bridges high-level architectural design, infrastructure as code (IaC), CI/CD pipelines, and active multi-cloud operations into a single conversational and command-driven system.

Zeus is built with **zero vendor lock-in**:
- **Multi-LLM Freedom (`/provider`)**: Seamlessly switch between local private models (**Ollama**, **vLLM**) and frontier cloud models (**Anthropic Claude**, **OpenAI**, **Google Gemini**, OpenRouter, DeepSeek) at runtime.
- **Model Context Protocol (MCP)**: Native MCP client support to dynamically plug in community MCP servers (GitHub, Postgres, Kubernetes, Cloudflare, etc.).
- **Modular Skills & Plugins**: Extend the harness with self-contained `SKILL.md` workflows and redistributable `plugin.yaml` packages.
- **Multi-Channel Control (CLI & Telegram)**: Operate from your local interactive terminal or manage infrastructure and authorize blast-radius actions directly from your phone via **Telegram Bot**.

### Key Inspirations & Pillars:
- [**Pi (`earendil-works/pi`)**](https://github.com/earendil-works/pi): Minimalist agent loop, token-minimal task reading, and dual-stream terminal logs.
- [**Hermes Agent (`NousResearch/hermes-agent`)**](https://github.com/NousResearch/hermes-agent): Persistent cross-session project memory, infrastructure topology constraints (Direct Connect, VPN, CIDR reservations).
- [**OpenCode (`anomalyco/opencode`)**](https://github.com/anomalyco/opencode): Decoupled **Plan** vs **Build** modes and resilient workspace orchestration.
- [**Model Context Protocol (MCP)**](https://modelcontextprotocol.io/): Open standard for dynamic AI tools and resource expansion.
- [**ec2s (`PYTHON01100100/ec2s`)**](https://github.com/PYTHON01100100/ec2s) & [**a1s (`PYTHON01100100/a1s`)**](https://github.com/PYTHON01100100/a1s): High-speed, terminal-native instance navigation (`k9s` style) across AWS and Alibaba Cloud.

---

## 🧭 Dual Operational Modes: `Plan` vs `Build`

Zeus enforces strict separation between read-only inquiry and mutating actions across both the CLI and Telegram:

```
┌────────────────────────────────────────────────────────────────────────┐
│                              ZEUS MODES                                │
├──────────────────────────────────┬─────────────────────────────────────┤
│ 🔍 PLAN MODE (Read-Only / Safe)  │ 🔨 BUILD MODE (Execution / Apply)   │
├──────────────────────────────────┼─────────────────────────────────────┤
│ • Natural language infra queries │ • Terraform apply & provisioning    │
│ • Inspect VPCs, subnets, vSwitches│ • Create, update, or stop resources │
│ • Review ECS & EC2 instances     │ • Execute SSH commands on VMs       │
│ • Consult on-prem & VPN memory   │ • Trigger Jenkins CI/CD builds      │
│ • Generate & inspect Terraform HCL│ • Strict Permission Guardrails      │
│ • Read-only MCP queries & tools  │ • Mutating MCP tools & fleet jobs   │
│ • Export audit reports (infra.md)│ • Mobile human approval to delete   │
│ • ZERO mutation risk             │   (CLI prompt / Telegram button)    │
└──────────────────────────────────┴─────────────────────────────────────┘
```

- **`PLAN` Mode**: The default safe mode. Query running infrastructure, inspect VPC allocations, preview `terraform plan` outputs, and run diagnostic checks without altering any cloud state.
- **`BUILD` Mode**: Active execution mode. Provisions infrastructure, updates resources, and runs remote fleet jobs—**always gated by explicit human authorization** for any destructive actions.

---

## 🚀 Core Capabilities

```
                         ┌──────────────────────────────────────────────┐
                         │                 ZEUS HARNESS                 │
                         │       Dual Modes: [ PLAN ] | [ BUILD ]       │
                         │   Channels: Terminal CLI & Telegram Bot      │
                         └───────┬──────────────┬──────────────┬────────┘
                                 │              │              │
        ┌────────────────────────┴──────┐       │       ┌──────┴────────────────────────┐
        ▼                               ▼       ▼       ▼                               ▼
 ┌──────────────┐              ┌─────────────┐ ┌─────────────────┐             ┌────────────────┐
 │  Terraform   │              │   Jenkins   │ │   Visual IaC    │             │  Unified VMs   │
 │  Provisioner │              │  Automator  │ │ Draw.io & Image │             │  Multi-Cloud   │
 │ (Plan/Apply) │              │  (Pipelines)│ │  to Terraform   │             │  (//VMs & SSH) │
 └──────┬───────┘              └──────┬──────┘ └────────┬────────┘             └───────┬────────┘
        │                             │                 │                              │
        └─────────────────────────────┼─────────────────┴──────────────────────────────┘
                                      │
        ┌─────────────────────────────┴───────────────────────────────┐
        ▼                                                             ▼
 ┌─────────────────────────────────────────┐   ┌────────────────────────────────────────┐
 │        MULTI-LLM ENGINE (/provider)     │   │         EXTENSIBILITY HUB              │
 │ • Ollama (Local / Offline)              │   │ • Model Context Protocol (MCP Clients) │
 │ • vLLM (Self-Hosted GPU Cluster)        │   │ • Progressive Skills (SKILL.md)        │
 │ • Anthropic Claude (3.7 Sonnet / Opus)  │   │ • Modular Plugins (plugin.yaml)        │
 │ • OpenAI (GPT-4o, o1, o3-mini)          │   │ • Telegram Bot Mobile Approvals        │
 │ • Google Gemini (2.5 Pro Multimodal)    │   │ • Hermes Persistent Project Memory     │
 └─────────────────────────────────────────┘   └────────────────────────────────────────┘
```

---

### 1. 🔀 Multi-LLM Provider Freedom (`/provider`)
Zeus eliminates lock-in by providing a dynamic LLM client abstraction. You can hot-swap providers and models at any moment using `/provider` or `//provider`:

```bash
# Interactive selector
//provider

# Direct CLI switch
//provider set ollama qwen2.5-coder:32b
//provider set vllm deepseek-ai/DeepSeek-R1-Distill-Qwen-32B
//provider set anthropic claude-3-7-sonnet
//provider set openai gpt-4o
//provider set gemini gemini-2.5-pro

# List and benchmark providers
//provider list
//provider test
```

| Provider | Ideal Use Case | Endpoint & Configuration |
|---|---|---|
| **Ollama** | 100% offline, zero data egress, on-prem air-gapped security | Local daemon (`http://localhost:11434`), auto-detects pulled models |
| **vLLM** | High-throughput, self-hosted private GPU cluster serving | OpenAI-compatible endpoint (`http://vllm-host:8000/v1`) |
| **Anthropic Claude** | Deep architectural reasoning, complex Terraform refactors | `ANTHROPIC_API_KEY`, Claude 3.7 Sonnet / Claude 3.5 Sonnet |
| **OpenAI** | High-speed function calling, agentic coding | `OPENAI_API_KEY`, GPT-4o, o1, o3-mini |
| **Google Gemini** | Massive context (1M+ tokens), multimodal diagram parsing | `GEMINI_API_KEY`, Gemini 2.5 Pro / Flash |
| **OpenRouter / DeepSeek** | Unified aggregators, DeepSeek R1 / V3 APIs | Configurable `base_url` + `api_key` in `~/.zeus/config.yaml` |

---

### 2. 🔌 Model Context Protocol (MCP) Integration
Zeus acts as a first-class **Model Context Protocol (MCP)** host/client. Easily expand Zeus with any community or corporate MCP server without writing custom adapters:

- **Stdio Transport**: Spawns local CLI tools and daemons (`npx`, `uvx`, native binaries).
- **SSE Transport**: Connects to remote or internal enterprise MCP servers over HTTP/SSE.
- **Mode-Aware Bridging**: Automatically classifies external MCP tools into `Plan` (read-only queries) vs `Build` (mutating actions).

#### Configuration (`.zeus/mcp.json` or `~/.zeus/mcp.json`):
```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": { "GITHUB_PERSONAL_ACCESS_TOKEN": "${GITHUB_TOKEN}" }
    },
    "postgres-infra": {
      "command": "uvx",
      "args": ["mcp-server-postgres", "--connection-string", "${DATABASE_URL}"]
    },
    "internal-k8s": {
      "url": "https://mcp.internal.infra.corp/sse",
      "transport": "sse"
    }
  }
}
```

```bash
# Manage MCP servers
//mcp list           # View connected servers & discovered tools
//mcp reload         # Hot-reload tool schemas
//mcp add <name>     # Register a new MCP server
```

---

### 3. 🧩 Extensible Skills & Plugin Ecosystem
Zeus includes a modular extensibility engine inspired by modern progressive disclosure architectures:

- **Skills (`SKILL.md`)**:
  - Organized in `.zeus/skills/<skill-name>/` or `~/.zeus/skills/`.
  - Self-contained folders containing prompt guidelines, operational triggers, and companion scripts.
  - **Progressive Disclosure**: Only compact metadata is indexed initially (< 20 tokens). Full operational instructions are loaded on-demand only when a user task matches the skill triggers.
- **Plugins (`plugin.yaml`)**:
  - Package bundles of skills, MCP configurations, custom cloud providers, and hooks into shareable packages.
  - Install, enable, disable, and update plugins with single commands:
    ```bash
    //plugin list
    //plugin install github.com/zeus-plugins/aws-security
    //plugin enable aws-security
    ```

---

### 4. 📱 Telegram Bot & Multi-Channel Remote Operations
Manage and audit your infrastructure from anywhere. Zeus runs both as an interactive terminal CLI and as a **persistent Telegram Bot daemon**:

```bash
# Run Zeus as a background Telegram daemon
zeus serve --channel telegram
```

#### Telegram Capabilities:
1. **Full Natural Language Cloud Ops**: Chat with Zeus directly from your smartphone or desktop Telegram client.
2. **Interactive Inline Keyboards**:
   - `/provider`: Tap to switch between Ollama, vLLM, OpenAI, Anthropic, or Gemini.
   - `/mode`: Toggle between `[ 🔍 PLAN ]` and `[ 🔨 BUILD ]` with one tap.
   - `/vms`: Quick compute instance status and management.
3. **Mobile Blast-Radius Approval Gate**:
   - When Zeus detects a destructive action (e.g. `terraform apply` with resource destructions, `terminate_instance`, `delete_ecs_service`), it halts execution and pushes a high-priority card to Telegram:
     ```text
     🚨 DESTRUCTIVE OPERATION DETECTED
     Action: Terminate EC2 Instance
     Target: i-0a1b2c3d4e5f (Production-API-Gateway)
     Blast Radius: 1 Compute Resource
     ⚠️ This action CANNOT be undone.

     [ ✅ Confirm Delete ]        [ ❌ Abort ]
     ```
   - Approve or abort directly from your phone with a single tap.
4. **Dual-Stream Delivery**:
   - Compact, high-signal summaries in chat.
   - Massive outputs (e.g., full `infra.md` reports, detailed `terraform plan` logs) sent as downloadable document attachments.
5. **Strict Security Whitelist**:
   - Configurable `allowed_chat_ids` ensures only authorized team members can interact with the bot.

---

### 5. 🔍 Natural Language Cloud Discovery & `infra.md` Exporter
- **Ask Your Cloud Anything**:
  - *"Show all my VPCs and their CIDR ranges across AWS and Alibaba Cloud."*
  - *"List all subnets and vSwitches with their IP allocations."*
  - *"What ECS services/clusters or EC2 instances are currently running?"*
  - *"Which instances have public IPs exposed to the internet?"*
- **One-Click `infra.md` Export**:
  - Run `//export infra.md` or type `/infra` in Telegram.
  - Generates a comprehensive Markdown audit capturing VPCs, CIDRs, subnets, compute fleet, security groups, and hybrid interconnects.

---

### 6. 🧠 Persistent Project Memory & Network Constraints (Hermes Engine)
- **Avoid Costly Architectural Collisions**:
  - Remembers whether your environment is **On-Premise**, **Hybrid**, or **Multi-Cloud**.
  - Stores existing **Direct Connect (DX)** links, **Site-to-Site VPN** tunnels, Transit Gateways, and reserved CIDRs in `.zeus/memory.yaml`.
- **Pre-Execution Guardrails**:
  - Automatically flags CIDR collisions before generating or applying Terraform code:
    `⚠️ Overlap Warning: Proposed CIDR 192.168.1.0/24 conflicts with on-prem Direct Connect range (192.168.0.0/16) in project memory.`

---

### 7. ⚡ Minimal-Token Pipeline & Smart Error Diagnostics (Pi Philosophy)
- **Dual-Stream Execution**:
  - Streams full live terminal output to the human operator.
  - Compresses execution results into high-signal summaries ($< 250$ tokens) for LLM context.
- **Smart Terraform & Jenkins Error Parsers**:
  - Strips ANSI colors and noisy banners.
  - Pinpoints exact file, line, offending code block, root cause, and suggested remediation.

---

### 8. 🎨 Diagram-to-Terraform (Draw.io & Vision Pipeline)
- **Draw.io (`.drawio`, `.xml`) Native Parser**:
  - Parses mxGraph XML hierarchies, container relationships, and network boundaries.
- **Multimodal Architecture Vision**:
  - Converts PNG/JPEG architecture diagrams into modular, validated Terraform HCL (`main.tf`, `variables.tf`, `outputs.tf`).
- **Interactive Human Review**:
  - Displays generated code, runs `terraform init` and `terraform plan`, and prompts for explicit approval before apply.

---

### 9. 🖥️ Unified Multi-Cloud VM & Compute Manager (`//VMs`)
- **Multi-Cloud Abstraction**:
  - AWS EC2 & ECS, Alibaba Cloud ECS & vSwitches, Generic SSH servers.
- **Fleet Jobs with Jenkins**:
  - Trigger multi-instance operational jobs (e.g. *Install Docker across selected AWS & Alibaba instances*).
  - Review targets in Plan Mode, authorize in Build Mode, and perform targeted retries on failed instances.

---

## 💻 Command Reference

Zeus commands use the `//` prefix in CLI and `/` prefix in Telegram:

| Command | Telegram | Description |
|---|---|---|
| `//provider` | `/provider` | Open interactive provider switcher or set active model (`//provider set ollama qwen2.5-coder`) |
| `//mode [plan\|build]` | `/mode` | Switch between safe read-only (`PLAN`) and mutation (`BUILD`) modes |
| `//vms [list\|ssh\|exec]` | `/vms` | Open unified multi-cloud VM dashboard or execute remote actions |
| `//mcp [list\|reload\|add]` | `/mcp` | Manage Model Context Protocol servers and inspect external tools |
| `//skills [list\|info]` | `/skills` | Browse, inspect, or scaffold progressive disclosure agent skills |
| `//plugin [list\|install]` | `/plugin` | Manage redistributable plugins and bundled capabilities |
| `//export infra.md` | `/infra` | Generate and export complete infrastructure audit documentation |
| `//memory [add]` | `/memory` | Inspect or record persistent infrastructure constraints in `memory.yaml` |
| `//help` | `/help` | Display comprehensive command reference card |

---

## ⚙️ Configuration (`~/.zeus/config.yaml`)

```yaml
# Zeus Configuration
default_provider: "anthropic"

# Multi-LLM Providers
providers:
  ollama:
    base_url: "http://localhost:11434"
    default_model: "qwen2.5-coder:32b"
  vllm:
    base_url: "http://localhost:8000/v1"
    api_key: "EMPTY"
    default_model: "deepseek-ai/DeepSeek-R1-Distill-Qwen-32B"
  anthropic:
    api_key: "${ANTHROPIC_API_KEY}"
    default_model: "claude-3-7-sonnet-20250219"
  openai:
    api_key: "${OPENAI_API_KEY}"
    default_model: "gpt-4o"
  gemini:
    api_key: "${GEMINI_API_KEY}"
    default_model: "gemini-2.5-pro"

# Telegram Bot Gateway
telegram:
  enabled: true
  bot_token: "${ZEUS_TELEGRAM_BOT_TOKEN}"
  allowed_chat_ids:
    - 123456789
  require_admin_approval: true

# Jenkins CI/CD
jenkins:
  url: "${JENKINS_URL}"
  user: "${JENKINS_USER}"
  api_token: "${JENKINS_API_TOKEN}"
```

---

## 🛠️ Prerequisites & Preflight Diagnostic Engine

| Dependency | Purpose | Failure Mode & Remediation |
|---|---|---|
| **Terraform** (`terraform`) | IaC Generation, Plan, Apply | **Error:** `[ZEUS-ERR-101] Terraform binary not detected in PATH.`<br>Displays platform-specific installation command (e.g., `winget install HashiCorp.Terraform` or `brew install terraform`) with direct link to docs. |
| **Jenkins** (`jenkins-cli` / API) | CI/CD Trigger & Status Polling | **Error:** `[ZEUS-ERR-102] Jenkins server unreachable or credentials unset.`<br>Prompts user to configure `JENKINS_URL`, `JENKINS_USER`, and `JENKINS_API_TOKEN`. |
| **Cloud Credentials** | AWS CLI (`~/.aws`), Alicloud (`~/.aliyun`) | Notifies if profile or environment variables (`AWS_ACCESS_KEY_ID`, etc.) are unconfigured before initiating provider calls. |
| **SSH Client** | Interactive VM Shells | Uses system `ssh` or embedded SSH client library (`golang.org/x/crypto/ssh`). |

---

## 📂 Implementation Roadmap

Detailed technical specifications and phase criteria are in [`PLAN.md`](./PLAN.md):

1. **Phase 1: Agentic Core, Multi-LLM Providers & Minimal-Token Pipeline** (`//provider`, Ollama, vLLM, OpenAI, Anthropic, Gemini, Terraform & Jenkins preflight, Plan/Build split, Hermes memory).
2. **Phase 2: Visual IaC Engine** (Draw.io XML parser + PNG vision prompt -> HCL generator -> Review -> `init`/`plan`/`apply`).
3. **Phase 3: Cloud Discovery, Unified Multi-Cloud VM Manager & Fleet Jobs** (`//VMs`, AWS/Alicloud discovery, `infra.md` export, Jenkins fleet jobs).
4. **Phase 4: Strict Permission Guardrails & Blast-Radius Engine** (Destructive action confirmation gates, SSH sessions).
5. **Phase 5: Extensibility Ecosystem** (Model Context Protocol client, dynamic skills framework `SKILL.md`, plugin manager `plugin.yaml`).
6. **Phase 6: Multi-Channel Operations & Telegram Bot Gateway** (Telegram daemon, mobile inline approvals, dual-stream file delivery).
7. **Phase 7: Packaging & Documentation** (Standalone CLI binary distribution, comprehensive guides).

---

## 🤝 Contributing

We welcome contributions! Please review our [**Contributing Guide (CONTRIBUTING.md)**](./CONTRIBUTING.md) for details on code style, safety guardrail policies, token efficiency requirements, and PR workflows.

---

## 💡 Inspirations & Acknowledgments

- [earendil-works/pi](https://github.com/earendil-works/pi) - Lightweight agent harness architecture & token-minimal tool philosophy.
- [NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent) - Persistent project memory and environmental constraint retention.
- [anomalyco/opencode](https://github.com/anomalyco/opencode) - Multi-agent planning, Plan/Build mode separation, & developer experience.
- [Model Context Protocol (MCP)](https://modelcontextprotocol.io/) - Open standard for secure model-to-tool connectivity.
- [PYTHON01100100/ec2s](https://github.com/PYTHON01100100/ec2s) - Terminal UI for Amazon EC2.
- [PYTHON01100100/a1s](https://github.com/PYTHON01100100/a1s) - Terminal UI for Alibaba Cloud ECS.
- [HashiCorp Terraform](https://developer.hashicorp.com/terraform) - Standard for Infrastructure as Code.
- [Jenkins](https://www.jenkins.io/) - Open source automation server.
