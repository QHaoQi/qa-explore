# qa-explore

> Autonomous QA exploration testing agent for Claude Code

**qa-explore** turns Claude Code into a QA engineer. It automatically discovers every page and route in your web project, tests them through 3 progressive rounds, captures screenshots with reproduction steps, and generates structured bug reports — all driven by a single command.

No runtime code. No dependencies. Just a markdown skill that Claude Code reads and executes via Playwright MCP.

## How It Works

1. **Discovery** — Reads your `CLAUDE.md`, detects your framework, and builds a route map
2. **Round 1: Happy Path** — Tests every page with normal user flows
3. **Round 2: Boundary** — Pushes inputs to limits (empty fields, long strings, special characters)
4. **Round 3: Destructive** — Rapid actions, back/forward spam, concurrent submissions
5. **Reporting** — Generates a structured report with screenshots, steps to reproduce, and severity ratings
6. **Issues** — Creates GitHub Issues for P0/P1 bugs automatically

## Supported Frameworks (v0.1)

| Framework | Discovery Method |
|-----------|-----------------|
| Next.js (App Router) | Scans `app/` directory for `page.tsx` files |
| Next.js (Pages Router) | Scans `pages/` directory |
| FastAPI | Parses `@app.route` / `APIRouter` decorators |
| Any web app | URL crawling fallback |

## Prerequisites

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) with **Playwright MCP plugin** enabled
- A running web application (local, staging, or production)

## Installation

```bash
cd your-project
git clone https://github.com/QHaoQi/qa-explore .claude/skills/qa-explore
```

## Usage

| Command | Description |
|---------|-------------|
| `/qa-explore` | Full 3-round exploration (local) |
| `/qa-explore staging` | Test staging environment |
| `/qa-explore prod` | Production (read-only mode) |
| `/qa-explore --quick` | Quick P0 happy path only |

### What It Discovers

1. Reads your project's `CLAUDE.md` for declared routes, ports, and auth info
2. Detects framework (Next.js, FastAPI) and scans source code for routes
3. Falls back to URL crawling if framework is unknown

### Output

```
reports/qa-explore/
├── report-2026-03-27.md       # Structured QA report
├── screenshots/               # Captured evidence
│   ├── p0-form-crash.png
│   └── p1-404-on-delete.png
└── ...
```

Add to your `.gitignore`:

```
reports/qa-explore/
```

## Project Structure

```
qa-explore/
├── SKILL.md              # Entry point (Claude Code reads this)
├── lib/
│   ├── discovery/        # Route discovery (per-framework)
│   ├── exploration/      # Test strategies (3 rounds)
│   ├── criteria/         # Bug severity + UX scoring
│   └── reporting/        # Report + issue templates
├── examples/
│   └── sample-report.md  # Example QA report
├── README.md
└── LICENSE
```

## Extending Framework Support

Add a new `.md` file to `lib/discovery/` describing the route extraction logic for your framework, then register it in `lib/discovery/framework-detect.md`.

## Complementary to `/qa`

| | `/qa` | `/qa-explore` |
|---|---|---|
| Focus | Known flows, regression | Unknown unknowns, exploration |
| Fixes bugs | Yes (edits source code) | No (report only) |
| Best for | Pre-merge CI gate | Post-deploy or periodic audit |

Use both: `/qa` catches regressions before merge, `/qa-explore` finds what you forgot to test.

## License

MIT — see [LICENSE](LICENSE).

---

## 中文说明

**qa-explore** 是一个 Claude Code QA 探索测试插件。它能自动发现你 Web 项目中的所有页面和路由，像真人 QA 一样用浏览器进行三轮递进测试（正常路径 → 边界值 → 破坏性操作），自动截图并生成结构化测试报告。

**核心特点：**
- 纯 Markdown 技能文件，零依赖，零运行时代码
- 自动识别 Next.js / FastAPI 等框架的路由
- 三轮递进探索，覆盖 happy path、边界和破坏性场景
- 自动生成报告 + 截图 + GitHub Issue

**安装：**
```bash
cd your-project
git clone https://github.com/QHaoQi/qa-explore .claude/skills/qa-explore
```

**使用：** 在 Claude Code 中输入 `/qa-explore` 即可启动完整探索测试。
