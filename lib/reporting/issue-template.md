# GitHub Issue 模板

## 创建条件

- 仅为 P0 和 P1 Bug 创建 Issue
- 创建前检查 memory.json 中的 known_bugs 去重（基于指纹匹配）
- 指纹 = SHA-256(页面路径 + 操作描述 + 错误类型)

## 创建方式

使用 `gh issue create` 命令：

```bash
gh issue create \
  --title "[QA-{severity}] {bug_title}" \
  --label "bug,qa-explore,{severity_label}" \
  --body "$(cat <<EOF
## 环境信息

- **环境**：{environment}
- **发现时间**：{timestamp}
- **页面**：{url}
- **严重度**：{severity}

## 复现步骤

1. 导航到 {url}
2. {step_2}
3. {step_3}

## 预期结果

{expected}

## 实际结果

{actual}

## 证据

### 截图

![bug-screenshot](screenshots/{screenshot_file})

### Console 日志

\`\`\`
{console_errors}
\`\`\`

### Network 请求

\`\`\`
{network_errors}
\`\`\`

## Bug 指纹

\`{fingerprint}\`

---

*由 QA Explore Agent 自动创建*
EOF
)"
```

## Label 映射

| 严重度 | Label |
|--------|-------|
| P0 | `priority:critical` |
| P1 | `priority:major` |

## 去重逻辑

```
new_fingerprint = sha256(page_path + action_desc + error_type)

if new_fingerprint in memory.known_bugs:
    skip（已报告）
else:
    create issue
    memory.known_bugs.append({
        fingerprint: new_fingerprint,
        severity: severity,
        issue_url: created_issue_url,
        date: now
    })
```

## 注意事项

- Issue 标题前缀 `[QA-P0]` 或 `[QA-P1]`，便于筛选
- 如果 `gh` 命令不可用（未登录），将 Issue 内容保存到报告中，提示用户手动创建
- 截图以相对路径引用，确保在仓库内可查看
