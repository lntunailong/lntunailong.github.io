# Loong

一个用 **GitHub Pages** 托管的静态站点。推送到 `main` 分支即自动发布。

**线上地址：<https://lntunailong.github.io/>**

---

## 怎么加自己的内容

仓库根目录就是网站的根目录。直接把静态文件放进来：

```
index.html          # 首页，换成你自己的
404.html            # 找不到页面时显示
assets/style.css    # 样式表
图片、其他 .html、子目录……
```

然后：

```bash
git add -A
git commit -m "更新网站内容"
git push
```

推上去之后，GitHub Actions 会自动跑一次部署（通常一两分钟）。在仓库的
**Actions** 标签页可以看到进度，变绿之后刷新网址即可。

> 纯静态站点不需要任何构建步骤 —— 推什么就发布什么，不用在本地装 Node 或跑打包。

## 目录结构

```
.
├── index.html                      # 首页
├── 404.html                        # 404 页面
├── assets/
│   └── style.css                   # 样式（改配色只需动文件顶部的变量）
├── .github/workflows/pages.yml     # 自动部署流水线
├── .nojekyll 不需要                # Actions 发布不经过 Jekyll
└── README.md
```

## 关于部署

发布流程在 [`.github/workflows/pages.yml`](.github/workflows/pages.yml)，
用的是 GitHub 官方动作：

| 步骤 | 动作 |
|---|---|
| 拉取代码 | `actions/checkout@v7` |
| 读取 Pages 配置 | `actions/configure-pages@v6` |
| 打包站点 | `actions/upload-pages-artifact@v5` |
| 部署 | `actions/deploy-pages@v5` |

几点说明：

- **打包范围**：整个仓库根目录。`upload-pages-artifact` 会自动排除 `.git` 和 `.github`。
- **隐藏文件**：以 `.` 开头的文件默认**不会**被发布。如果需要（例如 `.well-known/`），
  在 `pages.yml` 的 upload 步骤下加一行 `include-hidden-files: true`。
- **触发方式**：推送到 `main`，或在 Actions 页面手动 `Run workflow`。
- **同并发保护**：同一时间只跑一个部署，不会互相打断。

## 本地预览

不用装任何东西，起一个静态服务器就行：

```bash
python -m http.server 8000
```

然后打开 <http://localhost:8000>。

> 直接双击 `index.html` 用 `file://` 打开也能看，但样式表和 404 页面这类
> 使用绝对路径的资源可能不正常，建议用上面的本地服务器。

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

---

## 这个仓库是怎么建起来的

- 账号：`cmdpe`，组织：`lntunailong`（你是 Owner）
- 仓库名必须是 `<组织名>.github.io`，GitHub 才会把它当作**组织根站点**，
  网址才会是 `https://lntunailong.github.io/`（根路径，不带 `/仓库名/` 后缀）。
  组织名和用户名共用命名空间，所以这个名字是唯一的、不能随意改前缀。
- Pages 的发布来源设为 **GitHub Actions**（不是「从分支部署」），
  所以仓库里看不见 `gh-pages` 分支，也不需要它。
