# Hugo 博客开发指南

本仓库是一个使用 Stack 主题的 Hugo 站点，包含自定义的 About 页面布局与样式。

## 快速开始

1. 启动开发服务器：

   ```sh
   hugo server --disableFastRender
   ```

2. 浏览器打开站点（Hugo 会打印访问地址）。
3. 内容在 `content/`，样式在 `assets/scss/`。

## 项目结构

- `content/` - 页面与文章（带前置数据的 Markdown）。
- `config/` - 站点配置（`_default` 为主配置集合）。
- `layouts/` - 本地模板覆盖（优先级高于主题模板）。
- `assets/` - SCSS/JS 管道资源。
- `static/` - 原样复制到站点的静态文件。
- `themes/hugo-theme-stack/` - 主题源码（不要直接改，改动放 `layouts/`）。

## 当前自定义点

- About 页面模板：`layouts/page/about.html`
- About 页面数据：`content/page/about/index*.md`
- 自定义 CSS：`assets/scss/custom.scss`
- 头部注入（字体 + CSS）：`layouts/partials/head/custom.html`

## Markdown / HTML / CSS 的流程（Hugo 是怎么做的）

### 1) Markdown -> HTML

- 你写的内容在 `content/`，每个文件都包含 Front Matter（YAML/TOML）+ 正文 Markdown。
- Hugo 会把 Markdown 正文交给渲染器（默认 Goldmark）转换成 HTML 片段。
- 这些 HTML 片段会被放进页面模板中的 `.Content` 位置。

### 2) 模板渲染 -> 完整 HTML 页面

- 模板来自 `layouts/`（本地覆盖）或主题的 `themes/hugo-theme-stack/layouts/`。
- Hugo 根据页面类型选择模板，例如：
   - 单页：`single.html`
   - 列表页：`list.html`
   - 首页：`home.html`
- 模板内可以引用 partial（例如 `partials/head/head.html`），并组合出完整的 HTML 文档。

### 3) SCSS/CSS 管道

- 你写的样式在 `assets/scss/`。
- 模板里通过 Hugo Pipes 把 SCSS 编译成 CSS，并输出到 `public/`。
- 生成的 CSS 会在 head 里被 `<link>` 引用到页面。

### 4) 静态文件复制

- `static/` 下的文件会原样拷贝到 `public/`，可直接通过 URL 访问。

### 5) 构建输出

- 最终的 HTML/CSS/JS 都输出到 `public/`。
- 你在浏览器看到的就是 `public/` 里的文件。

### 一句话总结

Markdown 负责内容，模板负责结构，SCSS/CSS 负责样式，Hugo 把它们在构建时拼装成最终的静态站点。

## 从零开始的完整开发流程（写给初学者）

> 目标：你只需要在 Markdown 里填数据，HTML 页面就会自动更新。

### 1) 配置决定全局规则

- `config/` 定义站点语言、菜单、分页、主题参数。
- 这一步相当于“项目的总开关”，模板与内容都会读取这里。

### 2) 模板决定页面结构

- `layouts/` 是页面结构的“骨架”。
- 例如 About 页的模板是 `layouts/page/about.html`。
- 模板里会写好结构，并从页面数据读取字段。

### 3) Markdown 只负责填充数据

- `content/page/about/index*.md` 里写 `hero/projects/profile/site/timeline` 等字段。
- 模板通过 `.Params.hero`、`.Params.projects` 读取这些字段并渲染成卡片。
- 所以你改了 Markdown，HTML 结构不变，显示内容会自动更新。

### 4) 样式只负责“长什么样”

- `assets/scss/custom.scss` 定义颜色、字体、间距、阴影等。
- 模板结构稳定后，样式能独立迭代，不影响内容数据。

### 5) Hugo 构建时自动拼装

- Hugo 读取配置 -> 选模板 -> 渲染 Markdown -> 编译 SCSS -> 输出 `public/`。
- 你看到的页面就是这些输出文件的结果。

## 完成 About 页后的优化步骤

### A) 首页优化（先保证“第一眼”好看）

- 调整 `config/_default/params.toml` 的首页组件顺序。
- 给首页文章加封面图与摘要，提升“内容密度”。

### B) 文章页优化（提升可读性）

- 统一标题层级与段落间距。
- 为代码块和引用块设置清晰样式。
- 增加“上一篇/下一篇”和“相关推荐”。

### C) 分类与标签（让内容可探索）

- 统一标签命名，避免同义词重复。
- 用分类控制内容分区（例如：Tech / Notes / Projects）。

