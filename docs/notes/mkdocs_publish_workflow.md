# MkDocs 网站维护与发布流程

本文记录当前个人网站从内容更新到正式发布的完整流程。当前网站采用：

- GitHub Pages 托管
- MkDocs 生成静态网站
- Material for MkDocs 作为主题
- `main` 分支保存网站源码
- `gh-pages` 分支保存生成后的网页成品

---

## 1. 当前网站的基本机制

当前网站不是直接手写 HTML 页面进行维护，而是使用 MkDocs 管理内容。

整体流程是：

```text
编写 Markdown 内容
        ↓
MkDocs 根据 docs/ 和 mkdocs.yml 生成静态网站
        ↓
mkdocs gh-deploy 将生成结果推送到 gh-pages 分支
        ↓
GitHub Pages 从 gh-pages / root 发布网站
        ↓
用户访问 https://siberiagroundhog.github.io
```

需要重点区分两个分支：

| 分支 | 作用 | 是否手动修改 |
|---|---|---|
| `main` | 保存 MkDocs 源码、Markdown 内容和配置文件 | 是 |
| `gh-pages` | 保存 MkDocs 生成后的网页文件 | 否 |

日常维护时，只需要在 `main` 分支修改内容，不要手动修改 `gh-pages` 分支。

---

## 2. 当前项目结构

推荐保持如下结构：

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
│  │  └─ git.md
│  └─ assets/
│     └─ images/
├─ README.md
└─ old-site/
```

其中：

- `mkdocs.yml`：网站配置文件，控制网站标题、主题、导航栏、扩展功能等。
- `docs/`：网站正文内容目录。
- `docs/index.md`：网站首页。
- `docs/about.md`：关于我页面。
- `docs/projects/`：项目记录。
- `docs/notes/`：学习笔记。
- `docs/assets/images/`：图片资源。
- `old-site/`：旧版手写 HTML 网站备份。

---

## 3. 日常维护的核心原则

以后维护网站时，尽量不要直接写 HTML。

推荐方式是：

```text
写 Markdown
改 mkdocs.yml 导航
本地预览
提交 main 分支
部署到 gh-pages 分支
```

需要记住：

```text
Markdown 负责内容
mkdocs.yml 负责导航和配置
MkDocs 负责生成网页
GitHub Pages 负责托管网站
```

---

## 4. 每次更新网站前的准备

进入项目目录：

```bash
cd C:\Users\zyc\Desktop\文件夹合集\my-homepage
```

激活网站环境：

```bash
conda activate site-docs
```

确认当前分支：

```bash
git branch
```

正常应该显示：

```text
* main
```

如果当前不在 `main` 分支，切回：

```bash
git switch main
```

查看当前状态：

```bash
git status
```

如果工作区干净，通常会看到：

```text
nothing to commit, working tree clean
```

---

## 5. 修改已有页面

如果只是修改已有内容，例如修改首页：

```text
docs/index.md
```

或者修改关于我页面：

```text
docs/about.md
```

直接打开对应 Markdown 文件编辑即可。

修改完成后，先本地预览：

```bash
mkdocs serve
```

浏览器打开：

```text
http://127.0.0.1:8000
```

检查无误后，在终端按：

```text
Ctrl + C
```

停止本地服务。

然后提交并发布：

```bash
git add .
git commit -m "update site content"
git push
mkdocs gh-deploy
```

---

## 6. 新增一篇学习笔记

例如新增一篇 SVM 学习笔记。

第一步，在 `docs/notes/` 下新建文件：

```text
docs/notes/svm.md
```

内容示例：

```markdown
# SVM 学习笔记

## 1. 基本思想

SVM 的核心目标是寻找分类间隔最大的超平面。

## 2. sklearn 中的基本使用

```python
from sklearn.svm import SVC

model = SVC(kernel="rbf", C=1, gamma="scale")
model.fit(X_train, y_train)
```

## 3. 当前理解

后续继续补充模型原理、参数含义和实验记录。
```

第二步，修改 `mkdocs.yml` 中的 `nav`：

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
      - SVM: notes/svm.md
```

第三步，本地预览：

