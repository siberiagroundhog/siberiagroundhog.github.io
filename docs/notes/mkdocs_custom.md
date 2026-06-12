# MkDocs 网站自定义说明

本文用于记录当前 MkDocs + Material for MkDocs 网站中各个部分的对应关系，以及后续如何进行自定义修改。

当前网站采用：

- GitHub Pages 托管
- MkDocs 生成静态网站
- Material for MkDocs 作为主题
- `main` 分支保存源码
- `gh-pages` 分支保存生成后的网站成品

---

## 1. 先理解 MkDocs 网站的核心结构

一个 MkDocs 网站通常由两部分组成：

```text
my-homepage/
├─ mkdocs.yml
└─ docs/
```

其中：

| 位置 | 作用 |
|---|---|
| `mkdocs.yml` | 网站总配置文件，控制网站名称、主题、导航、颜色、功能等 |
| `docs/` | 网站正文内容目录，里面的 Markdown 文件会被 MkDocs 生成网页 |

可以这样理解：

```text
想改网站整体配置 → 改 mkdocs.yml
想改页面正文内容 → 改 docs/ 里的 .md 文件
```

---

## 2. 当前推荐项目结构

建议保持如下结构：

```text
my-homepage/
├─ mkdocs.yml
├─ docs/
│  ├─ index.md
│  ├─ about.md
│  ├─ projects/
│  │  ├─ index.md
│  │  └─ github-pages.md
│  ├─ notes/
│  │  ├─ index.md
│  │  ├─ python.md
│  │  ├─ git.md
│  │  └─ mkdocs_publish_workflow.md
│  ├─ tools/
│  │  └─ index.md
│  └─ assets/
│     └─ images/
├─ README.md
└─ old-site/
```

各部分含义：

| 文件或目录 | 作用 |
|---|---|
| `mkdocs.yml` | 网站总配置 |
| `docs/index.md` | 首页内容 |
| `docs/about.md` | 关于我页面 |
| `docs/projects/` | 项目页面 |
| `docs/notes/` | 学习笔记页面 |
| `docs/tools/` | 工具页面 |
| `docs/assets/images/` | 图片资源 |
| `README.md` | GitHub 仓库说明 |
| `old-site/` | 旧版手写 HTML 网站备份 |

---

## 3. `mkdocs.yml` 的整体作用

`mkdocs.yml` 是 MkDocs 网站最重要的配置文件。

它通常控制：

- 网站名称
- 网站描述
- 作者信息
- 网站地址
- 主题
- 语言
- 主题颜色
- 深色/浅色模式
- 顶部导航栏
- 左侧目录
- 搜索功能
- 代码复制按钮
- Markdown 扩展功能
- 自定义 CSS / JavaScript

常见结构如下：

```yaml
site_name: 飞雨的个人主页
site_description: 个人学习记录、项目展示与技术笔记
site_author: 厉飞雨
site_url: https://siberiagroundhog.github.io/

theme:
  name: material
  language: zh
  features:
    - navigation.tabs
    - navigation.sections
    - navigation.top
    - search.highlight
    - search.suggest
    - content.code.copy

  palette:
    - scheme: default
      primary: indigo
      accent: blue
      toggle:
        icon: material/weather-night
        name: 切换到深色模式
    - scheme: slate
      primary: indigo
      accent: blue
      toggle:
        icon: material/weather-sunny
        name: 切换到浅色模式

nav:
  - 首页: index.md
  - 关于我: about.md
  - 项目:
      - 项目总览: projects/index.md
      - GitHub Pages 建站: projects/github-pages.md
  - 学习笔记:
      - 笔记总览: notes/index.md
      - Python: notes/python.md
      - Git: notes/git.md
      - MkDocs 维护与发布流程: notes/mkdocs_publish_workflow.md

markdown_extensions:
  - admonition
  - toc:
      permalink: true
  - tables
  - fenced_code
  - attr_list
```

---

## 4. 网站名称如何修改

网站左上角显示的标题通常来自：

```yaml
site_name: 飞雨的个人主页
```

如果想改成：

```text
厉飞雨的学习与项目记录
```

则修改为：

```yaml
site_name: 厉飞雨的学习与项目记录
```

如果想使用 GitHub 用户名风格：

```yaml
site_name: siberiagroundhog 的个人网站
```

这一项会影响网站顶部标题，也可能影响浏览器标签页显示。

