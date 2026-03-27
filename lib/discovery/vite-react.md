# Vite + React 路由发现

## 路由检测策略

Vite + React 项目的路由通常定义在代码中（非文件系统约定），需解析源码。

### 1. React Router 检测

```bash
# 查找路由定义文件
Grep: "createBrowserRouter|Routes|Route" --glob "*.{tsx,jsx,ts,js}"

# 常见路由定义位置
Glob: "**/router.{tsx,jsx,ts,js}"
Glob: "**/routes.{tsx,jsx,ts,js}"
Glob: "**/App.{tsx,jsx,ts,js}"
```

### 路由模式提取

从 React Router 代码中提取 `path` 属性：

```tsx
// 声明式路由
<Route path="/users" element={<Users />} />
<Route path="/users/:id" element={<UserDetail />} />

// 配置式路由
{ path: "/users", element: <Users /> }
{ path: "/users/:id", element: <UserDetail /> }
```

提取规则：
- 匹配 `path="..."` 或 `path: "..."` 模式
- `:param` 标记为动态段
- 嵌套路由拼接父路径

### 2. TanStack Router 检测

```bash
Grep: "createFileRoute|createRootRoute" --glob "*.{tsx,jsx,ts,js}"
Glob: "**/routes/**/*.{tsx,jsx,ts,js}"
```

### 3. 无路由库

部分简单项目可能无路由库：
- 检查是否只有单页面
- 检查是否使用 hash 路由（`window.location.hash`）
- 回退到 `url-crawl.md` 浏览器爬取

## 入口文件定位

```bash
# Vite 入口
Read: index.html  # 找 <script src="...">
Read: src/main.tsx
Read: src/App.tsx
```

## 优先级分类

同 Next.js 规则：按路径语义分 P0/P1/P2。