```bash
mkdocs serve
```

确认导航和页面内容正常后，停止服务并发布：

```bash
git add .
git commit -m "add svm note"
git push
mkdocs gh-deploy
```

---

## 7. 新增一个项目页面

例如新增一个机器学习实验项目页面。

第一步，新建文件：

```text
docs/projects/svm-experiment.md
```

内容示例：

```markdown
# SVM 三分类实验

## 1. 项目目标

使用 sklearn wine 数据集完成 SVM 三分类实验，并记录模型训练、参数设置和结果分析过程。

## 2. 技术栈

- Python
- scikit-learn
- matplotlib
- pandas

## 3. 实验流程

1. 加载数据
2. 划分训练集和测试集
3. 标准化数据
4. 训练 SVM 模型
5. 评估准确率
6. 绘制可视化结果

## 4. 当前结果

后续补充实验结果、图像和参数对比。

## 5. 后续计划

- 增加 GridSearchCV 调参记录
- 增加 GA / PSO 优化参数记录
- 增加可视化结果说明
```

第二步，在 `mkdocs.yml` 中添加导航：

```yaml
  - 项目:
      - 项目总览: projects/index.md
      - GitHub Pages 建站: projects/github-pages.md
      - SVM 三分类实验: projects/svm-experiment.md
```

第三步，本地预览并发布：

```bash
mkdocs serve
```

检查无误后：

```bash
git add .
git commit -m "add svm experiment project"
git push
mkdocs gh-deploy
```

---

## 8. 添加图片

图片建议统一放到：

```text
docs/assets/images/
```

例如放入：

```text
docs/assets/images/pages-success.png
```

如果当前 Markdown 文件是：

```text
docs/index.md
```

引用图片：

```markdown
![GitHub Pages 部署成功截图](assets/images/pages-success.png)
```

如果当前 Markdown 文件是：

```text
docs/projects/github-pages.md
```

引用图片：

```markdown
![GitHub Pages 部署成功截图](../assets/images/pages-success.png)
```

如果当前 Markdown 文件在更深层目录中，要根据相对路径调整。

本地预览时一定检查图片是否能正常显示。

---

## 9. 完整发布流程

每次正式发布网站，建议固定使用以下流程。

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

应位于：

```text
* main
```

第四步，本地预览：

```bash
mkdocs serve
```

浏览器访问：

```text
http://127.0.0.1:8000
```

检查以下内容：

- 页面是否能打开
- 导航是否正常
- 图片是否显示
- 代码块是否正常
- 中文是否正常
- 链接是否能点
- 新增页面是否出现在导航栏中

第五步，停止本地预览：

```text
Ctrl + C
```

第六步，提交源码：

```bash
git status
git add .
git commit -m "update site"
git push
```

第七步，部署网站：

```bash
mkdocs gh-deploy
```

如果需要强制重新部署：

```bash
mkdocs gh-deploy --force
```

第八步，访问线上网站：

```text
https://siberiagroundhog.github.io
```

如果页面没有立刻更新，可以等待几十秒到几分钟，或者使用：

```text
Ctrl + F5
```

强制刷新浏览器缓存。

---

## 10. `git push` 和 `mkdocs gh-deploy` 的区别

这两个命令作用不同。

### `git push`

```bash
git push
```

作用是：

```text
把 main 分支中的 MkDocs 源码推送到 GitHub
```

包括：

- `mkdocs.yml`
- `docs/*.md`
- 图片资源
- README.md

它主要负责保存源码。

### `mkdocs gh-deploy`

```bash
mkdocs gh-deploy
```

作用是：

```text
构建网站，并把构建后的网页成品推送到 gh-pages 分支
```

它主要负责更新线上网站。

推荐每次发布都执行：

```bash
git add .
git commit -m "update site"
git push
mkdocs gh-deploy
```

只执行 `git push`，网站可能不会更新。

只执行 `mkdocs gh-deploy`，网站可能更新，但源码修改没有保存到 `main` 分支，不利于长期维护。

---

## 11. `mkdocs.yml` 维护注意事项

`mkdocs.yml` 是网站配置文件，最容易因为格式问题报错。