---

## 5. 网站描述如何修改

网站描述来自：

```yaml
site_description: 个人学习记录、项目展示与技术笔记
```

它一般不会作为大段正文直接显示，但会影响网页元信息，也可能影响搜索引擎摘要。

可以改成：

```yaml
site_description: 记录 Python、数据分析、机器学习与个人网站搭建过程
```

或者：

```yaml
site_description: Python、公共卫生数据分析、机器学习与技术笔记整理
```

---

## 6. 作者信息如何修改

作者信息来自：

```yaml
site_author: 厉飞雨
```

可以改成：

```yaml
site_author: siberiagroundhog
```

或者保留真实中文名：

```yaml
site_author: 厉飞雨
```

这部分主要是网站元信息，通常不会作为页面正文直接显示。

---

## 7. 网站地址如何修改

网站地址来自：

```yaml
site_url: https://siberiagroundhog.github.io/
```

如果当前网站地址就是：

```text
https://siberiagroundhog.github.io/
```

则不要修改。

只有在以后绑定自定义域名时才需要修改，例如：

```yaml
site_url: https://example.com/
```

---

## 8. 主题名称如何设置

当前使用 Material for MkDocs 主题：

```yaml
theme:
  name: material
```

这表示网站采用 Material 主题。

如果改成：

```yaml
theme:
  name: readthedocs
```

则会切换为 MkDocs 内置的 Read the Docs 风格。

当前建议继续使用：

```yaml
theme:
  name: material
```

因为它功能完整、界面美观、适合个人笔记和项目文档网站。

---

## 9. 网站语言如何设置

当前网站语言设置为中文：

```yaml
theme:
  language: zh
```

这会影响搜索框、按钮、提示语等主题文本。

如果使用中文网站，建议保持：

```yaml
language: zh
```

---

## 10. 顶部导航栏如何修改

网站顶部或侧边栏中的导航来自 `mkdocs.yml` 的 `nav` 部分。

例如：

```yaml
nav:
  - 首页: index.md
  - 关于我: about.md
  - 项目:
      - 项目总览: projects/index.md
      - GitHub Pages 建站: projects/github-pages.md
  - 学习笔记:
      - 笔记总览: notes/index.md
      - Python: notes/python.md
      - Git: notes/git.md
```

规则是：

```text
左边：网站上显示的名字
右边：对应的 Markdown 文件路径
```

例如：

```yaml
- Python: notes/python.md
```

表示：

```text
网站上显示：Python
实际文件：docs/notes/python.md
```

注意：`mkdocs.yml` 中的路径不需要写 `docs/`，因为 MkDocs 默认把 `docs/` 作为内容根目录。

---

## 11. 修改导航显示名称

如果当前导航中有：

```yaml
- MkDocs 维护与发布流程: notes/mkdocs_publish_workflow.md
```

想让它显示得更短，可以改成：

```yaml
- 网站维护流程: notes/mkdocs_publish_workflow.md
```

这里只修改左边的显示名称，右边文件路径不变。

---

## 12. 新增一级导航

如果想新增一个一级导航“工具”，可以在 `nav` 中添加：

```yaml
nav:
  - 首页: index.md
  - 关于我: about.md
  - 项目:
      - 项目总览: projects/index.md
      - GitHub Pages 建站: projects/github-pages.md
  - 学习笔记:
      - 笔记总览: notes/index.md
      - Python: notes/python.md
      - Git: notes/git.md
  - 工具:
      - 工具总览: tools/index.md
```

同时需要创建文件：

```text
docs/tools/index.md
```

内容示例：

```markdown
# 工具

这里整理后续可能开发的小工具。

## 计划

- BMI 计算器
- OR / RR 简单计算器
- 发病率与患病率计算器
- Markdown 写作辅助工具
```

---

## 13. 新增页面

假设要新增一个页面“我的学习路线”。

第一步，创建文件：

```text
docs/notes/study-plan.md
```

第二步，写入内容：

```markdown
# 我的学习路线

## 1. Python

先学习 Python 基础语法、函数、类和文件操作。

## 2. 数据分析

继续学习 pandas、numpy、matplotlib。

## 3. 机器学习

学习 sklearn、SVM、SVR、神经网络等内容。
```

第三步，在 `mkdocs.yml` 中注册导航：

