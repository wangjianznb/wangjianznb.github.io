# Git 学习知识库（完整版）

> 零基础到工程实战的系统化 Git 手册，共 7 章 + 速查表。基于 Git 2.40+（`git switch` / `git restore` 需 2.23+）。

## 总目录

| 章节 | 内容 | 建议时长 |
|---|---|---|
| 00 基础概念与安装配置 | 三区域三状态、安装、git config、SSH key、60 秒跑通全流程 | 1 天 |
| 01 核心工作流 | init / add / commit / status / diff / log、提交信息规范 | 2–3 天 |
| 02 分支与合并 | branch / switch / merge / 冲突 / rebase / cherry-pick | 3–4 天 |
| 03 远程仓库与协作 | clone / remote / fetch / pull / push / tag / PR / .gitignore | 2–3 天 |
| 04 撤销恢复与底层原理 | reset / revert / clean / stash / reflog、commit-tree-blob | 3–4 天 |
| 05 进阶工具与钩子 | bisect / blame / worktree / hooks / gc / 统计 | 3–5 天 |
| 06 工程实践 | LFS / submodule / monorepo / 规范清单 / 事故速查 | 2–3 天 |
| 99 速查表 | 按目的查命令、别名配置、报错 → 解法、黄金律 | 随时查 |

## 三条最重要的心智模型

1. **分支只是指向 commit 的指针**（一个存哈希的文本文件），不是副本，所以创建和切换极快。
2. **commit 存的是完整快照 + 指针链**（commit → tree → blob），不是行级差异，所以历史无法篡改、可随时回看任意版本。
3. **几乎所有「丢失」都能找回**：只要提交过，`reflog` 至少保留 30–90 天。所以敢放手去试。

## 学习方法

1. **建一个专用练习仓库**（如 `git-lab`），每个知识点都在里面玩个遍；破坏性操作（`reset --hard`、`clean -fd`）只在练功房做。
2. **每个命令动手前先想「它会动工作区 / 暂存区 / 仓库中的哪几个」**——这是理解 Git 的唯一钥匙。
3. 做完任何操作都用 `git status` 和 `git lg` 看一眼状态与历史图，形成反馈闭环。
4. 报错先看 `hint:` 提示，再查第 06 章与速查表的「报错 → 解法」对照表。
5. **先命令行后 GUI**：命令理解到位了，GUI 只是加速器；只会点按钮的人遇到冲突和误操作无法自救。

## 60 秒最小工作流

```bash
git clone git@github.com:user/repo.git && cd repo
git switch -c feature/my-thing        # 开分支干活
# ...编辑代码...
git status && git diff                # 看改动、确认改动
git add 相关文件                        # 登记进暂存区
git diff --staged                     # 确认这次要提交什么
git commit -m "feat(scope): 描述"      # 本地存档
git push -u origin feature/my-thing   # 上传，然后开 PR
```

---
# git-00 · 基础概念与安装配置

## 1. Git 是什么，为什么必须学

Git 是**分布式版本控制系统**。关键词是「分布式」：你本地就有一份完整的仓库历史，不依赖服务器也能提交、查日志、回滚、开分支。

它能解决的痛点：

| 没有 Git 时的日常 | 有 Git 之后 |
|---|---|
| `代码_final_v3_真正最终版.zip` | 任意历史版本可取回，`git switch` 秒切 |
| 改崩了只能凭记忆重敲 | `git reset` / `git restore` 一键回到上一个好状态 |
| 新功能不敢改，怕影响上线版 | 开分支独立开发，做完再合并 |
| 多人同时改一个文件靠微信传压缩包 | Git 自动合并，冲突处明确标记给你处理 |
| 不知道这行谁什么时候写的、为什么写 | `git blame` / `git log` 精确到人到 commit |
| 服务器上手改代码，本地早就忘了 | 提交 → 推送 → 部署，全程有记录 |

面试、协作、AI 编程工具（Cursor / Copilot 的 diff、回滚）全都建立在 Git 之上。这是**投入 10 小时、受益整个职业生涯**的技能。

## 2. 三个区域与三种状态（最重要的心智模型）

```
        git add            git commit
工作区  ────────▶  暂存区   ────────▶  Git 仓库
(你的文件)       (staging/index)    (history，本地 .git 目录)
   ▲                                  │
   └────────── git restore / checkout ─┘
                  git clone / pull（从远程）
```

- **工作区（Working Directory）**：你眼睛看到的、能编辑的那些文件。
- **暂存区（Staging Area / Index）**：一个「待提交清单」。`git add` 不是「保存」，是「把这个改动登记进下一次提交」。这层设计让你能**把一堆杂乱改动拆成几个干净的提交**。
- **仓库（Repository）**：`.git` 目录里存的历史快照。

所以文件只有三种状态：**已提交（committed）/ 已暂存（staged）/ 已修改（modified）**，未纳入版本控制的叫「未跟踪（untracked）」。

> 新手最大误区：以为 `git add` = 备份、`git commit` = 上传到网上。
> 正解：`add` = 挑改动；`commit` = 在**本地**存档；`push` 才是传到远程服务器。

## 3. 安装

### Windows
- 官网 https://git-scm.com/download/win 下载，一路默认下一步即可。
- 安装会带上 **Git Bash**（一个能用 Linux 命令的终端）。
- 验证：

```powershell
git --version        # git version 2.47.x
where git
```

> 建议装完就把 Git 加入 PATH（默认已做），这样 PowerShell / VS Code 终端里都能直接用 `git`。

### macOS
```bash
xcode-select --install     # 或 brew install git
git --version
```
系统自带的 git 版本偏旧，推荐 Homebrew 版。

### Linux（Debian/Ubuntu）
```bash
sudo apt update && sudo apt install git
```

## 4. 首次配置（必做）

```bash
# 身份：commit 会记录作者，不配置无法提交
git config --global user.name  "你的名字"
git config --global user.email "you@example.com"

# 中文文件名/路径不乱码（Windows 必做）
git config --global core.quotepath false
git config --global gui.encoding utf-8
git config --global i18n.commitencoding utf-8
git config --global i18n.logoutputencoding utf-8

# 编辑器：提交信息用的编辑器
git config --global core.editor "code --wait"      # VS Code
# Windows 记事本：git config --global core.editor notepad

# 换行符（关键，跨平台协作必设）
git config --global core.autocrlf true     # Windows：提交时转 LF，检出时转 CRLF
git config --global core.autocrlf input    # macOS/Linux
git config --global core.symlinks true

# 默认分支名设为 main（新仓库不再叫 master）
git config --global init.defaultBranch main

# 别名（强烈建议配上，效率翻倍）
git config --global alias.st  status
git config --global alias.co  checkout
git config --global alias.ci  "commit -v"
git config --global alias.br  branch
git config --global alias.lg  "log --oneline --graph --decorate --all"
git config --global alias.unstage "reset HEAD --"
git config --global alias.last "log -1 HEAD --stat"
git config --global alias.fix "commit --amend --no-edit"
```

查看全部配置及其来源：

```bash
git config --list --show-origin
git config user.name          # 读单项
```

配置文件位置（优先级从低到高）：

| 层级 | 路径 | 作用域 |
|---|---|---|
| system | `C:\Program Files\Git\etc\gitconfig` | 全机器 |
| global | `~/.gitconfig`（Windows: `C:\Users\你\.gitconfig`） | 当前用户 |
| local | `仓库/.git/config` | 单个仓库（优先级最高） |

`--global` 写到 global，`--local`（默认）写到当前仓库。GitHub 用不同邮箱的仓库可以单独 `git config user.email`。

## 5. SSH 密钥（推送私有仓库 / 免密登录）

```bash
# 生成（-C 注释写你的邮箱；直接一路回车表示不设密码短语）
ssh-keygen -t ed25519 -C "you@example.com"

# Windows 公钥位置
type $env:USERPROFILE\.ssh\id_ed25519.pub     # PowerShell
cat ~/.ssh/id_ed25519.pub                     # Git Bash

# 把公钥内容粘贴到 GitHub → Settings → SSH and GPG keys → New SSH key
# 测试连通
ssh -T git@github.com      # Hi xxx! You've successfully authenticated...
```

之后克隆用 SSH 地址 `git@github.com:user/repo.git`，而不是 HTTPS。HTTPS 方式从 2021 年起不能再用密码，需要 Personal Access Token 或凭据管理器。

> 也可用 `git credential-manager` （Windows 安装 Git 时默认自带），第一次推送会弹浏览器登录 GitHub，之后免密，最简单。

## 6. 图形界面与集成工具

| 工具 | 定位 |
|---|---|
| **VS Code 内置源代码管理面板** | 日常首选，改动列表/暂存/diff/提交点点就行 |
| GitLens 扩展 | VS Code 里的 `blame` 注解、历史可视化、文件时间线，强烈推荐 |
| GitHub Desktop | 零基础最友好，学概念期可用 |
| Sourcetree / Fork / GitKraken | 专业 GUI，分支图清晰 |
| Git Bash / Terminal | 命令行，本文档主讲，理解原理后效率最高 |

学习建议：**先命令行把 20 个核心命令敲熟，再换 GUI 提效**。只会点按钮的人遇到冲突和「误提交」就束手无策了。

## 7. 第一个仓库（60 秒跑通全流程）

```bash
mkdir demo && cd demo
git init                     # 变成 Git 仓库（多出 .git 目录）
echo "# Demo" > README.md
git status                   # Untracked files: README.md
git add README.md            # 进暂存区
git commit -m "docs: 初始化项目"
git log --oneline            # a1b2c3d (HEAD -> main) docs: 初始化项目
echo "更多说明" >> README.md
git add . && git commit -m "docs: 补充说明"
git reset --hard HEAD~1      # 体验一下回滚（本次可撤销，见 04 篇）
```