### 11.1 缩进只能使用空格

正确示例：

```yaml
nav:
  - 首页: index.md
  - 学习笔记:
      - Python: notes/python.md
```

不要使用 Tab 缩进。

### 11.2 层级要对齐

正确示例：

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
```

### 11.3 导航路径必须真实存在

如果写了：

```yaml
- SVM: notes/svm.md
```

就必须存在：

```text
docs/notes/svm.md
```

否则 MkDocs 构建时会报错。

---

## 12. 推荐的内容组织方式

后续内容多了以后，可以逐步整理成：

```text
docs/
├─ index.md
├─ about.md
├─ projects/
│  ├─ index.md
│  ├─ github-pages.md
│  ├─ python-data-analysis.md
│  └─ svm-experiment.md
├─ notes/
│  ├─ index.md
│  ├─ python/
│  │  ├─ basic.md
│  │  ├─ function.md
│  │  └─ oop.md
│  ├─ git/
│  │  ├─ basic.md
│  │  └─ github-pages.md
│  ├─ machine-learning/
│  │  ├─ svm.md
│  │  ├─ svr.md
│  │  └─ neural-network.md
│  └─ public-health/
│     ├─ logistic-regression.md
│     └─ forest-plot.md
├─ tools/
│  ├─ index.md
│  └─ calculator.md
└─ assets/
   └─ images/
```

不要一开始就建太复杂的结构。内容少时保持简单，内容多了再分类。

---

## 13. 推荐的学习笔记模板

```markdown
# 主题名称

## 1. 背景

为什么学这个内容。

## 2. 核心概念

整理关键概念。

## 3. 示例代码

```python
print("hello")
```

## 4. 常见问题

记录自己容易错的地方。

## 5. 总结

用自己的话总结。
```

---

## 14. 推荐的项目记录模板

```markdown
# 项目名称

## 1. 项目目标

写清楚这个项目要解决什么问题。

## 2. 技术栈

- Python
- MkDocs
- GitHub Pages

## 3. 实现过程

记录关键步骤。

## 4. 遇到的问题

记录报错和解决方案。

## 5. 当前结果

放截图、链接或实验结果。

## 6. 后续计划

记录下一步。
```

---

## 15. 常见问题

### 15.1 修改后网站没有更新

可能原因：

1. 只执行了 `git push`，没有执行 `mkdocs gh-deploy`。
2. GitHub Pages 部署还没完成，需要等一会儿。
3. 浏览器缓存导致看到旧页面。

解决：

```bash
mkdocs gh-deploy --force
```

然后浏览器按：

```text
Ctrl + F5
```

---

### 15.2 新页面本地能打开，但线上没有

检查是否执行了：

```bash
git push
mkdocs gh-deploy
```

再检查 GitHub Pages 设置是否仍然是：

```text
gh-pages / root
```

---

### 15.3 `mkdocs serve` 报错

优先检查：

1. `mkdocs.yml` 缩进是否正确。
2. `nav` 中的文件路径是否真实存在。
3. Markdown 代码块的三个反引号是否成对。
4. 当前终端是否激活了 `site-docs` 环境。

---

### 15.4 图片不显示

优先检查：

1. 图片是否放在 `docs/assets/images/`。
2. Markdown 中的相对路径是否正确。
3. 文件名大小写是否一致。
4. 图片扩展名是否写对，例如 `.png`、`.jpg`。

---

## 16. 最简命令清单

本地预览：

```bash
conda activate site-docs
mkdocs serve
```

正常发布：

```bash
git add .
git commit -m "update site"
git push
mkdocs gh-deploy
```

强制发布：

```bash
mkdocs gh-deploy --force
```

查看状态：

```bash
git status
git branch
```

---

## 17. 当前维护原则总结

以后维护网站时，记住这句话：

```text
只在 main 分支写 Markdown 和配置，使用 mkdocs gh-deploy 自动发布到 gh-pages。
```

日常流程可以简化成：

```text
写 Markdown
        ↓
mkdocs serve 本地预览
        ↓
git push 保存源码
        ↓
mkdocs gh-deploy 发布网站
```