```yaml
  - 学习笔记:
      - 笔记总览: notes/index.md
      - Python: notes/python.md
      - Git: notes/git.md
      - 我的学习路线: notes/study-plan.md
```

只有创建了 Markdown 文件并在 `nav` 中添加，页面才会出现在导航中。

---

## 14. 删除导航项

如果不想显示某个页面，例如：

```yaml
- Git: notes/git.md
```

可以从 `mkdocs.yml` 的 `nav` 中删除这一行。

注意：

- 删除导航项不会删除文件。
- `docs/notes/git.md` 仍然存在。
- 页面只是不会显示在导航中。

如果确认不需要这个文件，可以再手动删除对应 `.md` 文件。

---

## 15. 修改首页内容

首页对应：

```yaml
- 首页: index.md
```

实际文件：

```text
docs/index.md
```

想修改首页正文，就编辑 `docs/index.md`。

示例：

```markdown
# 厉飞雨的个人主页

这里用于记录我的 Python 学习、数据分析实践、机器学习实验和个人网站搭建过程。

## 当前关注方向

- Python 编程
- 公共卫生数据分析
- 机器学习基础
- GitHub Pages 与 MkDocs 网站维护

## 网站内容

- 项目记录
- 学习笔记
- 工具整理
- 网站维护文档
```

---

## 16. 修改“关于我”页面

“关于我”页面对应：

```yaml
- 关于我: about.md
```

实际文件：

```text
docs/about.md
```

示例内容：

```markdown
# 关于我

我是厉飞雨，目前正在学习 Python、数据分析、机器学习和个人网站搭建。

## 当前学习内容

- Python 基础
- pandas / numpy / matplotlib
- scikit-learn
- Git 与 GitHub
- MkDocs 静态网站维护

## 网站用途

这个网站用于记录学习笔记、项目实践和长期积累。
```

---

## 17. 修改项目总览页面

项目总览对应：

```yaml
- 项目总览: projects/index.md
```

实际文件：

```text
docs/projects/index.md
```

示例内容：

```markdown
# 项目总览

这里记录我的项目实践和阶段性成果。

| 项目 | 类型 | 状态 |
|---|---|---|
| [GitHub Pages 建站](github-pages.md) | 网站搭建 | 进行中 |
| Python 数据分析学习 | 数据分析 | 进行中 |
| SVM 三分类实验 | 机器学习 | 计划整理 |
```

在 `docs/projects/index.md` 中：

```markdown
[GitHub Pages 建站](github-pages.md)
```

表示链接到同目录下的：

```text
docs/projects/github-pages.md
```

---

## 18. 修改学习笔记总览页面

学习笔记总览对应：

```yaml
- 笔记总览: notes/index.md
```

实际文件：

```text
docs/notes/index.md
```

示例内容：

```markdown
# 学习笔记

这里整理我的学习记录。

## 分类

- Python
- Git 与 GitHub
- 数据分析
- 机器学习
- 公共卫生统计
- 网站搭建

## 最近整理

- MkDocs 网站维护与发布流程
- GitHub Pages 建站过程
- Python 基础语法
```

---

## 19. 主题颜色如何修改

Material 主题颜色由 `palette` 控制。

当前可能是：

```yaml
palette:
  - scheme: default
    primary: indigo
    accent: blue
    toggle:
      icon: material/weather-night
      name: 切换到深色模式
  - scheme: slate
    primary: indigo
    accent: blue
    toggle:
      icon: material/weather-sunny
      name: 切换到浅色模式
```

其中：

| 配置 | 含义 |
|---|---|
| `scheme: default` | 浅色模式 |
| `scheme: slate` | 深色模式 |
| `primary` | 主色 |
| `accent` | 强调色 |
| `toggle` | 明暗模式切换按钮 |

如果想改成蓝色系：

```yaml
palette:
  - scheme: default
    primary: blue
    accent: cyan
    toggle:
      icon: material/weather-night
      name: 切换到深色模式
  - scheme: slate
    primary: blue
    accent: cyan
    toggle:
      icon: material/weather-sunny
      name: 切换到浅色模式
```

如果想改成绿色系：

```yaml
palette:
  - scheme: default
    primary: teal
    accent: green
    toggle:
      icon: material/weather-night
      name: 切换到深色模式
  - scheme: slate
    primary: teal
    accent: green
    toggle:
      icon: material/weather-sunny
      name: 切换到浅色模式
```

