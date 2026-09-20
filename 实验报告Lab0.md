# Lab0 实验报告

学号：25300180063

## 一、文档中的问题

### 1. 你之前有过多人协同开发的经历吗？如果有，你们是使用什么方式分工协作的？

之前没有多人协同开发的经历，课程作业和项目基本都是独立完成的；偶尔需要合作时，也只是口头分工后各自完成、再把文件拷贝到一起，没有使用过正式的版本控制工具。

### 2. 思考一下，Git 为什么要设计“暂存-提交”两个步骤？

- **让一次提交成为一个完整的逻辑单元。** 工作区里可能同时存在多个不相关的改动，`git add` 可以把其中一部分先放进暂存区、分多次提交，使每个 commit 只做一件事。这样提交历史清晰，回滚、`revert`、`cherry-pick` 时不会误伤其他改动。
- **提供“反悔”和检查的机会。** 暂存后可以用 `git diff --cached` 检查即将提交的内容，发现多加了调试代码或临时文件，可以用 `git restore --staged` 撤出，而不必先 commit 再修改历史。
- **支持精细的暂存操作。** 例如 `git add -p` 只暂存一个文件里的部分修改，把一个大的改动拆成若干个小提交。
- **从实现上看**，暂存区（index）保存了下次提交的目录树，`git stash`、合并等功能也依赖它作为工作区与仓库之间的中间层。

### 3. `git branch` 和 `git branch -a` 的区别是什么？

- `git branch` 只列出**本地分支**，当前所在分支前面标有 `*`。
- `git branch -a`（`--all`）列出**所有分支**，既包括本地分支，也包括远程跟踪分支（remote-tracking branch），例如 `remotes/origin/main`。远程跟踪分支是本地对远程仓库分支状态的“缓存”，由 `git fetch` / `git pull` 更新，`git push` 后也会同步。
- 类似地，`git branch -r` 只列出远程跟踪分支。
- 所以需要查看远程仓库上有哪些分支（比如别人推送的 `feature` 分支）时，要用 `git branch -a`；只看本地的工作分支时，`git branch` 就够了。

## 二、个人仓库的建立

既然提交了本pdf, 已说明实验仓库的建立完成.

对main.c的修改的commit记录截图如下

![image-20260920144931583](/home/waldein/.config/Typora/typora-user-images/image-20260920144931583.png)



## 三、网页阅读

### 1. Commit Message 规范

阅读：阮一峰《Commit message 和 Change log 编写指南》。

概括：文章介绍了目前使用最广的 Angular 提交规范。一次提交的 commit message 由 Header、Body、Footer 三部分组成：

- **Header**（必需）：格式为 `<type>(<scope>): <subject>`。`type` 说明提交类别（`feat` 新功能、`fix` 修补 bug、`docs` 文档、`style` 格式、`refactor` 重构、`test` 测试、`chore` 构建/工具变动），`scope` 说明影响范围，`subject` 是简短描述（动词开头、现在时、首字母小写、结尾不加句号、不超过 50 字符）。
- **Body**：详细说明改动动机，并与以前的行为对比。
- **Footer**：用于标注不兼容变动（`BREAKING CHANGE`）或关闭 Issue（`Closes #234`）；撤销提交则以 `revert:` 开头。

任意一行不超过 72 个字符。规范化的 commit message 有三个好处：方便快速浏览历史、方便按关键字过滤提交、可以直接用 `conventional-changelog` 等工具自动生成 Change log；也可以用 Commitizen 交互式生成、用 validate-commit-msg 校验格式。

### 2. Git Flow 分支控制

概括：文章介绍了一套以分支为核心、适合团队迭代开发的工作流。分支分为两类：

- **永久分支**：`master` 与线上代码一致，只存放发布版本，每次发布打 tag；`develop` 是开发主线，从 `master` 分出。
- **临时分支**：`feature` 从 `develop` 分出开发新功能，完成后用 `--no-ff` 合回 `develop` 并删除；`release` 在所有功能开发完成后从 `develop` 分出，用于测试与修 bug，完成后合入 `master` 和 `develop` 并打 tag；`hotfix` 在线上出现紧急 bug 时从 `master` 分出，修复后合回 `master` 和 `develop`。

核心原则是“从哪里来，最后回到哪里去”：每个分支从哪个分支创建，完成后就合并回哪个分支。文章还给出了纯 Git 命令与 git-flow 扩展库两种使用示例，以及版本号与 tag 的约定。

### 3. 谈谈你对“为什么要学习 Git”这个问题的理解

结合上面两篇文章，我认为学习 Git 有三层意义：

- **对个人**：Git 是最基本的“时间机器”。它保存每一次修改而不是只留最新版，可以随时查看改了什么、回退到任意版本，避免 `student_old.cpp` 式的备份混乱，也让“为什么这样改”有迹可循。
- **对协作**：多人同时开发时，分支让每个人在独立的环境里工作、互不干扰，merge / PR 流程配合 Code Review 把分散的修改安全地合到一起；Git Flow 这样的分支规范进一步明确了各分支的职责，让协作有序可依。
- **对工程**：Git 是现代软件工程的基础设施。规范化的 commit message 能自动生成 Change log，语义化的版本号让依赖管理有据可依，CI/CD、开源贡献（fork + Pull Request）也都以 Git 为入口。可以说，不会 Git 就很难真正参与一个现代软件项目。

此外，本课程后续的实验都需要用 Git 提交，熟练使用 Git 也能让整个学期的学习更顺畅。

## 四、实验步骤

### 学习 Git 分支管理，新建 `feature` 分支，在该分支以及 `main` 分支上对 `main.c` 分别进行一次修改与提交（10 分）。

新建feature分支

![image-20260920152748851](/home/waldein/.config/Typora/typora-user-images/image-20260920152748851.png)

提交的记录

![image-20260920153159287](/home/waldein/.config/Typora/typora-user-images/image-20260920153159287.png)

![image-20260920153129217](/home/waldein/.config/Typora/typora-user-images/image-20260920153129217.png)

### 随后将 `feature` 分支 merge 到 `main` 分支（即切换回 main 分支执行 `git merge feature`），并处理发生的合并冲突（10 分）。

![截图 2026-09-20 15-26-39](/home/waldein/图片/截图/截图 2026-09-20 15-26-39.png)

![image-20260920153850430](/home/waldein/.config/Typora/typora-user-images/image-20260920153850430.png)

上面是先后在feature和main上进行修改与提交的记录

下面是对合并时冲突发生以及解决的记录

![image-20260920153955394](/home/waldein/.config/Typora/typora-user-images/image-20260920153955394.png)

## 五.建议

没有建议☺
