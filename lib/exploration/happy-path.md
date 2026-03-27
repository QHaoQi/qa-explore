# Round 1: Happy Path 探索

## 目标

验证核心用户旅程正常工作，建立功能基线。

## 探索策略

### 按优先级遍历

P0 路由 → P1 路由 → P2 路由，每个页面执行：

### 页面级检查清单

#### 1. 页面加载验证
- 导航后等待页面稳定（无 loading 状态残留）
- 检查 console 无 error 级别日志
- 检查 network 无 4xx/5xx 响应（排除预期的 401）
- 验证页面标题 / 主要内容区域已渲染

#### 2. 导航验证
- 点击导航菜单中的每个链接
- 验证 URL 变化正确
- 验证页面切换无白屏 / 闪烁
- 后退按钮行为正常

#### 3. 列表页操作
- 验证数据列表正常显示
- 翻页功能正常（如有）
- 搜索 / 筛选功能正常（如有）
- 空状态显示正确（如适用）

#### 4. 表单操作
- 使用合理数据填写表单（前缀 `[QA-Agent]`）
- 提交表单，验证成功反馈
- 验证数据已正确保存（刷新后仍存在）
- 编辑已创建的数据，验证更新成功

#### 5. 详情页验证
- 从列表进入详情页
- 验证详情数据完整显示
- 验证操作按钮可用（编辑 / 删除等）

## 核心循环

```
observe:
  snapshot = browser_snapshot()
  console = browser_console_messages(level="error")
  network = browser_network_requests()

plan:
  根据页面类型（列表/表单/详情）决定操作序列

action:
  browser_click / browser_type / browser_fill_form / browser_navigate

verify:
  snapshot_after = browser_snapshot()
  console_after = browser_console_messages(level="error")
  比对预期结果，新增 console error 视为 bug

record:
  如发现异常 → 记录为 finding，包含：
  - 页面 URL
  - 操作步骤
  - 预期 vs 实际
  - console 日志
  - 截图（browser_take_screenshot）
```

## 测试数据规范

- 文本字段：`[QA-Agent] Test {field_name} {timestamp}`
- 邮箱：`qa-agent-{timestamp}@test.local`
- 电话：`13800138000`
- 数字：合理范围内的正整数

## 超时处理

- 单页面探索上限：3 分钟
- 超时后记录为 P1 finding（性能问题）并继续下一页