初期建议只改 `primary` 和 `accent`，不要同时改太多主题项。

---

## 20. 主题功能 features 如何理解

当前常用配置：

```yaml
features:
  - navigation.tabs
  - navigation.sections
  - navigation.top
  - search.highlight
  - search.suggest
  - content.code.copy
```

含义如下：

| 功能 | 作用 |
|---|---|
| `navigation.tabs` | 一级导航显示为顶部标签 |
| `navigation.sections` | 左侧导航按章节分组 |
| `navigation.top` | 提供回到顶部功能 |
| `search.highlight` | 搜索结果高亮 |
| `search.suggest` | 搜索建议 |
| `content.code.copy` | 代码块显示复制按钮 |

建议保留这些配置。

如果不想让一级导航显示在顶部，可以删除：

```yaml
- navigation.tabs
```

但对于个人网站来说，保留顶部标签更清晰。

---

## 21. 代码块复制按钮

代码块复制按钮由以下配置控制：

```yaml
features:
  - content.code.copy
```

保留后，网页中的代码块右上角会出现复制按钮。

Markdown 中代码块写法：

````markdown
```python
print("hello")
```
````

生成网页后会自动显示代码样式和复制按钮。

---

## 22. 搜索功能

Material 主题内置搜索功能。

相关配置：

```yaml
features:
  - search.highlight
  - search.suggest
```

含义：

| 配置 | 作用 |
|---|---|
| `search.highlight` | 搜索结果中高亮关键词 |
| `search.suggest` | 输入搜索词时显示建议 |

通常不需要额外写代码。

---

## 23. 页面目录

页面右侧或正文中的目录主要受 Markdown 标题控制。

例如：

```markdown
# 一级标题

## 二级标题

### 三级标题
```

MkDocs 会根据标题自动生成目录。

如果在 `mkdocs.yml` 中配置：

```yaml
markdown_extensions:
  - toc:
      permalink: true
```

则标题旁可能会出现可复制链接的锚点。

---

## 24. Markdown 扩展功能

常用扩展：

```yaml
markdown_extensions:
  - admonition
  - toc:
      permalink: true
  - tables
  - fenced_code
  - attr_list
```

含义：

| 扩展 | 作用 |
|---|---|
| `admonition` | 支持提示框、警告框等 |
| `toc` | 支持目录 |
| `tables` | 支持 Markdown 表格 |
| `fenced_code` | 支持三反引号代码块 |
| `attr_list` | 支持给元素添加属性 |

---

## 25. 提示框写法

如果启用了：

```yaml
markdown_extensions:
  - admonition
```

可以在 Markdown 中写：

```markdown
!!! note "提示"
    这里是一段提示内容。
```

或者：

```markdown
!!! warning "注意"
    这里是一段警告内容。
```

常见类型：

```text
note
tip
warning
danger
info
success
```

---

## 26. 表格写法

如果启用了：

```yaml
markdown_extensions:
  - tables
```

可以写：

```markdown
| 项目 | 类型 | 状态 |
|---|---|---|
| GitHub Pages 建站 | 网站搭建 | 进行中 |
| Python 数据分析 | 数据分析 | 进行中 |
```

---

## 27. 图片如何添加

图片建议统一放在：

```text
docs/assets/images/
```

例如：

```text
docs/assets/images/deploy-success.png
```

如果在首页 `docs/index.md` 引用：

```markdown
![部署成功截图](assets/images/deploy-success.png)
```

如果在 `docs/notes/git.md` 引用：

```markdown
![部署成功截图](../assets/images/deploy-success.png)
```

因为 `notes/git.md` 比 `index.md` 深一层，所以需要 `../` 返回上一层。

---

## 28. 附件如何添加

如果想提供 PDF、Word、Excel 等附件下载，可以建立：

```text
docs/assets/files/
```

例如：

```text
docs/assets/files/report.pdf
```

在 Markdown 中写：

```markdown
[下载报告](../assets/files/report.pdf)
```

路径规则和图片类似。

---

## 29. 自定义 CSS

如果想进一步修改样式，可以创建：

```text
docs/stylesheets/extra.css
```

然后在 `mkdocs.yml` 中加入：

```yaml
extra_css:
  - stylesheets/extra.css
```

示例 CSS：

