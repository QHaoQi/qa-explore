# 报告模板

## 输出路径

`reports/qa-explore/{YYYY-MM-DD}-{env}.md`

## 模板结构

```markdown
# QA 探索测试报告

> 环境：{environment} | 日期：{date} | 模式：{mode} | 耗时：{duration}

## 摘要

- 探索页面数：{page_count}
- 发现缺陷数：P0={p0_count} / P1={p1_count} / P2={p2_count}
- UX 综合评分：{ux_score}/10
- 测试轮次：{rounds_completed}/3

## 路由覆盖

| 路由 | 优先级 | 状态 | 发现 |
|------|--------|------|------|
| / | P0 | ✅ 通过 | - |
| /users | P0 | ⚠️ P1 | 分页第二页空白 |
| /login | P0 | ❌ P0 | 空密码可提交 |

## 缺陷列表

### P0 — Critical

#### [{bug_id}] {bug_title}

- **页面**：{url}
- **严重度**：P0
- **复现步骤**：
  1. 导航到 {url}
  2. {step_2}
  3. {step_3}
- **预期结果**：{expected}
- **实际结果**：{actual}
- **截图**：![{screenshot_name}](screenshots/{screenshot_file})
- **Console 日志**：
  ```
  {console_errors}
  ```
- **Network 异常**：
  ```
  {network_errors}
  ```

### P1 — Major

(同上格式)

### P2 — Minor

(同上格式)

## UX 评分

| 维度 | 分数 | 说明 |
|------|------|------|
| 可用性 | {score}/10 | {comment} |
| 一致性 | {score}/10 | {comment} |
| 响应速度 | {score}/10 | {comment} |
| 无障碍 | {score}/10 | {comment} |
| 错误处理 | {score}/10 | {comment} |
| **综合** | **{avg}/10** | |

## 建议

### 优先修复
1. {p0_fix_suggestion}
2. {p1_fix_suggestion}

### 体验优化
1. {ux_improvement}

---

*报告由 QA Explore Agent 自动生成*
```

## 截图规范

- 路径：`reports/qa-explore/screenshots/`
- 命名：`{date}-{page_slug}-{issue_id}.png`
- 每个 P0/P1 bug 必须附带截图
- P2 bug 按需截图