## 8. 本章练习

1. 完成配置并 `git config --list --show-origin` 检查每一项写到了哪个文件。
2. 生成 SSH key 并添加到 GitHub，用 `ssh -T git@github.com` 验证成功。
3. 建一个 `hello-git` 仓库，连续提交 3 次，用 `git lg`（自己配的别名）看到分支图和 3 条记录。
4. 打开 `hello-git/.git` 目录看看里面有什么：`HEAD`、`config`、`objects/`、`refs/`，猜一下各自作用（04 篇会讲原理）。
5. 故意把 `user.name` 在**当前仓库**改成另一个值，验证 local 优先级高于 global。

---

---
# git-01 · 核心工作流：init / add / commit / status / diff / log

> 这一章吃透，你就完成了「能自己存档干活」的门槛。

## 1. git init —— 创建仓库

```bash
git init                       # 在当前目录初始化，生成隐藏的 .git
git init --initial-branch=main # 指定主分支名
git init --bare bare.git       # 裸仓库（无工作区，做服务器端仓库用）

# 把一个已有目录变成仓库
cd 我的项目
git init
git add .
git commit -m "chore: 导入现有代码"
```

> **撤销一个仓库**：删除 `.git` 目录即可，文件本身不受影响（Windows 需先显示隐藏文件，或 `rm -rf .git`）。

## 2. git status —— 你的导航仪

任何时候迷路了，先敲 `git status`。它会明确告诉你当前分支、哪些文件已改、哪些已暂存、并**提示下一步该敲什么命令**。

```bash
git status              # 简洁输出（推荐配别名 git st）
git status -s           # 极简输出（下面这种短格式，脚本和高手常用）
 M README.md            # 空格+M = 工作区改了但没 add
M  README.md            # M+空格 = 已 add，还没 commit
A  new.txt              # 新增并暂存
D  old.txt              # 删除并暂存
?? draft.md             # 未跟踪（untracked）
UU main.py              # 合并冲突未解决
git status -sb          # 再显示分支与远程的领先/落后数：## main...origin/main [ahead 2]
```

## 3. git add —— 登记改动（不是提交）

```bash
git add file.txt             # 指定文件
git add src/ docs/*.md       # 目录 / 通配符
git add .                    # 当前目录所有改动（含删除，不含未跟踪的新增在老版本有差异）
git add -A                   # 全仓库所有改动（新增+修改+删除）
git add -u                   # 只跟踪已修改和已删除，不加新文件
git add -p                   # ⭐ 交互式逐块暂存：把大改动拆成小提交的神器
git add -i                   # 交互菜单模式
git add -f .gitignore        # 强制添加被忽略的文件（如必须提交某配置）
git add note.txt             # 空文件不会被跟踪，Git 不存空目录
```

### 取消暂存（add 错了怎么退回）

```bash
git restore --staged file.txt    # 新写法（推荐），把文件从暂存区撤回工作区，改动还在
git reset HEAD file.txt          # 旧写法，等价
git reset                        # 撤回本次所有暂存
```

## 4. git commit —— 生成快照

```bash
git commit -m "feat: 支持导出 CSV"              # 最常见
git commit                                       # 打开编辑器写多行信息
git commit -m "标题" -m "正文详细说明"            # 标题 + 正文
git commit -a -m "fix: 修正笔误"                 # ⭐ 自动 add 所有「已跟踪文件」的改动并提交
git commit --amend -m "改一下上一条信息"          # 修改最近一次提交的信息
git commit --amend --no-edit                     # 把漏掉的改动补进上一次提交（不写新信息）
git commit --allow-empty -m "chore: 触发 CI"     # 空提交，常用于重新跑流水线
git commit -C HEAD                               # 复用上一次提交信息
```

> `commit -a` 不包含新增的未跟踪文件，容易漏提交，练习期建议老老实实 `add` 再 `commit`。
> **已经 push 到远程共享的提交不要 amend**（会改写历史，别人会冲突）。

### 提交信息怎么写（团队规范 / Conventional Commits）

```
feat: 新功能          fix: 修 bug
docs: 文档            style: 格式（不影响逻辑）
refactor: 重构        perf: 性能优化
test: 测试            build: 构建/依赖            ci: 流水线
chore: 杂项           revert: 回滚

格式：<type>(<scope>): <subject>

示例：
feat(auth): 增加手机号登录
fix(order): 修复并发下库存超卖

空一行
body：为什么改、怎么改的，而不是「改了什么」（diff 已经写了）
空一行
footer：Closes #123 / BREAKING CHANGE: 接口改为 v2
```

判断标准：**只看这一行 log，别人能不能知道这次干了什么**。「update」「改代码」「fix bug」这类提交信息等于没写。

## 5. git diff —— 看具体改了什么

```bash
git diff                        # 工作区 vs 暂存区（我改了什么还没 add）
git diff --staged               # 暂存区 vs 最新提交（我这次要提交什么）⭐ 提交前必查
git diff file.txt               # 只看某个文件
git diff HEAD                   # 工作区 vs 最新提交（所有未提交的改动）
git diff main dev               # 两个分支差异
git diff HEAD~1 HEAD            # 最近一次提交改了什么（等价 git show）
git diff v1.0..v2.0 -- src/     # 版本间某目录
git diff --stat                 # 只要文件级统计（改了几个文件、增删多少行）
git diff --name-only            # 只要文件名
git diff --word-diff            # 按词而非按行比较，看长文本改动更清晰
git difftool                     # 调用外部图形对比工具（Beyond Compare 等）
```

## 6. git log —— 读历史

```bash
git log                                        # 完整信息（按 q 退出）
git log --oneline                              # 一行一个，最常用
git log --oneline -5                           # 最近 5 条
git log --graph --oneline --decorate --all     # ⭐ 图形看所有分支（配成别名 git lg）
git log --stat                                 # 附带文件改动统计
git log -p                     # 附带完整 diff（看某段代码怎么来的）
git log -2 -p README.md        # 只看某文件的两次改动
git log --follow  a.py         # 文件被重命名后仍能追到历史
git log --author="张三"        # 按作者
git log --since="2 weeks ago" --until=yesterday
git log --grep="登录"           # 在提交信息里搜
git log -S "def login"         # ⭐ 搜「哪次提交引入了这段代码」
git log -L 10,40:app.py        # 看某文件某几行的演变史（神器）
git log main..dev              # dev 有、main 没有的提交
git log --no-merges            # 排除 merge 提交
git log --pretty=format:"%h %ad %an %s" --date=short
git log origin/main..HEAD      # 我本地领先远程的提交（push 前自检）
```

查看单个对象/提交详情：

```bash
git show HEAD                  # 最新提交的作者+信息+完整 diff
git show a1b2c3d:src/main.py   # 看某个历史版本的文件内容
git show --stat HEAD~2
git cat-file -p HEAD           # 看提交对象原始内容（原理篇用）
```

## 7. 撤销与恢复（本库 04 篇细讲，先记这几个）

```bash
git restore file.txt               # 放弃工作区的修改（从暂存区/HEAD 还原）
git restore --source=HEAD~3 file   # 取回某历史版本的文件
git checkout -- file               # 老写法，同 restore
git clean -n                       # 预览会删掉哪些未跟踪文件（安全）
git clean -fd                      # 真正删除未跟踪文件和目录 ⚠️
git stash                          # 临时把改动收起来 → 见 04 篇
```

## 8. 完整工作流串起来（肌肉记忆）

```bash
# 场景：改代码 → 提交
git status                         # 我在哪、有什么改动
# ...编辑代码...
git diff                           # 确认改动是否符合预期（别把调试代码提交了）
git add src/login.py               # 只登记相关文件（一次提交只做一件事）
git diff --staged                  # 二次确认要提交的内容
git commit -m "feat(login): 增加验证码校验"
git status                         # 确认工作区干净
git lg                             # 看一眼历史
```

每天开工第一件事：`git status` + `git pull`（或 `git fetch` 后再决定怎么合）。

## 9. 本章练习

1. 新建仓库，制造一次「改了 3 个文件但只想提交其中 2 个」的场景，用 `git add` + `git restore --staged` 完成。
2. 用 `git add -p` 把同一个文件里的两处改动拆成两个独立提交。
3. 写出符合 Conventional Commits 规范的 5 条提交，包含 `feat(scope):` 形式与一条正文较长的。
4. 用 `git commit --amend` 修正一次写错的提交信息，并解释为什么 push 之后不该这样做。
5. 用 `git log -S` 找出某段代码是在哪次提交被引入的。
6. 用 `git show <hash>:文件名` 查看并保存某个历史版本的文件内容，不改当前分支。
7. 给自己配好 `git lg`、`git st`、`git unstage` 别名并熟练使用（后面章节都会用到）。

---

---
# git-02 · 分支与合并

## 1. 分支到底是什么

Git 的分支**极其轻量**：一个分支就是一个 41 字节的文本文件，里面写着一个 commit 的哈希。所谓「切换分支」只是把工作区文件换成那个快照。

```
main      A───B───C          ← 分支 main 指向 C
                  \
dev              D───E        ← 分支 dev 指向 E
                       ↑
                     HEAD    ← HEAD 指向「当前所在分支」，工作区就跟着它
```

三个概念必须分清：