```css
.md-typeset h1 {
  font-weight: 700;
}

.md-typeset table {
  font-size: 0.9rem;
}
```

注意：初期不建议大量改 CSS。先熟悉 MkDocs 的默认样式。

---

## 30. 自定义 JavaScript

如果确实需要自定义 JS，可以创建：

```text
docs/javascripts/extra.js
```

然后在 `mkdocs.yml` 中加入：

```yaml
extra_javascript:
  - javascripts/extra.js
```

示例：

```javascript
console.log("custom script loaded");
```

初期大部分个人网站不需要自定义 JavaScript。

---

## 31. 添加网站图标 favicon

可以准备一个图标，例如：

```text
docs/assets/images/favicon.png
```

然后在 `mkdocs.yml` 中加入：

```yaml
theme:
  name: material
  favicon: assets/images/favicon.png
```

注意：这里路径同样不写 `docs/`。

---

## 32. 添加网站 Logo

可以准备一个 logo：

```text
docs/assets/images/logo.png
```

然后配置：

```yaml
theme:
  name: material
  logo: assets/images/logo.png
```

如果使用 logo，建议图片简洁，最好是正方形或接近正方形。

---

## 33. 添加 GitHub 仓库链接

可以在 `mkdocs.yml` 中添加：

```yaml
repo_name: siberiagroundhog/siberiagroundhog.github.io
repo_url: https://github.com/siberiagroundhog/siberiagroundhog.github.io
```

这样主题可能会在页面右上角显示 GitHub 仓库入口。

---

## 34. 添加社交链接

Material 支持在 `extra` 中添加社交链接，例如：

```yaml
extra:
  social:
    - icon: fontawesome/brands/github
      link: https://github.com/siberiagroundhog
      name: GitHub
```

后续也可以添加邮箱、Bilibili、知乎等链接，但图标名称要符合 Material 支持的图标库。

---

## 35. 页面内容和导航的关系

一个页面是否显示在导航中，取决于 `mkdocs.yml` 的 `nav`。

例如存在文件：

```text
docs/notes/temp.md
```

但是 `mkdocs.yml` 没有写：

```yaml
- 临时笔记: notes/temp.md
```

那么这个页面不会显示在导航中。

但页面本身可能仍然可以通过 URL 访问，具体取决于构建结果。

推荐做法：

```text
正式页面：加入 nav
草稿页面：暂时不加入 nav
```

---

## 36. 页面 URL 规则

MkDocs 通常会把：

```text
docs/notes/python.md
```

生成类似：

```text
https://siberiagroundhog.github.io/notes/python/
```

把：

```text
docs/projects/github-pages.md
```

生成类似：

```text
https://siberiagroundhog.github.io/projects/github-pages/
```

所以文件路径会影响最终网址。

建议文件名使用英文、小写和短横线，例如：

```text
github-pages.md
python-basic.md
svm-experiment.md
mkdocs-maintenance.md
```

不建议使用中文文件名、空格和特殊符号。

---

## 37. 文件命名建议

推荐：

```text
python-basic.md
github-pages.md
svm-experiment.md
logistic-regression.md
mkdocs-maintenance.md
```

不推荐：

```text
Python 基础.md
我的 笔记.md
第1篇!!.md
```

原因：

- 英文文件名更稳定
- URL 更简洁
- 不容易出现编码问题
- Git 和命令行中更好处理

---

## 38. 一次自定义修改的标准流程

无论修改什么内容，都建议按以下流程执行。

第一步，进入项目目录：

```bash
cd C:\Users\zyc\Desktop\文件夹合集\my-homepage
```

第二步，激活环境：

```bash
conda activate site-docs
```

第三步，确认分支：

```bash
git branch
```

应显示：

```text
* main
```

第四步，修改文件：

| 修改目标 | 修改位置 |
|---|---|
| 网站标题 | `mkdocs.yml` 的 `site_name` |
| 网站描述 | `mkdocs.yml` 的 `site_description` |
| 作者信息 | `mkdocs.yml` 的 `site_author` |
| 网站地址 | `mkdocs.yml` 的 `site_url` |
| 顶部导航 | `mkdocs.yml` 的 `nav` |
| 首页正文 | `docs/index.md` |
| 关于我正文 | `docs/about.md` |
| 项目总览 | `docs/projects/index.md` |
| 学习笔记 | `docs/notes/*.md` |
| 工具页面 | `docs/tools/*.md` |
| 图片 | `docs/assets/images/` |
| 主题颜色 | `mkdocs.yml` 的 `theme.palette` |
| 主题功能 | `mkdocs.yml` 的 `theme.features` |
| 自定义 CSS | `docs/stylesheets/extra.css` + `extra_css` |
| 自定义 JS | `docs/javascripts/extra.js` + `extra_javascript` |

