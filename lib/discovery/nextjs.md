# Next.js 路由发现

## App Router（Next.js 13+）

### 扫描规则

路由定义在 `app/` 或 `src/app/` 目录下，每个 `page.tsx` / `page.jsx` 对应一个路由。

```bash
# 发现所有路由文件
Glob: "**/app/**/page.{tsx,jsx,ts,js}"

# 发现动态路由
# [id] → 动态段，需要实际 ID 才能访问
# [...slug] → 全捕获段
# (group) → 路由组，不影响 URL
```

### 路径转换规则

| 文件路径 | URL 路径 |
|---------|---------|
| `app/page.tsx` | `/` |
| `app/about/page.tsx` | `/about` |
| `app/blog/[id]/page.tsx` | `/blog/:id` |
| `app/(auth)/login/page.tsx` | `/login` |
| `app/api/users/route.ts` | `/api/users`（API，非页面） |

### 布局和模板

- `layout.tsx` — 共享布局，标识导航结构
- `loading.tsx` — 加载状态
- `error.tsx` — 错误边界
- `not-found.tsx` — 404 页面

### 中间件

检查 `middleware.ts`，可能包含：
- 路由重定向规则
- 认证保护的路由列表
- 国际化路由前缀

## Pages Router（旧版）

```bash
Glob: "**/pages/**/*.{tsx,jsx,ts,js}"
# 排除 _app, _document, _error, api/
```

| 文件路径 | URL 路径 |
|---------|---------|
| `pages/index.tsx` | `/` |
| `pages/users/[id].tsx` | `/users/:id` |

## 动态路由处理

对于 `[id]` 类动态路由：
1. 先访问列表页获取实际 ID
2. 用获取的 ID 构造动态路由 URL
3. 如无列表页，尝试 `/1`、`/test` 等常见值

## 优先级分类

扫描完成后，按路径模式分类：

- **P0**：`/`、`/login`、含 CRUD 语义的路由（list/create/edit/detail）
- **P1**：`/settings`、`/search`、`/export`、`/profile`
- **P2**：`/admin`、`/debug`、`/api-docs`