| 名字 | 是什么 |
|---|---|
| 分支（branch） | 一个 movable pointer，指向某个 commit |
| `HEAD` | 指向当前分支的指针（分离头指针时直接指向 commit） |
| `origin/main` | 远程跟踪分支 = 本地记录的「上次远程 main 在哪」，不是实时状态 |

## 2. 查看与创建

```bash
git branch                      # 本地分支，* 标记当前
git branch -v                   # 带最后一次提交
git branch -a                   # 本地 + 远程跟踪分支
git branch -r                   # 只看远程
git branch -vv                  # ⭐ 看本地分支与远程的对应关系及领先/落后

git branch dev                  # 创建但不切过去
git branch dev a1b2c3d          # 从指定 commit 创建
git switch -c dev               # ⭐ 创建并切换（新命令，推荐）
git checkout -b dev             # 老写法，完全等价
git switch main                 # 切换分支
git switch -                     # 切回上一个分支（很实用）
git checkout a1b2c3d            # 分离头指针：进入某个 commit 看代码（改了就另建分支）
git switch -c hotfix a1b2c3d    # 从历史点开分支修 bug
```

> 切换分支前工作区必须干净（已 add 的改动可能被带到新分支），或先 `git stash`。Git 允许携带「不会与新分支冲突的未暂存改动」，新手容易被这个行为绕晕。

## 3. 改名与删除

```bash
git branch -m 新名              # 重命名当前分支
git branch -M main              # 强制改
git branch -d dev               # 删除（已合并才允许，安全）
git branch -D dev               # 强删（未合并也删）⚠️ 但 commit 还在 reflog 里能捞回来
git push origin --delete dev    # 删除远程分支
git push origin -d dev          # 简写
```

## 4. 合并 merge

```bash
git switch main
git merge dev                   # 把 dev 的提交并入当前分支（方向务必看清：目标是「当前分支」）
git merge --no-ff dev           # ⭐ 强制生成合并提交，保留分支历史形状（团队协作推荐）
git merge --ff-only dev         # 只允许快进，不能快进就失败（保持历史干净的检查）
git merge --squash dev          # 把 dev 的所有改动压成一个待提交改动（不落分支历史）
git merge --abort               # 冲突时放弃本次合并，回到合并前
git merge origin/main           # 合并远程跟踪分支（等价于 pull，但更可控）
```

### 两种合并结果

**Fast-forward（快进）**：main 没有新提交，Git 只把指针往前挪，**不产生新提交**。历史是一条直线。

```
main: A─B─C            dev:  C─D─E
merge 之后 main 直接指向 E：A─B─C─D─E      （无新 commit）
```

**三方合并（True merge）**：两边都有新提交 → Git 找到共同祖先，生成一个有两个父提交的合并节点。

```
A───B───C───────M     ← M 是新 merge commit，parents = C 和 E
    \         /
     └──D───E  (dev)
```

想强制产生合并节点用 `--no-ff`（即使技术上可以快进），这样 PR/合并记录在图上清晰可见。

## 5. 解决冲突（必须会，别慌）

### 长什么样

```python
def calc(price):
<<<<<<< HEAD
    # main 分支的写法
    return price * 1.1
=======
    # dev 分支的写法
    return price * 1.05 + tax(price)
>>>>>>> dev
```

`<<<<<<< HEAD` 到 `=======` 是你当前分支的内容，`=======` 到 `>>>>>>>` 是被合并进来的内容。

### 处理流程

```bash
git status                       # Unmerged paths: 列出所有冲突文件
# 1) 打开冲突文件，决定保留哪部分（或手工融合成正确代码），删掉三个标记行
git add src/calc.py              # 2) 标记「这个文件的冲突我已解决」
git status                       # 全部变成 to be committed
git commit                       # 3) 完成合并（用默认信息即可）
```

### 工具

```bash
git mergetool                    # 调用图形化工具（VS Code/Beyond Compare/kdiff3）
git checkout --ours  file        # 整个文件取当前分支版本
git checkout --theirs file       # 整个文件取被合并分支版本
git restore --ours/--theirs file # 新写法
code --merge --output file file  # 用 VS Code 处理（需配 difftool/mergetool）
git diff                         # 冲突时看三方差异
```

冲突不可避免的场景：两人改了同一行、一边改一边删、重命名与修改撞车。**解决冲突是理解代码的过程，不是 Git 出了错**。

## 6. rebase：改写历史的另一种合并

`rebase` 把你的提交「摘下来」，在目标分支的最新提交之后重新播放一遍，历史变成一条直线。

```bash
# 场景：dev 落后 main 太多，先把 main 的新提交同步进来
git switch dev
git rebase main                  # 把 dev 独有的提交移到 main 顶端之后

# 效果对比
# merge 后：        A─B─C─M          （多一个 merge 节点，保留真实过程）
#                       \ /
#                        D─E
# rebase 后：        A─B─C─D'─E'     （线性，提交哈希变了！）
```

```bash
git rebase --continue            # 冲突解决并 add 之后继续
git rebase --abort               # 放弃整个 rebase，回到开始状态（后悔药）
git rebase --skip                # 跳过当前这个提交
git rebase -i HEAD~3             # ⭐ 交互式 rebase：合并/改信息/重排/删除最近 3 个提交
git rebase --onto main dev-start feature   # 只把 feature 的部分提交挪到 main（cherry-pick 篇）
```

### 交互式 rebase 的 pick 列表

```
pick  a1b2  feat: 加了登录
pick  c3d4  fix: 修正登录错别字      ← 改成 squash/fixup 就并进上一条
pick  e5f6  wip                    ← 改成 drop 删除
reword 1a2b ...                    ← 只改这条的信息
edit  ...                          ← 停下来让你改内容（配合 git commit --amend + git rebase --continue）
```

指令：`pick` 保留、`reword` 改信息、`edit` 停下、`squash` 合并并保留信息、`fixup` 合并并丢弃信息、`drop` 丢弃。可**随意调整行的顺序来重排提交**。

### merge vs rebase 选择

| | merge | rebase |
|---|---|---|
| 历史形状 | 有分叉、有合并节点，真实 | 线性、干净 |
| 是否改写已有提交 | 否 | **是**（哈希全变） |
| 适用 | 把功能分支并入 main（团队主干） | 同步主干到自己未推送的功能分支 |
| 风险 | 图较乱 | 对已推送的公共分支 rebase = 全组灾难 |

> **黄金律：只 rebase 你本地还没推送、或只有你一个人在用的分支。**
> 已经 push 出去被别人拉走的分支，改写历史会让所有人崩溃（要救必须 `push --force-with-lease` 并通知全组）。

## 7. cherry-pick：只要某一次提交

```bash
git switch hotfix
git cherry-pick a1b2c3d              # 把那次提交的改动复制成新提交
git cherry-pick -n a1b2              # 只应用改动不提交（-n = --no-commit，便于继续修改）
git cherry-pick a1b2..c3d4           # 范围（不含 a1b2）
git cherry-pick --continue / --abort / --skip
git cherry-pick -x a1b2              # 信息里注明来源提交（跨分支移植留痕）
```

典型用途：把 main 上的一个 bugfix 单独搬到未发布的老版本分支。

## 8. 分支管理策略（看懂团队怎么协作）

- **GitHub Flow**：`main` 永远可发布，功能开 `feature/xxx`，测试通过后 PR 合回 main。简单，适合持续部署。
- **Git Flow**：`main` + `develop` + `feature/` + `release/` + `hotfix/`。适合定期发版的项目，重。
- **主干开发（Trunk Based）**：小步快提，用 feature flag 控制未完成功能，大厂 CI/CD 常用。

分支命名约定（建议统一）：

```
feature/用户登录          bugfix/订单金额错误          hotfix/线上崩溃
release/v2.3             refactor/拆分service层
```

## 9. 本章练习

1. 建仓库提交 3 次，创建 `dev` 分支再提交 2 次，切回 main 提交 1 次（制造分叉），分别用 `git merge` 和 `git merge --no-ff` 观察 `git lg` 的图形差异。
2. 手工制造一个真实冲突（两个分支改同一行），完成「编辑 → add → commit」全流程，再用 `git mergetool` 走一遍。
3. 用 `git rebase main` 消除上面的分叉，对比两幅图；解释提交哈希为什么变了。
4. 写 5 个杂乱提交（含 "wip"、"fix typo"），用 `git rebase -i HEAD~5` 整理成 2 个规范提交。
5. 在 `dev` 上修一个 bug 并提交，用 `cherry-pick` 把它搬到 `hotfix` 分支。
6. 用 `git checkout --ours` / `--theirs` 各解决一次冲突，说明它们分别保留了哪个分支的内容。

---

---
# git-03 · 远程仓库与团队协作

## 1. 远程仓库概念

远程 = 另一台机器上的 Git 仓库，充当团队的「交换中心」。常见托管平台：GitHub、Gitee（国内快）、GitLab（自建）、Coding。

```
本地仓库                        远程仓库 origin
main ──── push ────▶        origin/main（远程跟踪分支，本地的"上次已知状态"）
      ◀──── fetch ────
```

关键点：**`origin/main` 只在你 `fetch`/`pull` 时才更新**。它不代表远程此刻的真实状态，别看到本地 `origin/main` 没动就以为别人没提交。

`origin` 只是远程的默认别名（名字随便起，如 `upstream`）。

## 2. 克隆与关联