第五步，本地预览：

```bash
mkdocs serve
```

浏览器访问：

```text
http://127.0.0.1:8000
```

第六步，确认无误后停止服务：

```text
Ctrl + C
```

第七步，提交源码：

```bash
git status
git add .
git commit -m "customize mkdocs site"
git push
```

第八步，部署网站：

```bash
mkdocs gh-deploy
```

如果线上未更新，可以强制部署：

```bash
mkdocs gh-deploy --force
```

第九步，刷新线上网站：

```text
https://siberiagroundhog.github.io
```

如果浏览器显示旧内容，按：

```text
Ctrl + F5
```

---

## 39. 常见问题：修改了内容但网站没变

可能原因：

1. 只修改了本地文件，没有部署。
2. 只执行了 `git push`，没有执行 `mkdocs gh-deploy`。
3. GitHub Pages 还没部署完成。
4. 浏览器缓存了旧页面。

解决流程：

```bash
mkdocs gh-deploy --force
```

然后浏览器按：

```text
Ctrl + F5
```

---

## 40. 常见问题：`mkdocs serve` 报错

优先检查：

1. `mkdocs.yml` 是否缩进错误。
2. 是否用了 Tab 缩进。
3. `nav` 中的文件路径是否真实存在。
4. Markdown 代码块是否缺少结束的三个反引号。
5. 当前是否激活了 `site-docs` 环境。

---

## 41. 常见问题：页面出现在本地，但线上没有

检查：

```bash
git status
```

确认修改已经提交：

```bash
git add .
git commit -m "update site"
git push
```

然后部署：

```bash
mkdocs gh-deploy
```

---

## 42. 常见问题：图片不显示

检查：

1. 图片是否放在 `docs/assets/images/`。
2. Markdown 中的路径是否正确。
3. 文件名大小写是否一致。
4. 扩展名是否正确，例如 `.png`、`.jpg`、`.jpeg`。
5. 文件名是否包含空格或特殊符号。

---

## 43. 常见问题：导航新增页面后报错

例如 `mkdocs.yml` 中写了：

```yaml
- SVM: notes/svm.md
```

但实际没有文件：

```text
docs/notes/svm.md
```

就会报错。

解决：

- 创建对应文件；或者
- 从 `nav` 中删除这一行。

---

## 44. 初期最推荐自定义的部分

刚开始不要急着深度改样式。建议优先自定义这些：

1. `site_name`
2. `site_description`
3. `docs/index.md`
4. `docs/about.md`
5. `docs/projects/index.md`
6. `docs/notes/index.md`
7. `nav` 中的显示名称
8. 主题主色 `primary`
9. 主题强调色 `accent`

---

## 45. 暂时不建议大改的部分

初期不建议马上折腾：

- 大量自定义 CSS
- 大量自定义 JavaScript
- 复杂主页布局
- 评论系统
- 访问统计
- 自定义域名
- 自动化 GitHub Actions 部署
- 多语言网站
- 博客高级插件

这些都可以后续逐步加。

---

## 46. 当前最重要的理解

以后修改网站时，不要再想着“改网页”。

应该这样判断：

```text
我要改网站整体配置 → mkdocs.yml
我要改页面正文内容 → docs/对应的 .md 文件
我要改导航显示 → mkdocs.yml 的 nav
我要新增页面 → 新建 .md 文件 + 在 nav 里注册
我要改主题颜色 → mkdocs.yml 的 palette
我要加图片 → 放到 docs/assets/images/ 并在 Markdown 中引用
```

---

## 47. 一句话总结

MkDocs 网站的维护逻辑是：

```text
main 分支写 Markdown 和配置
mkdocs serve 本地预览
git push 保存源码
mkdocs gh-deploy 发布到 gh-pages
GitHub Pages 从 gh-pages 显示网站
```

只要理解：

```text
mkdocs.yml 管全局
docs/ 管正文
gh-pages 管发布成品
```

就能逐步完成大部分自定义。
