---
name: qa-explore-tune
description: |
  优化 qa-explore QA 插件。根据实战反馈修改插件文件并推送到 GitHub。
  Use when: "qa-explore tune", "优化 qa-explore", "qa-explore 有问题"
user-callable: true
---

# /qa-explore-tune — 插件自优化

用法：`/qa-explore-tune <问题描述或改进需求>`

## 仓库

- GitHub: https://github.com/QHaoQi/qa-explore
- Clone 路径: .claude/skills/qa-explore/

## Step 1: 定位目标文件

| 关键词 | 文件 |
|--------|------|
| 发现 / 路由 / 检测 / 框架 | `lib/discovery/` |
| 探索 / 测试策略 / happy path / 边界 / 破坏性 | `lib/exploration/` |
| bug 判定 / 严重度 / 分级 | `lib/criteria/bug-severity.md` |
| UX / 体验 / 评分 | `lib/criteria/ux-checklist.md` |
| 报告 / 模板 | `lib/reporting/` |
| issue / GitHub | `lib/reporting/issue-template.md` |
| 流程 / 阶段 / 入口 / 整体 | `SKILL.md` |
| 新框架支持 | 新建 `lib/discovery/{framework}.md` + 更新 `framework-detect.md` |

## Step 2: 读取现有内容

优先读 `.claude/skills/qa-explore/` 下的文件，不存在则 `gh repo clone QHaoQi/qa-explore /tmp/qa-explore` 后读 `/tmp/qa-explore/`。

## Step 3: 修改

约束：
- 单文件 ≤ 200 行，单一职责
- SKILL.md 是唯一入口，`Read lib/xxx.md` 按需加载
- **禁止硬编码**特定项目路径/URL/配置
- 指令具体可执行（✓ "检查 console Error 级别日志" / ✗ "注意质量"）
- 中文描述 + 英文技术术语

## Step 4: 推送

```bash
cd /tmp/qa-explore || gh repo clone QHaoQi/qa-explore /tmp/qa-explore
# 复制修改到 repo
git add -A && git commit -m "tune: <改动摘要>" && git push
```

同步更新本地 `.claude/skills/qa-explore/` 中对应文件。

## Step 5: 输出

表格汇总：文件 | 改动说明