```bash
git clone https://github.com/user/repo.git        # HTTPS
git clone git@github.com:user/repo.git            # SSH（免密，推荐）
git clone -b dev repo_url                          # 只检出指定分支
git clone --depth 1 repo_url                       # ⭐ 浅克隆，只要最近 1 层历史（CI/超大仓库提速）
git clone --depth 1 --branch v1.0 url              # 只要某标签，构建用
git clone --single-branch --branch main url        # 只要一个分支
git clone url mydir                                # 指定本地目录名
git clone --recursive url                          # 连带子模块

git remote -v                     # 查看已关联的远程
git remote add origin git@github.com:user/repo.git     # 把本地仓库连到远程
git remote rename origin old-origin
git remote remove origin
git remote set-url origin 新地址    # SSH/HTTPS 互换、仓库改名
git remote show origin             # ⭐ 远程详情：哪些分支被跟踪、push 会推哪个
```

### 首次推送并建立「跟踪关系」

```bash
git init
git add . && git commit -m "chore: init"
git remote add origin git@github.com:user/repo.git
git push -u origin main           # -u（--set-upstream）记录对应关系
# 之后直接 git push / git pull 即可，不用每次写分支名
git branch --set-upstream-to=origin/main main     # 忘了 -u 时补上
```

### 已有仓库改地址

```bash
cd 旧项目 && rm -rf .git
git init && git remote add origin 新地址
git add . && git commit -m "chore: 迁移到 GitHub"
git push -u origin main
```

## 3. fetch / pull / push

```bash
git fetch origin                  # ⭐ 只下载，不改工作区（安全，团队推荐）
git fetch --all --prune           # 抓所有远程，并清理别人已删的远程分支
git fetch origin main             # 只抓某分支
git pull                          # = fetch + merge（默认）
git pull --rebase                 # ⭐ = fetch + rebase，历史线性，不在主干留 "Merge branch" 垃圾提交
git pull --ff-only                # 只允许快进，有分叉就报错让你自己决定（最安全）
git pull --rebase --autostash     # 有未提交改动时自动 stash 再恢复

git push                          # 推当前分支到它跟踪的远程
git push origin main              # 显式指定
git push -u origin feature        # 首次推新分支并建立跟踪
git push --force-with-lease       # ⭐ 安全强推：远程若被别人动过就拒绝（比 --force 好）
git push --force                  # ⚠️ 盲目覆盖，团队里禁用
git push origin --delete old-branch
git push origin :refs/tags/v0.1    # 删远程标签
```

### 为什么推荐 `git pull --rebase`（团队共识）

用默认 `git pull` 时，每次远程有新提交你都会得到一个 `Merge branch 'main' of github...` 的空洞合并提交，几周后历史图变成「地铁线路图」，无法阅读。配置一劳永逸：

```bash
git config --global pull.rebase true
git config --global rebase.autoStash true
git config --global fetch.prune true
git config --global push.default current
git config --global push.autoSetupRemote true     # 新分支首次 push 自动带 -u
```

### fetch 之后怎么合

```bash
git fetch
git log --oneline HEAD..origin/main     # 远程多了什么
git diff HEAD..origin/main              # 具体改了什么
git merge origin/main                   # 或 git rebase origin/main
```

## 4. 领先/落后与同步检查

```bash
git status                        # Your branch is ahead of 'origin/main' by 2 commits / behind...
git log --oneline origin/main..HEAD        # 我领先的（还没 push）
git log --oneline HEAD..origin/main        # 我落后的（需要 pull）
git rev-list --left-right --count origin/main...HEAD    # 输出 "3 1" = 落后3 领先1
git branch -vv                    # [origin/main: ahead 1]
```

## 5. 标签 tag

版本发布的锚点，指向固定 commit、永不移动。

```bash
git tag v1.0.0                         # 轻量标签
git tag -a v1.0.0 -m "第一版正式发布"    # ⭐ 附注标签（含作者日期，推荐）
git tag -a v1.0.0 一个commit哈希        # 给历史补标签
git tag                                # 列出
git show v1.0.0                        # 详情
git checkout v1.0.0                    # 检出该版本代码（进入分离头指针，只读）
git switch -c hotfix-v1 v1.0.0         # ⭐ 基于标签开分支修线上问题

git push origin v1.0.0                 # 标签不会随 push 自动上传，必须显式推
git push origin --tags                 # 推全部
git push --follow-tags                 # 只推当前提交涉及的标签（推荐）
git tag -d v1.0.0                      # 删本地
git push origin -d v1.0.0              # 删远程
git tag -l "v1.*"
```

语义化版本：`v主.次.补丁`（1.2.3）——破坏性改动升主版本、新功能层次版本、修 bug 升补丁。

## 6. Fork + Pull Request（开源协作标准姿势）

```
upstream（原作者）  ◀──── PR ────  origin（你 fork 的）  ◀──push──  本地
```

```bash
# 在 GitHub 点 Fork，然后：
git clone git@github.com:你/repo.git
git remote add upstream git@github.com:原作者/repo.git     # 加原作者地址
git fetch upstream
git switch -c feature/xxx upstream/main                    # 基于最新主干开分支
# ...开发、提交...
git push origin feature/xxx                                # 推到你自己的 fork
# 在 GitHub 页面点 "Compare & pull request"
# 维护者合并后清理本地：
git fetch --prune upstream origin
git branch -d feature/xxx
git push origin --delete feature/xxx
```

PR 前整理提交的惯用组合：

```bash
git rebase -i origin/main      # 合并碎提交、改信息、reorder
git push --force-with-lease    # 改写了自己分支历史，需要安全强推
```

## 7. .gitignore 忽略规则（每个仓库第一件要做的事）

```gitignore
# 注释；以 # 开头
*.pyc                  # 所有 .pyc
__pycache__/           # 目录（结尾斜杠），含所有层级
.env                   # 精确文件名
!/.env.example         # 取反：这个不忽略（前面有 * 时用 !）
build/
dist/
node_modules/
.venv/
venv/
*.log
.DS_Store              # macOS
Thumbs.db              # Windows
.vscode/*              # 忽略内容
!.vscode/settings.json # 但提交这个
data/*.tmp
/temp                  # 只忽略根目录下的 temp
/docs/secret.md
*.swp
```

```bash
git check-ignore -v file.txt        # ⭐ 查这个文件被哪条规则忽略了（排错必备）
git check-ignore -v --no-index src/ # 结合 .git/info/exclude 一起看
git rm --cached -r .idea            # ⭐ 已经误提交的文件从版本控制移除（本地文件保留！）
git rm --cached .env && git commit -m "chore: 忽略密钥文件"
```

三种忽略机制的区别：

| 位置 | 作用范围 | 用途 |
|---|---|---|
| `.gitignore`（提交） | 全组共享 | 语言/框架产物 |
| `.git/info/exclude` | 仅本地该仓库 | 个人的临时忽略，不污染团队 |
| `~/.config/git/ignore`（`core.excludesFile`） | 全局 | `.DS_Store`、编辑器备份等个人杂物 |

> **Git 忽略规则只对未跟踪文件生效。**已经提交过的文件，加进 `.gitignore` 也还会被跟踪，必须先 `git rm --cached`。

已废弃但常见：模板可在 https://www.toptal.com/developers/gitignore 生成（`npx gitignore python`）。

## 8. 线上密钥泄露怎么办

一旦把密码/token 提交并 push，**它就已经泄露了，删掉文件没用**（历史里还在）。正确处置：

1. **立刻让密钥失效**：去平台撤销 token、改密码。这是唯一真正有效的动作。
2. 从历史清除：`git filter-repo`（官方推荐，取代老式 `filter-branch`）或 BFG Repo-Cleaner。
3. 强推覆盖远程，并通知协作者重新 clone。
4. 预防：本地 `.gitignore` 挡住、`.env` + `python-dotenv` 加载、pre-commit 钩子扫描（`detect-secrets` / gitleaks）、GitHub Secret Scanning 告警。

## 9. 多人协作流程建议

```bash
# 每日流程
git switch main && git pull --rebase         # 开工先同步主干
git switch -c feature/xxx                    # 一功能一分支
# ... 小步提交，每次提交前 git diff --staged 自检
git fetch && git rebase origin/main          # 提交 PR 前先同步主干并解决冲突
git push -u origin feature/xxx               # 推送、开 PR
# Review 通过、CI 绿 → 合并（团队常设 squash merge 保持主干一功能一提交）
```

协作礼仪：不要长期独占共享分支、提交保持小而连贯、`git status` 干净再切分支、绝不强推公共分支、合并冲突先沟通语义而不是只挑一边。

## 10. 本章练习

1. 在 GitHub/Gitee 建空仓库，把本地练习项目推上去，验证 `git remote -v`、`git branch -vv` 的跟踪关系。
2. 用 `git fetch` + `git log HEAD..origin/main` 找出远程多了哪些提交，再分别用 merge 和 rebase 同步，比较历史图。
3. 配置 `pull.rebase=true` 等推荐项，写一次 PR 并合并（可自己 fork 自己的仓库练）。
4. 给项目打 `v1.0.0` 附注标签并推送；然后基于该标签开 `hotfix` 分支再合并回 main。
5. 写一份 Python 项目的 `.gitignore`，然后故意 `git add` 一个 `.log` 文件验证被拦下，用 `git check-ignore -v` 找出规则。
6. 模拟「误提交 .env」：用 `git rm --cached` 移除 + 加忽略规则 + 提交，说明为什么还需要撤销密钥。

---

---
# git-04 · 撤销、恢复与底层原理

> 这一章解决「我把它搞坏了，怎么救回来」。理解 Git 用「快照 + 指针」存数据之后，所有撤销命令都不再神秘。

## 1. 后悔药总览（先看这张表）

