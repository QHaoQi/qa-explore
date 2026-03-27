---
name: qa-explore
description: 自主探索 Web 应用、发现 Bug、生成报告的 QA 智能体
allowed-tools:
  - Bash
  - Read
  - Write
  - Edit
  - Glob
  - Grep
  - WebFetch
  - mcp__plugin_playwright_playwright__browser_navigate
  - mcp__plugin_playwright_playwright__browser_snapshot
  - mcp__plugin_playwright_playwright__browser_click
  - mcp__plugin_playwright_playwright__browser_type
  - mcp__plugin_playwright_playwright__browser_take_screenshot
  - mcp__plugin_playwright_playwright__browser_console_messages
  - mcp__plugin_playwright_playwright__browser_network_requests
  - mcp__plugin_playwright_playwright__browser_fill_form
  - mcp__plugin_playwright_playwright__browser_select_option
  - mcp__plugin_playwright_playwright__browser_hover
  - mcp__plugin_playwright_playwright__browser_press_key
  - mcp__plugin_playwright_playwright__browser_wait_for
  - mcp__plugin_playwright_playwright__browser_close
  - mcp__plugin_playwright_playwright__browser_tabs
  - mcp__plugin_playwright_playwright__browser_evaluate
  - mcp__plugin_playwright_playwright__browser_resize
---

# QA Explore — Web 应用自主探索测试

## 调用方式

- `/qa-explore` — 探索本地开发环境（默认）
- `/qa-explore staging` — 探索 staging 环境
- `/qa-explore production` — 探索生产环境（只读模式）
- `/qa-explore --quick` — 快速模式，仅执行 Round 1 Happy Path

参数解析：第一个非 flag 参数为环境名，`--quick` 可与环境组合使用。

## 执行流程

按 5 个阶段顺序执行：INIT → DISCOVER → EXPLORE → JUDGE → REPORT。

---

### Phase 1: INIT — 环境初始化

1. 解析调用参数，确定目标环境和模式（完整 / quick）
2. 读取项目根目录 `CLAUDE.md`，提取：
   - 端口号（后端 / 前端）
   - 启动命令（`scripts/*.sh`）
   - 认证信息（登录方式、测试账号）
   - 已声明的路由 / 页面列表
   - 环境地址映射表
3. 确定 base URL：优先从 CLAUDE.md 环境表读取，未找到则默认 `http://localhost:3000`
4. 加载跨会话记忆 `reports/qa-explore/memory.json`（如存在）
5. **安全规则**：
   - `production` 环境：只读模式，禁止表单提交、数据修改、删除操作
   - 所有环境：测试数据统一前缀 `[QA-Agent]`，便于清理
   - Issue 去重：基于 memory.json 中已报告的 bug 指纹跳过重复

---

### Phase 2: DISCOVER — 路由发现

1. `Read lib/discovery/framework-detect.md` — 检测项目框架（Next.js / Vite / CRA 等）
2. 框架匹配成功：
   - `Read lib/discovery/{framework}.md` — 按框架约定扫描源码提取路由
3. 框架未匹配：
   - `Read lib/discovery/url-crawl.md` — 从 base URL 出发，浏览器爬取链接
4. 合并路由：CLAUDE.md 声明路由 ∪ 自动发现路由 → 去重得到完整路由表
5. 路由优先级分类：
   - **P0**：核心业务流程（列表、详情、创建、编辑）
   - **P1**：辅助功能（搜索、筛选、导出、设置）
   - **P2**：管理后台、配置页面

---

### Phase 3: EXPLORE — 三轮探索

每个页面的探索核心循环：`observe → plan → action → verify → record`

- **observe**：snapshot + console messages + network requests 三合一采集
- **plan**：基于当前页面状态决定下一步操作
- **action**：执行 click / type / navigate 等交互
- **verify**：操作后再次 observe，对比预期结果
- **record**：记录异常（console error、非 2xx 请求、UI 异常、功能缺陷）

#### Round 1: Happy Path（所有模式执行）
`Read lib/exploration/happy-path.md`
- 按 P0 → P1 → P2 优先级遍历路由
- 执行核心用户旅程：导航、查看、创建、编辑、删除
- 验证页面加载、数据展示、基本交互正常

#### Round 2: Boundary（完整模式）
`Read lib/exploration/boundary.md`
- 对所有表单执行边界输入：空值、超长文本、特殊字符、SQL/XSS payload
- 验证错误提示、输入校验、防御性处理

#### Round 3: Destructive（完整模式）
`Read lib/exploration/destructive.md`
- 权限绕过：未登录访问受保护路由、越权操作
- 并发冲突：快速重复提交、竞态条件
- 陈旧状态：后退按钮、过期表单、缓存一致性

> `--quick` 模式仅执行 Round 1，跳过 Round 2 和 Round 3。

---

### Phase 4: JUDGE — 缺陷定级

1. `Read lib/criteria/bug-severity.md` — 按标准对每个发现分级：
   - **P0 Critical**：功能不可用、数据丢失、安全漏洞、页面崩溃
   - **P1 Major**：功能部分失效、严重 UX 问题、性能明显劣化
   - **P2 Minor**：样式瑕疵、文案错误、非关键功能异常
2. `Read lib/criteria/ux-checklist.md` — 5 维度 UX 评分：
   - 可用性 / 一致性 / 响应速度 / 无障碍 / 错误处理

---

### Phase 5: REPORT — 输出报告

1. `Read lib/reporting/report-template.md` — 生成 Markdown 报告
   - 输出路径：`reports/qa-explore/{date}-{env}.md`
   - 内容：摘要 → 路由覆盖表 → Bug 列表（按严重度排序）→ UX 评分 → 截图引用
2. `Read lib/reporting/issue-template.md` — P0/P1 Bug 自动创建 GitHub Issue
   - 包含：复现步骤、截图、console 日志、network 请求
   - 去重：基于 bug 指纹（页面 + 操作 + 错误类型）比对 memory.json
3. 更新 `reports/qa-explore/memory.json`：
   - 已探索页面及覆盖状态
   - 已发现 bug 指纹（用于下次去重）
   - 本次运行统计（页面数、bug 数、耗时）

---

## 记忆机制

`reports/qa-explore/memory.json` 结构：

```json
{
  "last_run": "2026-03-27T10:00:00Z",
  "explored_pages": { "/path": { "last_explored": "...", "rounds_completed": 3 } },
  "known_bugs": [{ "fingerprint": "sha256...", "severity": "P0", "issue_url": "..." }],
  "run_history": [{ "date": "...", "env": "...", "pages": 12, "bugs_found": 3 }]
}
```

跨会话行为：优先探索上次未覆盖的页面，已报告 bug 不重复提 Issue。

## 约束

- 单次探索不超过 30 分钟，超时进入 JUDGE → REPORT
- 截图保存至 `reports/qa-explore/screenshots/`
- 所有测试数据使用 `[QA-Agent]` 前缀，探索结束后提示清理
- 生产环境严格只读，违反则立即中止
