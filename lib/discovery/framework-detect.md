# 框架检测指南

## 检测策略

按优先级依次检测，命中即停：

### 1. 配置文件检测（最可靠）

| 文件 | 框架 | 模块文件 |
|------|------|---------|
| `next.config.*` | Next.js | `nextjs.md` |
| `nuxt.config.*` | Nuxt | `url-crawl.md` |
| `vite.config.*` + 无 `next.config` | Vite (React/Vue/Svelte) | `vite-react.md` |
| `angular.json` | Angular | `url-crawl.md` |
| `svelte.config.*` | SvelteKit | `url-crawl.md` |
| `remix.config.*` 或 `app/root.tsx` | Remix | `url-crawl.md` |

### 2. package.json 依赖检测（备选）

读取 `package.json` 的 `dependencies` + `devDependencies`：

```
next → Next.js
nuxt → Nuxt
@angular/core → Angular
svelte → SvelteKit
remix → Remix
vite + react → Vite React
```

### 3. 目录结构推断（最后手段）

| 目录模式 | 推断 |
|---------|------|
| `app/` + `page.tsx` | Next.js App Router |
| `pages/` + `_app.tsx` | Next.js Pages Router |
| `src/app/` + `app.module.ts` | Angular |
| `src/routes/` + `+page.svelte` | SvelteKit |

### 4. 无法识别

使用通用 `url-crawl.md` 方案，从 base URL 爬取发现路由。

## 执行方式

```bash
# 用 Glob 工具检测配置文件
Glob: "next.config.*"
Glob: "vite.config.*"
Glob: "angular.json"
Glob: "nuxt.config.*"

# 读取 package.json 提取依赖
Read: package.json
```

检测结果决定加载哪个框架特定的路由发现模块。
