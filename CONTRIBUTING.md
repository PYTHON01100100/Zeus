# 🤝 Contributing to Zeus

Thank you for your interest in contributing to **Zeus: The Agentic Cloud & Infrastructure Harness**!

Zeus bridges high-level architectural designs, Infrastructure as Code (Terraform), CI/CD pipelines (Jenkins), and real-time multi-cloud fleet management into a single, safety-first agentic harness.

We welcome contributions of all kinds: new cloud provider adapters, parser improvements for architecture diagrams, token-optimization filters, bug fixes, and documentation updates.

---

## 📜 Table of Contents

1. [Guiding Architectural Principles](#-guiding-architectural-principles)
2. [Development Environment Setup](#-development-environment-setup)
3. [Project Directory Structure](#-project-directory-structure)
4. [Contribution Workflow](#-contribution-workflow)
5. [Coding & Safety Standards](#-coding--safety-standards)
6. [Testing & Quality Assurance](#-testing--quality-assurance)
7. [Commit Message Conventions](#-commit-message-conventions)

---

## 🏛️ Guiding Architectural Principles

When writing code for Zeus, keep these 4 core tenets in mind:

1. **Safety First & Zero Silent Deletions**:
   - Every mutating or destructive action (destroying Terraform resources, terminating EC2/ECS/VM instances, deleting subnets) **must** route through the Permission Gate (`pkg/guardrails`).
   - The harness must never allow the LLM to bypass human confirmation for destructive actions.
2. **Token Efficiency (The Pi Philosophy)**:
   - Tool outputs intended for LLM consumption must be concise, high-signal, and strictly formatted ($< 250$ tokens per tool response).
   - Never dump raw multi-megabyte log files or full `.tfstate` files into agent context.
   - Dual-stream rule: Stream rich terminal logs to the human operator; feed clean, summarized context to the agent.
3. **Respect Project Memory (The Hermes Philosophy)**:
   - Any new infrastructure generation or plan module must cross-check `.zeus/memory.yaml`.
   - Never generate code with CIDR collisions against on-premise ranges, unrouteable Direct Connect links, or broken VPN paths.
4. **Actionable Error Diagnostics**:
   - Raw errors from external tools (Terraform, Jenkins, Cloud SDKs) must be parsed into pinpointed diagnostic cards (File, Line, Offending Snippet, Root Cause, Suggested Remediation).

---

## 🛠️ Development Environment Setup

### Prerequisites

Ensure you have the following installed on your machine:
- **Go** (version 1.22+ recommended) or **Node.js** (version 20+ LTS).
- **Terraform CLI** ($\ge 1.5.0$) in your `$PATH`.
- **Git**.
- Optional: Local Docker engine and access to AWS / Alibaba Cloud sandboxes for provider integration testing.

### Initial Setup

```bash
# 1. Clone your fork of the repository
git clone https://github.com/<your-username>/Zeus.git
cd Zeus

# 2. Check prerequisites locally
terraform version
git status

# 3. Create your local development branch
git checkout -b feat/your-feature-name
```

---

## 📂 Project Directory Structure

```text
Zeus/
├── cmd/
│   └── zeus/                  # CLI and entrypoint binary
├── pkg/
│   ├── harness/               # Core ReAct loop, mode controller (Plan vs Build)
│   ├── memory/                # Hermes-style persistent memory (.zeus/memory.yaml)
│   ├── guardrails/            # Permission gates & blast-radius calculators
│   ├── tools/
│   │   ├── terraform/         # Terraform tool suite & smart error parser
│   │   ├── jenkins/           # Jenkins API client & log windowing engine
│   │   ├── discovery/         # Cloud network discovery (VPCs, Subnets, vSwitches)
│   │   └── vms/               # Unified VM registry (//VMs) & SSH runner
│   ├── providers/
│   │   ├── aws/               # AWS EC2 & ECS adapter
│   │   ├── alicloud/          # Alibaba Cloud ECS & vSwitch adapter
│   │   └── generic_ssh/       # Generic SSH server adapter
│   ├── visual_iac/
│   │   ├── drawio/            # mxGraph XML AST parser
│   │   └── vision/            # Multimodal diagram vision synthesizer
│   └── ui/
│       ├── tui/               # Bubbletea interactive table dashboard
│       └── terminal/          # Lipgloss formatting & diagnostic cards
├── tests/                     # Unit and integration test suites
├── README.md                  # Project overview & documentation
├── PLAN.md                    # Segmented implementation master plan
└── CONTRIBUTING.md            # Contributor guidelines
```

---

## 🔄 Contribution Workflow

1. **Find or Open an Issue**:
   - Check existing issues or open a new one to discuss significant architectural changes before coding.
2. **Create a Feature Branch**:
   ```bash
   git checkout -b feat/add-gcp-vm-adapter
   ```
3. **Develop & Format**:
   - Write clean, well-commented code following idiomatic patterns.
   - Run code formatters:
     ```bash
     # If Go:
     gofmt -s -w .
     go vet ./...
     ```
4. **Write Tests**:
   - Add unit tests verifying new functionality and edge cases.
   - Always mock network calls and cloud SDKs in unit tests.
5. **Submit a Pull Request (PR)**:
   - Provide a clear PR title and description outlining what was changed, which issue is addressed, and manual testing steps performed.

---

## 🔒 Coding & Safety Standards

### Adding a New Tool to the Harness
When adding a tool to `pkg/tools`:
- **Define Strict JSON Schemas**: Every tool parameter must be strictly typed.
- **Assign Mode Classification**:
  - `ModePlan`: Read-only queries (e.g. `list_subnets`, `get_vm_status`).
  - `ModeBuild`: Mutating actions (e.g. `apply_terraform`, `reboot_instance`).
- **Enforce Permission Gate on Deletion**:
  ```go
  if tool.IsDestructive() {
      if !guardrails.RequestUserConfirmation(ctx, blastRadius) {
          return nil, errors.New("operation canceled by user")
      }
  }
  ```

### Handling External CLI Outputs
- Always strip ANSI color codes before passing stdout/stderr to LLM context filters.
- Truncate long arrays and log outputs to a sensible window (maximum 25-30 lines).

---

## 🧪 Testing & Quality Assurance

All pull requests must pass automated checks:
- **Unit Tests**:
  ```bash
  go test -race -v ./...
  ```
- **Terraform Integration Tests**:
  - Use test fixtures under `tests/fixtures/terraform/` to test HCL generation without mutating real cloud resources.
- **Diagnostics Parsing Tests**:
  - Ensure Terraform and Jenkins error parsers correctly extract file, line, and remediation from sample failure outputs.

---

## 📝 Commit Message Conventions

We follow the [Conventional Commits](https://www.conventionalcommits.org/) standard:

- `feat:` A new feature (e.g., `feat(vms): add Alibaba Cloud ECS start and stop actions`)
- `fix:` A bug fix (e.g., `fix(terraform): resolve nil pointer in diagnostic error parser`)
- `docs:` Documentation improvements (e.g., `docs: update PLAN.md with Hermes memory engine`)
- `refactor:` Code restructuring without changing behavior
- `test:` Adding or refactoring tests
- `chore:` Build scripts, dependency updates, or tool configurations

Example:
```text
feat(memory): add CIDR collision checker against on-prem Direct Connect ranges

Cross-references newly generated VPC subnets with .zeus/memory.yaml to
prevent IP overlap with on-premises data centers.
```

---

## 💬 Community & Help

If you have questions, feedback, or need help getting started:
- Open a discussion or issue on GitHub.
- Reach out to the maintainers directly through project repositories.

Thank you for making **Zeus** faster, safer, and smarter for cloud engineers everywhere! ⚡
