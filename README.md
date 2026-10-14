# Loong

一个用 **GitHub Pages** 托管的静态站点。推送到 `main` 分支即自动发布。

**线上地址：<https://lntunailong.github.io/>**

---

## 怎么加自己的内容

仓库根目录就是网站的根目录。直接把静态文件放进来：

```
index.html          # 首页，换成你自己的
404.html            # 找不到页面时显示（GitHub Pages 会自动使用它）
assets/style.css    # 样式表
图片、其他 .html、子目录……
.nojekyll           # 别删，见下面「为什么它必须在」
```

然后：

```bash
git add -A
git commit -m "更新网站内容"
git push
```

推上去之后 GitHub 会自动重新构建并发布，通常**几十秒到一两分钟**。
进度可以在仓库的 **Actions** 标签页看 `pages build and deployment` 这条运行记录，
变绿之后刷新网址即可。

> 纯静态站点不需要任何构建步骤 —— 推什么就发布什么，不用在本地装 Node 或跑打包。

## 这个站点是怎么部署的

用的是 GitHub Pages 的**「从分支部署」(Deploy from a branch)** 模式，不是 GitHub Actions：

| 项目 | 值 |
|---|---|
| 发布来源 | `main` 分支，路径 `/` |
| 构建方式 | 由 GitHub 自动完成，仓库里没有工作流文件 |
| 产物分支 | 无（不产生 `gh-pages` 分支） |
| HTTPS | 已强制启用 |

**这样最省事**：仓库里没有 `.yml` 要维护，也不用消耗 Actions 额度，
推送到 `main` 就自动发布。

### 为什么 `.nojekyll` 必须在

「从分支部署」默认会用 **Jekyll** 处理你的文件。这会带来几个意外：

- 以 `_` 开头的目录和文件（如 `_assets/`、`_next/`）会被**直接忽略**，不发布
- `.md` 文件会被渲染成 HTML（`README.md` 会变成 `README.html`）
- 文件内容里出现 `{{ }}` 之类的模板语法可能被 Jekyll 吃掉

仓库根目录放一个空的 `.nojekyll` 就能关掉 Jekyll，让文件**原样发布**。
所以：**不要删掉它**。

## 目录结构

```
.
├── index.html        # 首页
├── 404.html          # 404 页面
├── assets/
│   └── style.css     # 样式（改配色只需动文件顶部的变量）
├── .nojekyll         # 关闭 Jekyll，让文件原样发布（必须保留）
├── .gitignore
└── README.md
```

## 本地预览

开一个静态服务器：

```bash
python -m http.server 8000
```

然后打开 <http://localhost:8000>。

> 直接双击 `index.html` 用 `file://` 打开也能看，但 `404.html` 里用了绝对路径
> `/assets/style.css`，那种方式下样式会加载不到 —— 建议用上面的本地服务器。

## 换配色

改 `assets/style.css` 顶部 `:root` 里的变量即可：

```css
:root {
  --bg:       #0b0f16;   /* 页面背景 */
  --text:     #e7ecf5;   /* 正文颜色 */
  --accent:   #4ade80;   /* 主强调色 */
  --accent-2: #38bdf8;   /* 次强调色 */
  --accent-3: #a78bfa;   /* 渐变第三色 */
}
```

## 常见问题

**改了内容但网址上没变？**
等 `Actions` 里那条构建变绿，然后强制刷新（`Ctrl` + `F5`）。
浏览器和 GitHub 的 CDN 都会缓存，偶尔要等一两分钟。

**想让某个以 `.` 开头的文件也能访问（比如 `.well-known/`）？**
「从分支部署」模式下这类文件不会被发布，Jekyll 关了也一样。
这种情况需要改用 GitHub Actions 方式部署。

**以后想加构建步骤（比如用静态站点生成器）？**
到仓库 **Settings → Pages → Build and deployment → Source** 改成 **GitHub Actions**，
再按官方模板添加工作流即可。

---

## 这个仓库是怎么建起来的

- GitHub 账号：`cmdpe`，组织：`lntunailong`（你是 Owner）
- 仓库名必须是 `<组织名>.github.io`，GitHub 才会把它认作**组织根站点**，
  网址才会是 `https://lntunailong.github.io/`（根路径，不带 `/仓库名/` 后缀）。
  组织名和用户名**共用同一个命名空间**，所以这个前缀是唯一的、不能随便挑 ——
  你自己的 `cmdpe` 前缀已经被现有的个人站 `cmdpe.github.io` 占用了，
  新建组织正是为了拿到一个新的根前缀。
- Pages 的发布来源设为**从分支部署**（GitHub 为该仓库自动开启），
  所以仓库里没有工作流文件，也不会产生 `gh-pages` 分支。