| 我想…… | 命令 | 影响 |
|---|---|---|
| 丢弃工作区的某文件改动 | `git restore file` | 危险（改动没了） |
| 取消 add | `git restore --staged file` | 安全 |
| 修改最后一次提交的信息/内容 | `git commit --amend` | 本地安全，已 push 需注意 |
| 撤销提交但保留改动（回到 add 前） | `git reset --soft HEAD~1` | 安全 |
| 撤销提交并把改动退回工作区 | `git reset --mixed HEAD~1`（默认） | 安全 |
| 彻底丢弃提交和改动，回到上个版本 | `git reset --hard HEAD~1` | **危险** |
| 用「新提交」安全地抵消某次提交 | `git revert HEAD` | 安全，公共分支用这个 |
| 删除未跟踪的新文件 | `git clean -fd`（先 `-nd` 预览） | **危险** |
| 找回「以为丢了的提交」 | `git reflog` + `git reset --hard <哈希>` | 救命 |
| 临时保存现场去干别的 | `git stash` / `git stash pop` | 安全 |

## 2. git reset —— 移动当前分支指针

reset 做三件事之一，取决于模式（这是理解 reset 的唯一钥匙）：

```
git reset --soft   <提交>   →  只动分支指针，暂存区和工作区不变
git reset --mixed  <提交>   →  动指针 + 清空暂存区，工作区不变（默认）
git reset --hard   <提交>   →  动指针 + 清空暂存区 + 覆盖工作区  ⚠️
```

```bash
git reset HEAD~1              # 等价 --mixed：撤掉提交，改动还在工作区，重新整理再提交
git reset --soft HEAD~1       # 把最后 2 次提交「合并」成待提交状态 → 然后一次 commit
git reset --hard a1b2c3d      # 回到某个版本，工作区完全复原（本地未提交改动全部丢弃）
git reset .                   # 把当前目录全部退回 HEAD 的暂存状态
git reset --keep HEAD~1       # 会覆盖本地未提交改动时自动中止（比 --hard 温和）
```

典型用法：
- 提交信息写错/漏文件 → `git reset --soft HEAD~1` 后重新 `commit`（比 `--amend` 更直观，一次退多条时用 soft）。
- 想把 5 个碎提交并成 1 个 → `git reset --soft HEAD~5` + `git commit`。

> `git reset` 只动**本地**分支。已经推给别人的历史，reset 后必须 `--force-with-lease` 才能覆盖远程 —— 也就是为什么公共分支应该用 `revert`。

## 3. git revert —— 用新提交抵消（安全撤销）

```bash
git revert HEAD                    # 生成一个「把最后一次提交反着做」的新提交
git revert a1b2c3d                 # 撤销指定提交
git revert -n a1b2 c3d4            # -n：只应用反向改动不提交（多个 revert 合并成一次提交）
git revert -m 1 合并提交哈希        # ⭐ 撤销一次 merge（-m 1 = 保留主干那条线）
git revert --abort / --continue
```

revert 不改写历史，只往前追加，所以**公共分支 / main 上出错，一律用 revert**。

## 4. git checkout / switch / restore 的分工

新 Git 把老 `checkout` 的职责拆开了，更清晰：

```bash
git switch dev                       # 切分支
git switch -c new                    # 建分支
git restore file                     # 还原本文件的工作区改动（丢改动，不可逆）
git restore --staged file            # 退暂存
git restore --source=v1.0 file       # 从指定版本恢复
git restore --worktree --staged file # 同时清暂存和工作区（≈ 彻底丢弃）
git checkout HEAD -- file            # 老写法，还原样能用
```

## 5. git clean —— 清除未跟踪文件

```bash
git clean -n            # ⭐ 先预览（dry-run），永远这样开始
git clean -fd           # 删未跟踪文件 + 目录
git clean -fdx          # 连 .gitignore 忽略的也删（清 build 产物，慎用）
git clean -fi           # 交互确认每一个
git clean -fd path/     # 只清某目录
```

## 6. git stash —— 临时收纳现场

场景：改到一半，突然要切分支修线上 bug，但改动还不够提交。

```bash
git stash                          # 把「已跟踪文件的改动」压起来，工作区瞬间干净
git stash push -m "登录页改到一半"   # 带说明
git stash push -u -m "含新文件"      # -u 连未跟踪文件一起存 ⭐
git stash list                     # stash@{0}: On main: 登录页改到一半
git stash show -p stash@{0}        # 看里面是什么
git stash pop                      # 恢复并删除该记录（最常用）
git stash apply                    # 恢复但保留记录（要复制给多个分支时用）
git stash drop stash@{1}           # 删某条
git stash clear                    # 全清 ⚠️
git stash apply stash@{2}          # 恢复指定一条
git stash branch 新分支名           # ⭐ 基于 stash 时的基点开分支并恢复（stash 时基线变了也能干净应用）
git stash push -- src/a.py         # 只 stash 某个文件
```

注意：
- stash 默认不含未跟踪文件和被忽略文件（要 `-u` / `-a`）。
- stash 存在仓库里，不随 push 走，换机器带不走。
- `pop` 时可能与当前分支冲突 → 解决冲突后记得 `git stash drop`（pop 冲突时不会自动删）。

## 7. reflog —— Git 的「操作流水账」，最后的救命稻草

只要提交过（哪怕分支删了、reset --hard 了、rebase 崩了），本地至少 90 天内都能找回。

```bash
git reflog                         # HEAD 的移动史
git reflog show dev                # 某分支的历史
git log -g --oneline dev

# 典型救援流程
git reset --hard HEAD~3            # 手滑删了 3 个提交
git reflog                         # 找到操作前的哈希 a1b2c3d（HEAD@{4}）
git reset --hard a1b2c3d           # 全部回来
git branch 恢复分支 a1b2c3d         # 或另建分支保住它
```

其他救援：

```bash
# 找回被删分支
git reflog | grep 分支名
git checkout -b dev a1b2c3d
# 找回 dangling 提交（没被任何指针引用但还在 objects 里）
git fsck --lost-found
git show 提交的哈希
# 找回误 rebase/merge 的中断点
git rebase --abort
```

保留期配置：`git config --gc 的 reflog.expire`（默认 unreachable 90 天、过期 reflog 30 天）。**别乱敲 `git gc --prune=now`，会真的清掉找回的可能。**

## 8. 底层原理：Git 到底存了什么

### 8.1 快照，不是差异

Git 每次提交保存**所有文件的完整快照**（内容寻址存储），只在文件未变时存链接。SVN 存差异行，Git 存「照片」，所以 checkout/切换分支快、完整性校验简单。

```
A ──▶ B ──▶ C      每个提交 = 一个 commit 对象 → 指向一个 tree → 指向若干 blob
```

### 8.2 四种对象

| 对象 | 内容 | 类比 |
|---|---|---|
| blob | 一个文件的某个版本（只存内容 + 大小，不存文件名） | 文件快照 |
| tree | 目录：名字 → blob/其他 tree 的映射 | 文件夹清单 |
| commit | 一个 tree + 父提交 + 作者/提交者 + 信息 | 版本记录 |
| tag | 附注标签对象：指向 commit + 标签信息 | 书签卡片 |

哈希 = 内容的 SHA-1（Git 正在过渡到 SHA-256）。**内容变 → 哈希必变 → 历史无法篡改**，这就是 Git 完整性的来源。

### 8.3 亲手拆开看

```bash
git cat-file -t HEAD              # commit
git cat-file -p HEAD              # tree / parent / author / message
git cat-file -p HEAD^{tree}       # 这层的 tree，列出根目录
git cat-file -p HEAD:README.md    # 直接取该版本文件内容
git ls-tree -r HEAD               # 递归列出全部 blob + 哈希
git hash-object -w 文件            # 手工把文件写成 blob 并返回哈希
git count-objects -vH             # 仓库对象统计与体积
```

一个 commit 对象原文长这样：

```
tree 5b3a...c2d1
parent 7f0a...9e11
author 你 <you@x.com> 1730000000 +0800
committer 你 <you@x.com> 1730000000 +0800

feat: 支持导出 CSV
```

### 8.4 .git 目录结构

```
.git/
├── HEAD                # 当前分支指针 → ref: refs/heads/main
├── config              # 本仓库配置（remote、分支跟踪）
├── description         # GitWeb 用，可忽略
├── hooks/              # 钩子脚本（见 05 篇）
├── info/exclude        # 本地私有忽略规则
├── objects/            # 所有 blob/tree/commit，a1/b2c3... 分桶存放
├── refs/
│   ├── heads/          # 本地分支（一个分支 = 一个存哈希的小文本文件）
│   ├── remotes/        # 远程跟踪分支 origin/main
│   └── tags/           # 标签
├── index               # 暂存区（二进制，记录待提交的 tree）
├── logs/               # reflog（HEAD 与各分支的变动流水）
├── COMMIT_EDITMSG      # 上次提交信息，供 --amend/edit 复用
└── packed-refs         # 分支多了以后打包引用，加速
```

`cat .git/refs/heads/main` 会看到一个 40 位哈希 —— **这就是「分支是什么」的全部真相**。

`git update-ref refs/heads/main a1b2...` 就是一次手工「改分支指向」。

### 8.5 由原理推出的三条判断

1. 提交前必须先 `add`：因为 commit 从 **index（暂存区）** 生成 tree，工作区内容不会自动进入。
2. `git log` 走的是 commit 的 parent 链 —— 分支只是入口，所以 rebase 会「重写」历史（造新对象、改指向）。
3. 只要对象还被某个 ref 或 reflog 引用，`git gc` 就不会删 → 这就是 reflog 能救命的原因。