### D) 视觉一致性（形成个人品牌）

- 定义主色、强调色、背景色并贯穿全站。
- 统一按钮、卡片、标题的大小与阴影。

### E) 性能与发布

- 图片尽量用 WebP，控制尺寸。
- 发布前运行 `hugo`，确保 `public/` 构建正常。

## 分步优化路线

### 1) 让站点稳定构建

- 确保 `hugo server --disableFastRender` 无报错。
- 保持 `defaultContentLanguage` 与 `languages` 配置一致。

### 2) 完成 About 页面

- 更新 `content/page/about/index*.md` 的 hero/projects/profile/site/timeline。
- 如需头像与社交链接，补充对应字段。
- 结构改在 `layouts/page/about.html`，视觉改在 `assets/scss/custom.scss`。

### 3) 产出核心内容

- 新建文章：`content/post/your-post/index.md`。
- 文章封面图放在同目录，并在 front matter 引用。
- 加上 `tags`/`categories` 方便导航与聚合。

### 4) 调整导航与分类

- 修改 `config/_default/menu.toml` 或在页面 front matter 里配置菜单。
- 调整 `config/_default/params.toml` 的首页/侧边栏小组件。

### 5) 加强品牌与视觉

- 替换 `static/img/` 或 `assets/img/` 下的头像与 favicon。
- 更新 `config/_default/params.toml` 中的站点标题、描述、页脚。
- 在 `assets/scss/custom.scss` 里完善色彩和字体。

### 6) SEO 与分享

- 为页面设置 `description`、`keywords`、`images`。
- 在 `config/_default/params.toml` 中补齐社交与 OG 配置。

### 7) 性能与细节

- 压缩图片，优先 WebP。
- 避免超大首屏图，使用合适尺寸。
- 用 Lighthouse 检查并优化 CSS/JS。

### 8) 部署

- 执行 `hugo` 生成 `public/`。
- 将 `public/` 部署到 GitHub Pages / Netlify / Cloudflare Pages。

## 本地约定

- 覆盖主题模板用 `layouts/`，不要直接改 `themes/`。
- 自定义样式集中到 `assets/scss/custom.scss`（由 Hugo Pipes 编译）。

## 常见问题

- Front matter 必须以 `---` 开头并以 `---` 结束。
- 报错优先检查 `layouts/` 覆盖逻辑。
- 主题模块缓存位于 `~/.hugo_cache`，需要覆盖时请用 `layouts/`。

## 项目学习地图（快速上手）

这是一个 Hugo + Stack 主题的多语言静态博客项目，核心思路是“内容数据驱动页面”。

### 1. 技术栈与入口

- 站点配置入口：`config/_default/hugo.toml`
- 主题来源：`github.com/CaiJimmy/hugo-theme-stack/v3`（见 `go.mod`）
- 构建输出目录：`public/`

### 2. 关键目录职责

- `content/`：文章与页面内容（Markdown + front matter）
- `config/_default/`：全局配置（语言、菜单、参数、渲染）
- `layouts/`：本地模板覆写（优先级高于主题）
- `assets/scss/`：自定义样式源（Hugo Pipes 编译）
- `themes/hugo-theme-stack/`：主题原始实现（尽量不直接改）

### 3. 当前仓库已做定制

- About 页面模板：`layouts/page/about.html`
- About 页面数据：`content/page/about/index*.md`
- 自定义样式：`assets/scss/custom.scss`
- 头部注入（字体 + CSS）：`layouts/partials/head/custom.html`
- 分页逻辑覆写：`layouts/partials/helper/paginator.html`

### 4. 多语言结构

- 语言配置：`config/_default/languages.toml`（`zh-cn/en/zh-hant-tw/ja`）
- 首页语言入口：`content/_index.zh-cn.md`、`content/_index.md`
- 页面内容示例：`content/page/search/index.zh-cn.md`、`content/page/links/index.zh-cn.md`
- 文章内容示例：`content/post/Markdown Syntax/index.md`

### 5. 推荐学习顺序

1. 先读 `config/_default/hugo.toml`、`config/_default/params.toml`、`config/_default/languages.toml`
2. 对照阅读 `content/page/about/index.zh-cn.md` 与 `layouts/page/about.html`
3. 阅读 `assets/scss/custom.scss`，理解样式接入
4. 阅读 `content/post/...` front matter（`tags/categories/image/slug`）

### 6. 常用命令

- 开发预览：`hugo server --disableFastRender`
- 生产构建：`hugo`
