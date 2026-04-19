# Agency Orchestrator

**English** | [中文](./README.md)

> **You're already paying for AI. Why do you need another API key just to run a workflow?**

[![CI](https://github.com/jnMetaCode/agency-orchestrator/actions/workflows/ci.yml/badge.svg)](https://github.com/jnMetaCode/agency-orchestrator/actions)
[![npm version](https://img.shields.io/npm/v/agency-orchestrator)](https://www.npmjs.com/package/agency-orchestrator)
[![License: Apache-2.0](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](./LICENSE)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](./CONTRIBUTING.md)

**186 expert AI roles · Zero-code YAML orchestration · Use your existing subscription · No API key required**

```
Claude Max ✓  ChatGPT Plus ✓  GitHub Copilot ✓  Google Account (free) ✓  Ollama ✓
```

> If you find this useful, please **Star** it — helps others discover the project.

---

## Why Agency Orchestrator

**Sound familiar?**

- You want multiple AI agents to collaborate, but CrewAI/LangGraph require Python and defining every role from scratch
- You set up the framework, then discover you **still need to get API keys, add credits, and manage quotas**
- You're already paying $20/month for Claude Max / ChatGPT Plus / Copilot — but you can't use that subscription

**Agency Orchestrator's answer:**

Write one YAML file → 186 expert roles collaborate automatically → runs on your **existing subscription**.

> **Industry first: Every multi-agent framework out there (CrewAI, LangGraph, AutoGen, Dify, n8n, Agency Swarm, MetaGPT, Google ADK, OpenAI Agents SDK) requires API keys. Agency Orchestrator is the only one that lets you bring your subscription.**

### 30-Second Comparison

```python
# CrewAI: ~50 lines of Python, define every role, API key required
researcher = Agent(role="PM", goal="...", backstory="...(write it yourself)...")
task = Task(description="...", agent=researcher)
crew = Crew(agents=[researcher], tasks=[task])
crew.kickoff()
```

```yaml
# Agency Orchestrator: 10 lines of YAML, 186 roles ready to go, no API key
steps:
  - id: analyze
    role: "product/product-manager"   # expert prompt already written
    task: "Analyze this PRD: {{prd_content}}"
```

| | CrewAI | LangGraph | AutoGen | **Agency Orchestrator** |
|---|--------|-----------|---------|---------------------|
| Language | Python | Python | Python | **YAML (zero code)** |
| Roles | Write your own | Write your own | Write your own | **186 ready-to-use** |
| API Key | Required | Required | Required | **6 providers need none** |
| Dependencies | pip + LiteLLM + dozens | pip + LangChain | pip + AutoGen | **npm + 2 deps** |
| Parallelism | Manager mode | Manual graph | Manual | **Auto DAG detection** |
| Branching | None | Manual | Manual | **Condition expressions** |
| Loops | None | Manual | Manual | **Declarative loop/exit** |
| Resume | None | Checkpointers | None | **Built-in `--resume`** |
| Price | Open-source + $25-99/mo | Open-source | Open-source | **Completely free** |

## Get Started in 3 Steps

### Step 1: Try it in 5 seconds

```bash
npx agency-orchestrator demo
```

4 AI roles collaborate on a story. No install, no config, no API key.

### Step 2: Run a real workflow with your subscription

```bash
npm install agency-orchestrator
npx ao init                    # download 186 AI roles

# Use your existing Claude Max subscription (no API key!)
npx ao run workflows/story-creation.yaml --input premise="A time travel story"
```

Just set `provider: "claude-code"` in your YAML. No Claude? Use `gemini-cli` (free with Google account), `copilot-cli`, or `codex-cli`.

### Step 3: Use it inside your AI coding tool

```bash
./scripts/install.sh           # one-command install for your AI tools
```

Then tell Cursor / Claude Code / Copilot:

```
Run workflows/story-creation.yaml with premise="A time travel story"
```

It auto-parses YAML → loads roles → executes in DAG order → saves results.

**Works with 14 AI coding tools** ([integration guides](./integrations/)): Claude Code · GitHub Copilot · Cursor · Windsurf · Kiro · Trae · Aider · Gemini CLI · Codex CLI · OpenCode · Qwen Code · DeerFlow 2.0 · Antigravity · OpenClaw

## Demo: 4 AI Roles Write a Complete Story in 2 Minutes

```
$ ao run workflows/story-creation.yaml -i "premise=A programmer discovers AI replies with things it shouldn't know"

  Workflow: Short Story Creation
  Steps: 4 | Concurrency: 2 | Model: deepseek-chat
──────────────────────────────────────────────────

  ── [1/4] story_structure (Narratologist) ──
  Done | 14.9s | 1,919 tokens

  ── [2/4] character_design (Psychologist) ──            ← parallel
  Done | 65.5s | 4,016 tokens

  ── [3/4] conflict_design (Narrative Designer) ──       ← parallel
  Done | 65.5s | 3,607 tokens

  ── [4/4] write_story (Content Creator) ──
  Done | 33.9s | 5,330 tokens

==================================================
  Completed: 4/4 steps | 114.3s | 14,872 tokens
==================================================
```

Steps 2 and 3 run **in parallel** (auto-detected from DAG dependencies). Four specialized AI roles collaborate to produce a complete suspense short story.

## How It Works

```yaml
name: "Product Requirements Review"
agents_dir: "agency-agents-zh"

llm:
  provider: "deepseek"       # or: claude, openai, ollama
  model: "deepseek-chat"

concurrency: 2

inputs:
  - name: prd_content
    required: true

steps:
  - id: analyze
    role: "product/product-manager"
    task: "Analyze this PRD and extract core requirements:\n\n{{prd_content}}"
    output: requirements

  - id: tech_review
    role: "engineering/engineering-software-architect"
    task: "Evaluate technical feasibility:\n\n{{requirements}}"
    output: tech_report
    depends_on: [analyze]

  - id: design_review
    role: "design/design-ux-researcher"
    task: "Evaluate UX risks:\n\n{{requirements}}"
    output: design_report
    depends_on: [analyze]

  - id: summary
    role: "product/product-manager"
    task: "Synthesize feedback:\n\n{{tech_report}}\n\n{{design_report}}"
    depends_on: [tech_review, design_review]
```

The engine automatically:
1. Parses YAML → builds a **DAG** (directed acyclic graph)
2. Detects parallelism — `tech_review` and `design_review` run concurrently
3. Passes outputs between steps via `{{variables}}`
4. Loads role definitions from [agency-agents-zh](https://github.com/jnMetaCode/agency-agents-zh) as system prompts
5. Retries on failure (exponential backoff)
6. Saves all outputs to `ao-output/`

```
analyze ──→ tech_review  ──→ summary
         └→ design_review ──┘
          (parallel)
```

## Features

### AI Workflow Composer

Describe your workflow in one sentence — AI selects the right roles, designs the DAG, and generates a ready-to-run YAML:

```bash
ao compose "PR code review covering security and performance"
```

The AI will:
1. Select matching roles from 186 available (e.g., Code Reviewer, Security Engineer, Performance Benchmarker)
2. Design the DAG (3-way parallel → summary)
3. Generate complete YAML with variable passing and task descriptions
4. Save to `workflows/` — ready to `ao run`

Supports `--provider` and `--model` flags (default: DeepSeek).

### Condition Branching

```yaml
- id: tech_path
  role: "engineering/engineering-sre"
  task: "Technical evaluation: {{requirements}}"
  depends_on: [classify]
  condition: "{{job_type}} contains technical"

- id: biz_path
  role: "marketing/marketing-social-media-strategist"
  task: "Business evaluation: {{requirements}}"
  depends_on: [classify]
  condition: "{{job_type}} contains business"

- id: summary
  depends_on: [tech_path, biz_path]
  depends_on_mode: "any_completed"  # proceeds when ANY upstream completes
```

Supported operators: `contains`, `equals`, `not_contains`, `not_equals`.

### Loop Iteration

```yaml
- id: write_draft
  role: "marketing/marketing-content-creator"
  task: "Write article: {{topic}}"
  output: draft

- id: brand_review
  role: "design/design-brand-guardian"
  task: "Review brand compliance: {{draft}}"
  output: review_result
  depends_on: [write_draft]
  loop:
    back_to: write_draft
    max_iterations: 3
    exit_condition: "{{review_result}} contains approved"
```

When the exit condition is not met, execution loops back to `back_to`. The `{{_loop_iteration}}` variable tracks the current round.

### Resume & Iterate

**Problem**: After `ao run` completes, all step outputs are lost. To tweak the final story, you'd have to re-run everything from scratch.

**Solution**: `--resume` reloads previous outputs. `--from` specifies where to restart.

```bash
# Round 1: Normal run
ao run workflows/story-creation.yaml -i premise="A time travel story"

# Round 2: Characters feel flat — re-run from character_design
ao run workflows/story-creation.yaml --resume last --from character_design

# Round 3: Only rewrite the final prose
ao run workflows/story-creation.yaml --resume last --from write_story

# Round 4: Go back to a specific version
ao run workflows/story-creation.yaml --resume ao-output/<dir>/ --from write_story
```

Each round creates a new timestamped output directory. All versions are preserved.

| Scenario | Command |
|----------|---------|
| First run | `ao run workflow.yaml -i key=value` |
| Re-run from a step | `ao run workflow.yaml --resume last --from <step-id>` |
| Re-run only failed steps | `ao run workflow.yaml --resume last` |
| Resume specific version | `ao run workflow.yaml --resume ao-output/<dir>/ --from <step-id>` |

## 9 LLM Providers — 6 Need No API Key

**Already paying for one of these? You're ready to go:**

| You have... | Provider config | Install CLI | Cost to you |
|------------|----------------|-------------|-------------|
| Claude Max/Pro ($20/mo) | `provider: "claude-code"` | `npm i -g @anthropic-ai/claude-code` | **$0 extra** |
| Google Account | `provider: "gemini-cli"` | `npm i -g @google/gemini-cli` | **Free** (1000 req/day, Gemini 2.5 Pro) |
| GitHub Copilot ($10/mo) | `provider: "copilot-cli"` | `npm i -g @github/copilot` | **$0 extra** |
| ChatGPT Plus/Pro ($20/mo) | `provider: "codex-cli"` | `npm i -g @openai/codex` | **$0 extra** |
| OpenClaw account | `provider: "openclaw-cli"` | `npm i -g openclaw` | **$0 extra** |
| A computer | `provider: "ollama"` | [ollama.ai](https://ollama.ai) | **Free** (local models) |

**Or use traditional API keys:**

| Provider | Config | Env Variable |
|----------|--------|-------------|
| DeepSeek | `provider: "deepseek"` | `DEEPSEEK_API_KEY` |
| Claude API | `provider: "claude"` | `ANTHROPIC_API_KEY` |
| OpenAI | `provider: "openai"` | `OPENAI_API_KEY` |

All API providers support custom `base_url` and `api_key`, compatible with any OpenAI-compatible API.

## CLI Reference

```bash
ao demo                              # Zero-config multi-agent demo
ao init                              # Download 186 AI roles
ao init --workflow                    # Interactive workflow creator
ao compose "description"             # AI-powered workflow generation
ao run <workflow.yaml> [options]      # Execute workflow
ao validate <workflow.yaml>           # Validate without running
ao plan <workflow.yaml>               # Show execution plan (DAG)
ao explain <workflow.yaml>            # Explain execution plan in natural language
ao roles                             # List all available roles
ao serve                             # Start MCP Server (for Claude Code / Cursor)
```

| Option | Description |
|--------|-------------|
| `--input key=value` | Pass input variables |
| `--input key=@file` | Read variable value from file |
| `--output dir` | Output directory (default `ao-output/`) |
| `--resume <dir\|last>` | Resume from previous run |
| `--from <step-id>` | With `--resume`, restart from a specific step |
| `--watch` | Real-time terminal progress display |
| `--quiet` | Quiet mode |

## MCP Server Mode

AI coding tools (Claude Code, Cursor, etc.) can invoke workflow operations directly via the MCP protocol:

```bash
ao serve              # Start MCP stdio server
ao serve --verbose    # With debug logging
```

Claude Code (`settings.json`):

```json
{
  "mcpServers": {
    "agency-orchestrator": {
      "command": "npx",
      "args": ["agency-orchestrator", "serve"]
    }
  }
}
```

Cursor (`.cursor/mcp.json`):

```json
{
  "mcpServers": {
    "agency-orchestrator": {
      "command": "npx",
      "args": ["agency-orchestrator", "serve"]
    }
  }
}
```

6 tools available: `run_workflow`, `validate_workflow`, `list_workflows`, `plan_workflow`, `compose_workflow`, `list_roles`.

## YAML Schema

### Workflow

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | string | Yes | Workflow name |
| `agents_dir` | string | Yes | Path to role definitions directory |
| `llm.provider` | string | Yes | `claude-code` / `gemini-cli` / `copilot-cli` / `codex-cli` / `openclaw-cli` / `ollama` / `claude` / `deepseek` / `openai` |
| `llm.model` | string | Yes | Model name |
| `llm.max_tokens` | number | No | Default 4096 |
| `llm.timeout` | number | No | Step timeout in ms (default 120000) |
| `llm.retry` | number | No | Retry count (default 3) |
| `concurrency` | number | No | Max parallel steps (default 2) |
| `inputs` | array | No | Input variable definitions |
| `steps` | array | Yes | Workflow steps |

### Step

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | string | Yes | Unique step identifier |
| `role` | string | Yes | Role path (e.g. `"engineering/engineering-sre"`) |
| `task` | string | Yes | Task description, supports `{{variables}}` |
| `output` | string | No | Output variable name |
| `depends_on` | string[] | No | Dependent step IDs |
| `depends_on_mode` | string | No | `"all"` (default) or `"any_completed"` |
| `condition` | string | No | Condition expression; step skipped if not met |
| `type` | string | No | `"approval"` for human approval gate |
| `prompt` | string | No | Prompt text for approval nodes |
| `loop` | object | No | Loop config |
| `loop.back_to` | string | No | Step ID to loop back to |
| `loop.max_iterations` | number | No | Max loop rounds (1-10) |
| `loop.exit_condition` | string | No | Exit condition expression |

## Programmatic API

```typescript
import { run } from 'agency-orchestrator';

const result = await run('workflow.yaml', {
  prd_content: 'Your PRD here...',
});

console.log(result.success);     // true/false
console.log(result.totalTokens); // { input: 1234, output: 5678 }
```

## Integrations

Works with **14 AI coding tools** — install with one command:

```bash
./scripts/install.sh                       # auto-detect installed tools
./scripts/install.sh --tool copilot        # or specify one
```

| Tool | Config Location | Install Command | Docs |
|------|----------------|----------------|------|
| **Claude Code** | Skill mode | `npx superpowers-zh` | [Guide](./integrations/claude-code/) |
| **GitHub Copilot** | `.github/copilot-instructions.md` | `--tool copilot` | [Guide](./integrations/copilot/) |
| **Cursor** | `.cursor/rules/` | `--tool cursor` | [Guide](./integrations/cursor/) |
| **Windsurf** | `.windsurfrules` | `--tool windsurf` | [Guide](./integrations/windsurf/) |
| **Kiro** | `.kiro/steering/` | `--tool kiro` | [Guide](./integrations/kiro/) |
| **Trae** | `.trae/rules/` | `--tool trae` | [Guide](./integrations/trae/) |
| **Aider** | `CONVENTIONS.md` | `--tool aider` | [Guide](./integrations/aider/) |
| **Gemini CLI** | `GEMINI.md` | `--tool gemini-cli` | [Guide](./integrations/gemini-cli/) |
| **Codex CLI** | `.codex/instructions.md` | `--tool codex` | [Guide](./integrations/codex/) |
| **OpenCode** | `.opencode/instructions.md` | `--tool opencode` | [Guide](./integrations/opencode/) |
| **Qwen Code** | `.qwen/rules/` | `--tool qwen` | [Guide](./integrations/qwen/) |
| **DeerFlow 2.0** | `skills/custom/` | `--tool deerflow` | [Guide](./integrations/deerflow/) |
| **Antigravity** | `AGENTS.md` | `--tool antigravity` | [Guide](./integrations/antigravity/) |
| **OpenClaw** | Skill mode | `--tool openclaw` | [Guide](./integrations/openclaw/) |

## Workflow Templates (30)

### Dev Workflows (7)

| Template | Roles | Description |
|----------|-------|-------------|
| `dev/tech-design-review.yaml` | Architect, Backend Architect, Security Engineer, Code Reviewer | **Tech design review** (design → parallel review → verdict) |
| `dev/pr-review.yaml` | Code Reviewer, Security Engineer, Performance Benchmarker | PR review (3-way parallel → summary) |
| `dev/tech-debt-audit.yaml` | Architect, Code Reviewer, Test Analyst, Sprint Prioritizer | Tech debt audit (parallel → prioritize) |
| `dev/api-doc-gen.yaml` | Tech Writer, API Tester | API doc generation (analyze → validate → finalize) |
| `dev/readme-i18n.yaml` | Content Creator, Tech Writer | README internationalization |
| `dev/security-audit.yaml` | Security Engineer, Threat Detection Engineer | Security audit (parallel → report) |
| `dev/release-checklist.yaml` | SRE, Performance Benchmarker, Security Engineer, PM | Release Go/No-Go decision |

### Marketing Workflows (3)

| Template | Roles | Description |
|----------|-------|-------------|
| `marketing/competitor-analysis.yaml` | Trend Researcher, Analyst, SEO Specialist, Executive Summary | **Competitor analysis** (research → parallel analysis → summary) |
| `marketing/xiaohongshu-content.yaml` | Xiaohongshu Expert, Creator, Visual Storyteller, Operator | **Xiaohongshu content** (topic → parallel creation → optimize) |
| `marketing/seo-content-matrix.yaml` | SEO Specialist, Strategist, Content Creator | **SEO content matrix** (keywords → strategy → batch generate → review) |

### Data / Design / Ops Workflows (7)

| Template | Roles | Description |
|----------|-------|-------------|
| `data/data-pipeline-review.yaml` | Data Engineer, DB Optimizer, Data Analyst | Data pipeline review |
| `data/dashboard-design.yaml` | Data Analyst, UX Researcher, UI Designer | Dashboard design |
| `design/requirement-to-plan.yaml` | PM, Architect, Project Manager | Requirements → tech design → task breakdown |
| `design/ux-review.yaml` | UX Researcher, Accessibility Auditor, UX Architect | UX review |
| `ops/incident-postmortem.yaml` | Incident Commander, SRE, PM | Incident postmortem |
| `ops/sre-health-check.yaml` | SRE, Performance Benchmarker, Infra Ops | SRE health check (3-way parallel) |
| `ops/weekly-report.yaml` | Meeting Assistant, Content Creator, Executive Summary | **Weekly/monthly report** (organize → highlights → finalize) |

### Strategy / Legal / HR Workflows (3)

| Template | Roles | Description |
|----------|-------|-------------|
| `strategy/business-plan.yaml` | Trend Researcher, Financial Forecaster, PM, Executive Summary | **Business plan** (market → parallel analysis → integrate) |
| `legal/contract-review.yaml` | Contract Reviewer, Legal Compliance | **Contract review** (clause analysis → compliance → opinion) |
| `hr/interview-questions.yaml` | Recruiter, Psychologist, Backend Architect | **Interview questions** (dimensions → parallel design → scorecard) |

### General Workflows (10)

| Template | Roles | Description |
|----------|-------|-------------|
| `product-review.yaml` | PM, Architect, UX Researcher | Product requirements review |
| `content-pipeline.yaml` | Strategist, Creator, Growth Hacker | Content creation pipeline |
| `story-creation.yaml` | Narratologist, Psychologist, Narrative Designer, Creator | Collaborative fiction (4 roles) |
| `ai-opinion-article.yaml` | Trend Researcher, Narrative Designer, Psychologist, Creator | AI opinion long-form article |
| `department-collab/code-review.yaml` | Code Reviewer, Security Engineer | Code review (review loop) |
| `department-collab/hiring-pipeline.yaml` | HR, Tech Interviewer, Biz Interviewer | Hiring pipeline |
| `department-collab/content-publish.yaml` | Content Creator, Brand Guardian | Content publishing (review loop) |
| `department-collab/incident-response.yaml` | SRE, Security Engineer, Backend Architect | Incident response |
| `department-collab/marketing-campaign.yaml` | Strategist, Creator, Approver | Marketing campaign (human approval) |
| `department-collab/ceo-org-delegation.yaml` | CEO, Engineering/Marketing/Product/HR Leads | **CEO org delegation** (decide → parallel depts → summary) |

## Output Structure

Each run saves to `ao-output/<name>-<timestamp>/`:

```
ao-output/product-review-2026-03-22/
├── summary.md          # Final step output
├── steps/
│   ├── 1-analyze.md
│   ├── 2-tech_review.md
│   ├── 3-design_review.md
│   └── 4-summary.md
└── metadata.json       # Timing, token usage, step states
```

## Ecosystem

```
Your AI subscription ──→ agency-orchestrator ──→ 186 expert roles collaborate ──→ quality output
                              │
             ┌────────────────┼────────────────┐
             ▼                ▼                ▼
      14 AI Coding Tools    CLI Mode        MCP Server
      (Cursor/Claude Code   (automation/    (Claude Code/
       /Copilot/...)        CI/CD)          Cursor direct)
```

| Project | Description |
|---------|-------------|
| [agency-agents-zh](https://github.com/jnMetaCode/agency-agents-zh) | 186 AI role definitions — the role library for this engine |
| [superpowers-zh](https://github.com/jnMetaCode/superpowers-zh) | AI coding superpowers — 20 skills for Claude Code / Cursor |
| [shellward](https://github.com/jnMetaCode/shellward) | AI agent security middleware — prompt injection detection, DLP, command safety |

## Roadmap

- [x] **v0.1** — YAML workflows, DAG engine, 4 LLM connectors, CLI, streaming output
- [x] **v0.2** — Condition branching, loop iteration, human approval, Resume, 5 department-collab templates
- [x] **v0.3** — 9 AI tool integrations, 20+ workflow templates, `ao explain`, `ao init --workflow`, `--watch` mode
- [x] **v0.4** — MCP Server mode (`ao serve`), 14 AI tool integrations, one-command installer, 30 workflow templates, **9 LLM providers (6 need no API key: Claude Code / Gemini / Copilot / Codex / OpenClaw / Ollama)**
- [ ] **v0.5** — Web UI, visual DAG editor, English role support, workflow marketplace

## Contributing

See [CONTRIBUTING.md](./CONTRIBUTING.md). PRs welcome!

## License

[Apache-2.0](./LICENSE)