## 9. 事故急救合集（背下来）

| 事故 | 立即处理 |
|---|---|
| 提交信息写错 | `git commit --amend` |
| 漏了一个文件 | `git add 漏的` → `git commit --amend --no-edit` |
| 提交到了错误分支 | 在正确分支 `cherry-pick 哈希`，回错分支 `reset --hard HEAD~1` |
| `reset --hard` 删了提交 | `git reflog` 找哈希 → `git reset --hard <哈希>` |
| 删了分支 | `git reflog` / `git fsck --lost-found` |
| 把大文件提交了想删 | `git filter-repo --path 大文件 --invert-paths` + 强推（本地先备份！） |
| rebase 途中崩了 | `git rebase --abort`（回到 rebase 前，可 reflog 二次确认） |
| 误在分离头指针上提交 | `git branch 临时名`（先救住提交），再决定合到哪 |
| merge 搞乱了 main | `git merge --abort`（未提交时）／`git reset --hard ORIG_HEAD`（`ORIG_HEAD` 是合并/硬 reset 前的位置） |
| 想撤回已 push 的提交 | `git revert <哈希>` + push（公共历史，别改写） |
| stash 丢了 | `git fsck --lost-found` 或 `git stash list`，drop 掉的 stash 可用其 commit 哈希 `git show` 找回 |
| 大仓库 clone 太慢 | 重新 `git clone --depth 1`，之后 `git fetch --unshallow` |

## 10. 本章练习

1. 连续提交 3 次，分别用 `--soft` / `--mixed` / `--hard` 撤销最后一次，每次用 `git status` + `git diff --staged` 记录三者差异（这是最经典的学习实验）。
2. 制造一次「`reset --hard` 丢了两条提交」，用 reflog 完整救回。
3. 在 main 上改一半代码，切分支修 hotfix，全程用 `git stash push -u` + `pop` 不丢失任何改动。
4. 用 `git revert` 撤销一次 merge 提交（`-m 1`），解释为什么不能直接 revert 一个 merge 而不带 `-m`。
5. 用 `git cat-file -p HEAD`、`git ls-tree -r HEAD` 画出你自己仓库的 commit→tree→blob 关系（至少 2 层），并说明未改动文件在两个版本间是怎么存储的。
6. 用 `git fsck --lost-found` 找出一个 dangling commit，恢复成新分支。

---

---
# git-05 · 进阶工具与自动化

## 1. git bisect —— 二分找 bug，神器

「这功能上次还好好的，现在坏了，到底是哪次提交弄坏的？」——交给二分查找，1000 个提交只需约 10 步。

```bash
git bisect start
git bisect bad                   # 当前版本是坏的
git bisect good v1.0.0           # 这个版本是好的
# Git 自动切到中间的提交，你测试一下：
git bisect good                  # 这个版本没问题（继续往后半找）
git bisect bad                   # 这个版本已经坏了
...
# Git 输出：a1b2c3d is the first bad commit
git bisect reset                 # 回到原来的分支
```

自动化（脚本判断，无需人肉测试）：

```bash
git bisect start HEAD v1.0.0
git bisect run pytest -q tests/test_login.py     # 退出码 0 = good，非 0 = bad
git bisect run make test
git bisect run bash check.sh
```

适合配合 CI 脚本做回归定位；`check.sh` 里必须自己 `exit 0/1`。

## 2. git blame —— 追责与溯源（不是用来骂人的）

```bash
git blame src/login.py                 # 每行是谁、哪次提交、什么时候写的
git blame -L 10,20 src/login.py        # 只看第 10–20 行
git blame -w src/login.py              # 忽略空白改动
git blame -C -C -C file                # 追到代码从别的文件搬过来的地方
git blame --ignore-rev a1b2c3d file    # ⭐ 跳过某次纯格式化提交（团队应配 git-blame-ignore-revs）
git log --follow -p 文件                # 单文件完整演变
```

VS Code：装 **GitLens**，光标所在行的 blame 信息直接显示在行尾，点开可看 diff。

## 3. git worktree —— 同一仓库同时检出多个分支

场景：正在 dev 上改到一半（不能 stash，因为要跑长时间任务），线上要紧急修 main。以前只能 clone 两份，现在：

```bash
git worktree add ../hotfix-dir hotfix          # 为 hotfix 分支单开一个目录
git worktree add ../release -b release/v2.3    # 顺便新建分支
git worktree list                              # 列出所有工作树
cd ../hotfix-dir                               # 这里是独立目录，有自己的文件、可编译运行
# ...修完提交、推送...
git worktree remove ../hotfix-dir              # ⭐ 用完删掉（别 rm -rf）
git worktree prune                             # 清理已消失目录的记录
```

特点：共享同一个 `.git` 对象库（省空间），但**同一个分支不能被两个 worktree 同时检出**。适合：并行跑长任务、对比两个版本代码、多分支同时构建。

## 4. 仓库瘦身与维护

```bash
git gc                              # 手动垃圾回收与打包（日常 Git 会自动跑）
git gc --aggressive --prune=now     # 深度整理（大仓库提速；⚠️ 会清 unreachable 对象）
git count-objects -vH               # 体积与对象数
git repack -ad                      # 重打包对象，加速 clone
git prune --expire=now              # 删不可达对象（谨慎，会毁掉 reflog 救援能力）
git fsck --full                     # 完整性检查
git update-ref -d refs/heads/旧分支  # 底层删引用
```

大仓库提速：

```bash
git config --global core.bigFileThreshold 10m
git sparse-checkout set src docs            # ⭐ 只检出部分目录（monorepo 必备）
git sparse-checkout disable
git config --global fetch.negotiationAlgorithm skipping   # 加速与老旧服务器的协商
git clone --filter=blob:none url            # ⭐ 部分克隆：blob 按需下载，超大仓库秒 clone
git config --global core.preloadindex true
```

## 5. hooks —— 在提交/推送时自动做事

钩子脚本放在 `.git/hooks/`（无扩展名，需可执行权限），文件名固定：

| 时机 | 常用钩子 |
|---|---|
| 提交前（可拦截） | `pre-commit`、`prepare-commit-msg`、`commit-msg` |
| 提交后 | `post-commit` |
| 推送前（可拦截） | `pre-push` |
| 收数据时（服务器端） | `pre-receive`、`update`、`post-receive` |
| 其他 | `post-checkout`、`post-merge`、`post-rewrite` |

```bash
# .git/hooks/pre-commit（Linux/macOS 或 Git Bash）
#!/bin/sh
echo "检查代码风格..."
ruff check . || exit 1          # 非 0 退出 → 阻止本次提交
ruff format --check . || exit 1
```

Windows PowerShell 版本（`.git/hooks/pre-commit` 仍走 sh，所以用 Git Bash 语法最稳）。

```bash
chmod +x .git/hooks/pre-commit     # Linux/macOS 必须加执行权限
git config --global core.hooksPath ~/.git-hooks    # 全局钩子目录（团队统一）
git commit --no-verify                # 临时跳过钩子（尽量少用）
```

### pre-commit 框架（推荐，钩子可版本化）

`.git/hooks/` 不会随仓库分发，团队方案用 [`pre-commit`](https://pre-commit.com)：

```yaml
# .pre-commit-config.yaml
repos:
  - repo: https://github.com/astral-sh/ruff-pre-commit
    rev: v0.6.9
    hooks:
      - id: ruff
        args: [--fix]
      - id: ruff-format
  - repo: https://github.com/gitleaks/gitleaks
    rev: v8.18.4
    hooks:
      - id: gitleaks                 # 拦截密钥泄露
```

```bash
pip install pre-commit
pre-commit install                   # 装钩子（每次 clone 后跑一次）
pre-commit run --all-files           # 全量跑一遍
```

## 6. git rerere —— 重复冲突自动复用解法

rebase/cherry-pick 反复遇到同一个冲突时，Git 记住你的解法：

```bash
git config --global rerere.enabled true
```

## 7. 检索与统计

```bash
git grep -n "关键词"                    # 在版本库内容里搜（比系统 grep 快，认分支）
git grep -e "TODO" -- "*.py"            # 限定文件类型
git log --pretty=format:"%an" | sort | uniq -c | sort -rn    # 各作者提交数排行
git shortlog -sn --all                  # ⭐ 同上，简洁版
git shortlog -sn --since="3 months ago"
git log --numstat --pretty="%h" -- . | awk '{s+=$2} END {print s}'   # 加行数统计
git ls-files | wc -l                    # 纳管文件数
git diff --shortstat "@{0} day ago"     # 过去一天的改动量
git cherry -v origin/main dev           # 哪些提交还没进主干
git notes add -m "code review 备注"     # 给提交附加备注（不动原提交）
git blame --line-porcelain file | grep "^author " | sort | uniq -c | sort -rn
```

## 8. 凭据与安全

```bash
git config --global credential.helper manager      # Windows（默认）
git config --global credential.helper store        # 明文存（不推荐）
git config --global credential.helper "cache --timeout=3600"   # 内存缓存 1h
git config --global commit.gpgsign true            # 提交 GPG 签名
git config --global --add safe.directory /path     # 解决 "dubious ownership" 报错
```

Windows 上出现 `detected dubious ownership` → 因为目录属主与当前用户不同，加 `safe.directory` 或修正所有权即可。

## 9. 本章练习

1. 在练习仓库里造 8 个提交，其中第 5 个引入 bug（一个函数返回值改错）。用 `git bisect` 定位它，并统计你执行了几步。
2. 用 `git bisect run` 让 pytest 自动定位同一个 bug。
3. 用 `git blame -L` 找出自己某段代码的作者与提交，并用 `--ignore-rev` 跳过一次纯格式化提交。
4. 建一个 worktree 用于并行开发 `main` 和 `dev`，在两个目录里分别运行同一套测试，然后 `worktree remove` 清理。
5. 写一个 `pre-commit` 钩子：禁止提交信息为「update/fix/111」等无意义内容（正则判断 `commit-msg`），或改用 `pre-commit` 框架配 ruff + gitleaks。
6. 用 `git shortlog -sn` 与 `git grep` 各做一次统计与检索练习。

---

---
# git-06 · 大文件、子模块与工程最佳实践

## 1. LFS 大文件存储（Git LFS）

Git 不适合存大二进制文件：**每个历史版本都会完整保存**，仓库越用越肥，clone 越来越慢。LFS 把真实文件放到对象存储，Git 里只存一个指针文本。

适用：模型权重、图片/视频、`psd`/`unity` 资源、数据集、安装包。

```bash
# 安装：https://git-lfs.com （Windows 装完重启终端）
git lfs install                          # 全局启用一次
git lfs track "*.psd"                    # 声明类型（会写入 .gitattributes）
git lfs track "data/*.csv" "*.bin"
git add .gitattributes                   # ⭐ 必须把 .gitattributes 提交进仓库
git add model.bin && git commit -m "chore: 加入模型权重" && git push

git lfs ls-files                         # 本分支管理的 LFS 文件
git lfs status / git lfs env / git lfs migrate info    # 排查
git lfs migrate in --include-ref=refs/heads/main --include="*.bin"   # 把历史里的大文件迁到 LFS
git lfs fetch --all                      # 拉全所有版本的 LFS 内容
GIT_LFS_SKIP_SMUDGE=1 git clone url      # clone 时跳过下载大文件（只要指针，快）
```

GitHub LFS 免费额度有限（1GB 存储 / 1GB 月带宽），别把 LFS 当网盘用。

**大文件已进历史的补救**（GitHub 会拒绝 push 超过 100MB 的文件）：

```bash
git lfs migrate import --include="*.bin" --everything    # 自动重写历史并转 LFS
git push --force-with-lease
# 或用 git filter-repo 直接删掉
```

## 2. 子模块 submodule —— 仓库套仓库

子模块在主仓库里**只记一个 commit 哈希**，不复制内容。适合引用第三方库、内部公共库、把大项目拆分。

```bash
git clone --recursive url                # 连子模块一起拉
git submodule update --init --recursive  # ⭐ 已有仓库补装子模块（clone 后必做）

git submodule add https://github.com/user/lib libs/mylib
git commit -m "chore: 引入 mylib 子模块"

cd libs/mylib
git pull origin main                     # 子模块内部像普通仓库一样操作
cd ../..
git add libs/mylib                       # 主仓库更新记录的哈希
git commit -m "chore: 升级 mylib 到 abc123"

git submodule foreach "git checkout main"
git submodule update --remote            # 拉每个子模块的最新提交
git submodule deinit -f libs/mylib
git rm -f libs/mylib                     # 移除子模块
```

痛点：忘记 `--recursive`、别人拉下来是空目录、子模块停在分离头指针导致提交丢失。
**能用包管理器（pip/npm/cargo）就别用 submodule。**

## 3. subtree（另一种合并方式）

```bash
git subtree add --prefix=lib https://github.com/user/lib main --squash
git subtree pull --prefix=lib https://github.com/user/lib main --squash   # 同步上游
git subtree push --prefix=lib 自己的fork main                            # 把子目录推出去
```

subtree 把内容**真实合并**进主仓库，clone 不需要额外命令，代价是仓库更大、命令更重。

## 4. monorepo / 多分支策略实践

- 单仓库多项目：`packages/a`、`packages/b` + `git sparse-checkout` + `git worktree`。
- 长期并存分支：`main`（已发布）、`develop`（集成）、`release/*`、`feature/*`、`hotfix/*`。
- 每个 PR 只做一件事，控制在 200–400 行 diff 内 —— 审查质量和合并速度都会显著提升。

## 5. 工程最佳实践清单

**每天**

- `git status` 看清位置再动手，绝不「不知道自己在哪个分支就提交」。
- 小步提交：一次提交一个可说明的意图；碎提交比巨型提交好（可以 squash，反之很难拆）。
- 提交前 `git diff --staged`，防止把 `console.log`、调试打印、密钥提交上去。
- 切分支前工作区必须干净（commit / stash / worktree 三选一）。

**命名与规范**

- 分支：`feature/xxx`、`fix/xxx`、`release/vX.Y`、`hotfix/xxx`。
- 提交：Conventional Commits（`feat|fix|docs|refactor|test|chore(scope): 描述`），一行动词开头、不超过 50 字、结尾不加句号。
- 标签：`v1.2.3` 附注标签，语义化版本。

**安全红线**

- 公共分支（main/develop）永不 `rebase` / `reset --hard` / `push --force`。要推错就用 `--force-with-lease` 且通知团队。
- 密钥、口令、token、`.env`、用户数据一律不入库；被 GitHub 的 Secret Scanning 报警立即撤销密钥。
- 大文件（>10MB）先想 LFS 或外部存储；`git filter-repo` 改写历史前**必须先镜像备份仓库**（`git clone --mirror`）。
- 不要把 `.git` 目录本身复制给同事，用 `git bundle create x.bundle --all` 传离线包：`git pull x.bundle main`。

**性能与体量**

```bash
git clone --depth 1 ...             # CI 用浅克隆
git sparse-checkout set ...         # monorepo 只取所需目录
git -c core.fscache=1 ...           # Windows 提速
du -sh .git                         # 关注体积增长
git verify-pack -v .git/objects/pack/*.idx | sort -k3 -n | tail -5   # 找最大的对象
```

**备份与迁移**

```bash
git clone --mirror url backup.git   # 完整镜像备份（含所有分支/标签）
git bundle create 项目-2026-11-04.bundle --all    # 单文件离线备份
git remote set-url origin 新地址 && git push --all && git push --tags
```

## 6. 常见事故速查表

| 症状 | 根因 | 解法 |
|---|---|---|
| `git push` 被拒绝 non-fast-forward | 远程有新提交 | `git pull --rebase` 后再 push，别硬来 |
| `error: cannot lock ref` / `index.lock` 存在 | 上次操作崩溃/IDE 并发 | `rm -f .git/index.lock`（确认无 Git 进程在跑） |
| 提交后找不到文件 | 只 `git add` 了别的/未跟踪 | `git status`、`git log --stat -1` |
| 中文文件名显示成 `\344\270\255` | 未关 quotepath | `git config --global core.quotepath false` |
| 换行符全变 `^M`，整个文件显示已改 | CRLF/LF 冲突 | `core.autocrlf true` + `.gitattributes` 加 `* text=auto` |
| `detected dubious ownership` | 目录属主与用户不符 | `git config --global --add safe.directory 路径` |
| `.gitignore` 加了却还在跟踪 | 已提交过 | `git rm --cached -r 目录` 后提交 |
| push 太慢 / 仓库 3GB | 大文件或所有历史 blob | LFS / `--depth 1` / `filter-repo` 清理 |
| 别人拉下来子模块是空的 | 没用 `--recursive` | `git submodule update --init --recursive` |
| `Your local changes will be overwritten by checkout` | 有未提交改动与新分支冲突 | `git stash push -u` → 切分支 → `git stash pop` |
| 误在 main 直接改了线上代码 | 未开分支 | `git switch -c hotfix/xxx`（改动跟着走）→ 提交 → PR |
| HEAD 变成 (no branch, detached) | 检出了 commit/标签 | `git switch main`；若已提交先 `git branch 保住它` |
| `git pull` 报 `Need to specify how to reconcile` | 未设 pull 策略 | `git config --global pull.rebase true` |
| 提交作者显示错误邮箱 | 仓库级 config 不对 | `git log --format='%an %ae'` 查；`--author` 或 `git filter-repo --mailmap` 修 |
| GitHub 显示 commit 不是我的 | 邮箱与账号不匹配 | 账号里加该邮箱；或 `git config user.email` 换成已验证邮箱 |

## 7. 学完之后的路线

```
熟练 20 个核心命令（本库内容）
  → 团队 PR / Code Review 流程
  → 读懂 CI/CD（Actions / GitLab CI，本质是被 Git 事件触发）
  → 服务器裸仓库 + webhook 自动部署
  → 大型仓库治理（LFS、monorepo、CODEOWNERS、branch protection）
  → 进阶原理（packfile、reachability、hash 迁移到 SHA-256）
```

---

---
# git-99 · Git 速查表

> 忘了就查这页。适合打印或贴在第二个标签页。

## 1. 按「我要做什么」查命令

### 起步

```bash
git init / git clone URL / git clone --depth 1 URL
git remote -v / git remote add origin URL / git remote set-url origin URL
git push -u origin main                # 首次推送并建立跟踪
git config --global user.name/.user.email
```

### 日常循环

```bash
git status            git diff            git add 文件或 .
git commit -m "..."   git commit -am "..."(只含已跟踪)   git commit --amend
git pull --rebase     git push
```

### 查看历史与差异

```bash
git log --oneline --graph --decorate --all     # 建议别名 git lg
git log -p 文件 / --follow / -S"代码" / -L 10,20:文件 / --author --since
git show HEAD / HEAD~2 / abc123:路径
git diff / --staged / HEAD / 分支A 分支B / --stat / --name-only
git blame -L 10,20 文件
git grep -n 关键词
git shortlog -sn --all         # 提交量排行
```

### 分支

```bash
git branch -a / -v / -vv / --show-current
git switch 分支 / switch -c 新分支 / switch -
git branch -d/-D 分支
git merge 分支 / --no-ff / --abort / --squash
git rebase 分支 / -i HEAD~3 / --continue / --abort
git cherry-pick 哈希 / -n / --abort
git push origin --delete 分支
git tag -a v1.0 -m "..." / push --follow-tags / tag -d
```

### 撤销与恢复 ⭐

```bash
git restore 文件                    # 丢弃工作区改动
git restore --staged 文件            # 取消 add
git revert HEAD / 哈希 / -m 1 合并哈希   # 安全撤销（公共分支用）
git reset --soft/--mixed/--hard HEAD~1
git clean -nd  →  git clean -fd     # 删未跟踪（先预览）
git stash push -u -m "说明" / list / show -p / pop / apply / branch
git reflog                          # 救命：找回任何提交过的东西
git reset --hard 哈希               # 配合 reflog 完成救援
git fsck --lost-found               # 找悬空提交
```

### 远程与同步

```bash
git fetch --all --prune
git log --oneline HEAD..origin/main        # 我落后了什么
git log --oneline origin/main..HEAD        # 我领先了什么
git rev-list --left-right --count origin/main...HEAD    # "落后 领先"
git push --force-with-lease                # 唯一可接受的强推方式
git branch --set-upstream-to=origin/main
```

### 进阶

```bash
git bisect start / bad / good v1.0 / run pytest -q / reset
git worktree add ../dir 分支 / list / remove ../dir / prune
git submodule update --init --recursive
git lfs track "*.bin" && git add .gitattributes
git add -p                        # 交互式分块暂存（拆提交神器）
git gc / git count-objects -vH / git fsck --full
git sparse-checkout set src docs
git clone --filter=blob:none URL
git bundle create x.bundle --all  /  git clone x.bundle
git clone --mirror URL backup.git
```

## 2. 状态码含义（git status -s）

```
 M 已改未暂存   M␣ 已暂存   A 新增   ?? 未跟踪
 D 删除          MM 暂存后又改了
 UU 双方改动冲突  AA 双方新增冲突  DU 我方删对方改  UD 我方改对方删
```

## 3. 推荐别名与全局配置（一次配好）

```ini
[user]
    name = 你的名字
    email = you@example.com
[core]
    editor = code --wait
    autocrlf = true            # Windows；macOS/Linux 用 input
    quotepath = false          # 中文不乱码
    pager = less -FRX
[init]
    defaultBranch = main
[pull]
    rebase = true
[push]
    default = current
    autoSetupRemote = true
[fetch]
    prune = true
[rebase]
    autoStash = true
[merge]
    ff = false
[diff]
    algorithm = histogram
    colorMoved = plain
[status]
    showUntrackedFiles = all
[rerere]
    enabled = true
[alias]
    st = status -sb
    ci = commit -v
    co = checkout
    br = branch -vv
    lg = log --oneline --graph --decorate --all
    la = log --oneline --graph --decorate
    unstage = reset HEAD --
    last = log -1 HEAD --stat
    fix = commit --amend --no-edit
    undo = reset --soft HEAD~1
    wtf = "!f(){ git st; git lg -10; };f"
    ignore = "!f(){ echo \"$1\" >> .gitignore; };f"
```

写入方法：`git config --global <key> <value>`，或直接编辑 `~/.gitconfig`。

## 4. 提交信息规范（Conventional Commits）

```
feat     新功能          fix       修 bug
docs     文档            style     格式（不改逻辑）
refactor 重构            perf      性能
test     测试            build     构建/依赖
ci       流水线          chore     杂项
revert   回滚

<type>(<scope>): <subject>     ← 50 字内，动词开头，句尾不加句号

body：为什么改 + 关键取舍（不是复述 diff）

footer：Closes #123 / BREAKING CHANGE: ...
```

## 5. .gitignore 语法

```gitignore
*.log            # 扩展名
build/           # 目录（含所有层级）
/temp            # 仅根目录
docs/*.tmp       # 路径通配
!important.md    # 取反（须先有一条能匹配它的规则）
**/node_modules/ # 任意深度
```

```bash
git check-ignore -v 文件      # 哪条规则挡住了它
git rm --cached -r 目录        # 停止跟踪但保留本地文件
# 个人杂物 → .git/info/exclude 或 ~/.gitignore（core.excludesFile）
```

## 6. 报错 → 解法对照

| 报错 | 原因 | 处理 |
|---|---|---|
| `Updates were rejected because the remote contains work` | 别人先推了 | `git pull --rebase` 再 push |
| `fatal: not a git repository` | 不在仓库里 | `cd` 到仓库或 `git init` |
| `Unable to create '.git/index.lock'` | 上次中断/IDE 占用 | 确认无进程后删掉 `index.lock` |
| `error: Your local changes would be overwritten` | 未提交改动冲突 | `git stash push -u` → 操作 → `pop` |
| `HEAD detached at xxx` | 检出了 commit/标签 | `git switch main`（有提交先 `git branch 名字` 保住） |
| `fatal: refusing to merge unrelated histories` | 两边历史无关（常见于远程有 README） | `git pull origin main --allow-unrelated-histories` |
| `error: pathspec ... did not match any file` | 文件名/分支名写错 | `git status`、`git branch -a` 核对 |
| `dubious ownership` | 目录属主不同 | `git config --global --add safe.directory 路径` |
| `Please tell me who you are` | 未配 user.name/email | 配 global |
| ``Permission denied (publickey)`` | SSH key 没加/不对 | `ssh -T git@github.com`，重生成或改用 HTTPS+token |
| `Failed to connect to github.com port 443` | 网络/代理 | 换网络、配 `http.proxy`，或用 Gitee |
| `fatal: sha1 file ... write error (disk full)` | 磁盘满 | 清理磁盘 |
| `too large file (限制 100 MB)` | 大文件入库 | 用 Git LFS / `git filter-repo` 清除 |
| `Merge conflict in ...` | 真冲突 | 编辑文件去掉标记 → `git add` → `git commit` |
| `could not apply ...` (rebase) | rebase 冲突 | 解决 → `git add` → `git rebase --continue`；放弃用 `--abort` |
| `empty commit` (rebase/cherry-pick) | 该改动已存在 | `git rebase --skip` / `git cherry-pick --skip` |

## 7. 一图看懂 reset 三模式

```
                 动分支指针   动暂存区   动工作区
--soft              ✅          ❌         ❌      ← 改提交信息/合并碎提交
--mixed(默认)        ✅          ✅         ❌      ← 退回 add 前，改动保留
--hard              ✅          ✅         ✅      ← 彻底抹掉改动 ⚠️（reflog 可救提交）
```

## 8. merge vs rebase 决策

```
同步主干到我的未推送功能分支        → git rebase origin/main
把功能分支并入团队主干（PR）        → git merge（常配 --no-ff 或平台 squash）
公共分支 / 已推送且别人拉过的历史    → 只 merge，禁止 rebase
只要某一次提交                     → git cherry-pick
提交太碎想整理                     → git rebase -i（squash / fixup / reword / drop）
```

## 9. 冲突标记模板

```
<<<<<<< HEAD        ← 你当前分支（rebase 时「HEAD」是正在应用的目标基，语义会反转，注意）
你的内容
=======
对方内容
>>>>>>> 分支名或提交号
```

解决三步：删标记留下正确代码 → `git add` → `git commit`（或 `--continue`）。
批量策略：`git checkout --ours 文件` / `--theirs 文件` / `git mergetool`。

## 10. 黄金律（贴在显示器上）

1. 动手前先 `git status`，确认自己在哪个分支、有什么改动。
2. **公共分支不改写历史**（no rebase / no reset / no `--force`）。
3. 强推只用 `--force-with-lease`。
4. 破坏性命令（`clean -f`、`reset --hard`、`stash drop`）执行前先 `git branch 备份点` 或 `git stash`。
5. 忘记的提交去 `git reflog` 找，别重写代码。
6. `.gitignore` 对已跟踪文件无效，要 `git rm --cached`。
7. 每次提交只做一件事，信息按规范写；碎提交可以 squash，混在一起的大提交拆不开。
8. 密码/token 一旦 push 就视为泄露：先撤销密钥，再谈清理历史。
9. 改写历史前先 `git clone --mirror` 备份。
10. 不确定时：`git fetch` + 只读命令（`log`/`diff`/`show`）观察，永远比猜着敲安全。

## 11. 新手 30 分钟上手路径

```bash
mkdir git-lab && cd git-lab && git init
git config user.name "我" && git config user.email "me@x.com"
echo "# 练功房" > README.md && git add . && git commit -m "chore: init"
git switch -c dev ; echo "x" >> a.txt ; git add . ; git commit -m "feat: a"
git switch main ; echo "y" >> README.md ; git commit -am "docs: 补充"
git merge dev                      # 观察是否快进
echo "冲突" >> README.md           # 在 dev 上也改同一行，制造冲突
git merge dev                      # 解决冲突 → add → commit
git log --oneline --graph --all    # 看你的作品
git reset --soft HEAD~1 && git commit -m "chore: 体验 soft 撤销"
git reflog                         # 看到刚才所有的指针移动
```

跑完这一遍，你对 Git 的理解会超过 80% 只会点按钮的人。

---