# 培训课第一课笔记：Linux、Vim & Shell、Git & GitHub

> **课程**：27 赛季控制视觉招新培训 · 第一课
> **主线**：从 Linux 到第一次 Pull Request
> **目标**：在一个真实目录里练习 Linux 基础操作，最后把修改提交给团队

| 环节 | 内容 | 时长 |
| :--- | :--- | ---: |
| 01 | 进入 Linux | 20 min |
| 02 | 终端操作 | 25 min |
| 03 | Vim 编辑 | — |
| 04 | Git 提交 | 35 min |
| 05 | GitHub PR | — |
| — | 总结与答疑 | 10 min |

**目录**

- [一、Linux 基础](#一linux-基础)
- [二、Shell 的四种常用组合](#二shell-的四种常用组合)
- [三、Vim：在终端中编辑文件](#三vim在终端中编辑文件)
- [四、Git：本地版本控制](#四giti本地版本控制)
- [五、GitHub：远程托管与协作](#五github远程托管与协作)
- [六、命令速查表](#六命令速查表)

---

## 一、Linux 基础

### 1.1 为什么用 Linux

视觉代码最终要跑在机器人上的小电脑里，那台机器装的是 Linux。它轻量、开源、可裁剪，没有图形界面也能跑，
这也是为什么团队要花时间学命令行，而不是继续留在 Windows 的图形界面里。

### 1.2 目录结构：从 `/` 开始

Linux 没有 C 盘、D 盘的概念，所有东西都挂在唯一的一棵树下，树根就是 `/`（根目录）。

| 路径 | 用途 |
| :--- | :--- |
| `/` | 根目录，所有目录的起点 |
| `/home/user` | 当前用户的家目录，个人文件都放这里 |
| `/etc` | 系统配置文件 |
| `/usr` | 程序与库 |
| `/var` | 日志等可变数据 |
| `/dev` | 设备文件 |

> **路径大小写敏感**。`Hello.txt` 和 `hello.txt` 是两个不同的文件，这一点和 Windows 不一样，写错大小写就会「找不到文件」。

### 1.3 绝对路径与相对路径

```text
/home/user/example     ← 绝对路径：从 / 开始
./hello.txt            ← 相对路径：从当前位置开始
```

三个特殊符号必须记住：

| 符号 | 含义 |
| :--- | :--- |
| `~` | 当前用户的家目录，即 `/home/user` |
| `.` | 当前目录 |
| `..` | 上一级目录 |

所以 `~/example` 表示「家目录下的 example」，等价于 `/home/user/example`。

### 1.4 终端、Shell 与命令的关系

这三者经常被混为一谈，其实是三层：

```text
你敲键盘
   ↓
Terminal 终端        →  输入输出界面
   ↓
Shell（Bash / Zsh）  →  解释命令、展开路径、组织执行
   ↓
程序 / 系统           →  ls 查看文件、git 管理版本
```

在终端输入命令，由 Shell 解释，再交给程序执行。`ls`、`cd`、`git` 本身都是程序，Shell 只负责把你要做什么翻译给系统听。

### 1.5 文件与目录操作

| 命令 | 作用 | 示例 |
| :--- | :--- | :--- |
| `pwd` | 显示当前目录 | `pwd` |
| `ls` | 列出目录内容 | `ls -la` |
| `cd` | 进入指定目录 | `cd ~/linux_demo` |
| `cd ..` | 返回上一级 | `cd ..` |
| `cd ~` | 返回家目录 | `cd ~` |
| `mkdir -p` | 创建目录，允许补齐父目录 | `mkdir -p ~/linux_demo/notes` |
| `touch` | 创建空文件 / 更新时间 | `touch hello.txt` |
| `cp` | 复制文件 | `cp hello.txt hello-copy.txt` |
| `mv` | 移动或重命名 | `mv hello-copy.txt backup.txt` |
| `rm` | 删除文件 | `rm backup.txt` |
| `cat` / `less` | 直接查看 / 分页查看 | `cat hello.txt` |

> ⚠️ **`rm` 通常不进回收站**，删掉就是删掉了。执行前先 `pwd` 确认当前位置、`ls` 确认文件名，
> 尤其不要在 `/` 或家目录下随手 `rm -rf`。

### 1.6 陌生命令的帮助入口

「先查用途与参数，再在练习目录中尝试」，这是学命令行最重要的习惯。

| 方式 | 说明 |
| :--- | :--- |
| `ls --help` | 快速查看选项和用法 |
| `man ls` | 打开手册，按 `q` 退出 |
| `Tab` | 补全命令或路径 |
| `↑` / `↓` | 调出历史命令 |

`Tab` 补全能省掉大量打字和拼写错误，`↑` 能直接复用上一条命令改一改再跑，这两个是日常使用频率最高的技巧。

---

## 二、Shell 的四种常用组合

单条命令能力有限，真正好用的是把它们组合起来。

| 组合 | 写法 | 作用 |
| :--- | :--- | :--- |
| **变量** | `name="RM"; echo "$name"` | 保存一个值，再通过变量引用它 |
| **重定向** | `echo "hello" > hello.txt` | `>` 覆盖写入；`>>` 追加到文件末尾 |
| **管道** | `ls -la \| grep txt` | 将输出交给下一个程序 |
| **条件连接** | `mkdir -p demo && cd demo` | 前一条成功后，再执行下一条 |

### 2.1 重定向

```bash
echo "hello" >  hello.txt    # 覆盖写入：文件原有内容会被清空
echo "world" >> hello.txt    # 追加写入：写在文件末尾，之前的内容保留
```

对比 `>` 和 `>>` 是这节课的重点演示之一。**`>` 会清空原文件**，脚本里如果写错成 `>`，之前的数据就没了。

除了标准输出，还有标准错误和输入：

```bash
command 2> error.log         # 只要错误信息
command &> all.log           # 正确输出 + 错误一起
command < input.txt          # 从文件读入
```

### 2.2 管道

管道 `|` 把前一条命令的输出，直接当成下一条命令的输入，数据在内存里传递，不落地成文件。

```bash
ls -la | grep txt            # 只保留名字里含 txt 的行
cat hello.txt | wc -l        # 数一数有多少行
ps aux | grep python         # 找进程
```

### 2.3 条件连接

```bash
mkdir -p demo && cd demo     # && ：前一条成功才执行后一条
mkdir demo || echo "失败"     # || ：前一条失败才执行后一条
```

`&&` 在脚本里非常常见，因为它天然实现了「出错就停」的效果。

### 2.4 脚本的几个基本要素

```bash
#!/usr/bin/env bash        # shebang：告诉系统用哪个解释器运行
set -euo pipefail          # 严格模式：出错即停

"$0"                       # 脚本自身的路径
"$(dirname "$0")"          # 脚本所在目录，用来定位同目录的其它文件
```

定位「脚本同路径下的文件」时，一定要用 `$(dirname "$0")` 拼路径，而不是写死相对路径——
否则脚本换个地方运行就会找不到文件。

---

## 三、Vim：在终端中编辑文件

Vim 常用于 SSH 连接和无桌面环境中的配置、代码编辑。它**没有菜单**，靠模式切换来操作，所以新手第一周会觉得难受，
但熟练之后手指不用离开主键区，改配置的速度远快于鼠标点来点去。

### 3.1 三种模式

```text
        i（插入）
普通模式 ─────────→ 插入模式
   ↑  ←─────────
   │    Esc
   │ 按 : 
   ↓
命令行输入（末行模式）
```

| 模式 | 作用 |
| :--- | :--- |
| 普通模式 | 移动、删除、复制（打开文件时的默认模式） |
| 插入模式 | 输入文本 |
| 命令行模式 | 保存、退出 |

### 3.2 模式与保存退出

| 命令 | 作用 |
| :--- | :--- |
| `i` | 进入插入模式 |
| `Esc` | 返回普通模式 |
| `:w` | 保存 |
| `:q` | 退出 |
| `:wq` | 保存并退出 |
| `:q!` | 放弃修改，强制退出 |

> 卡住的时候先连按几下 `Esc`，确保自己在普通模式，再按 `:` 敲命令。
> 想放弃所有改动就直接 `:q!`。

### 3.3 普通模式常用按键

| 按键 | 作用 |
| :--- | :--- |
| `h` `j` `k` `l` | 左 / 下 / 上 / 右 |
| `dd` | 删除当前行 |
| `yy` / `p` | 复制当前行 / 粘贴 |
| `u` | 撤销 |
| `/word` | 搜索 word |

`dd`、`yy`、`u` 是最高频的三个，配合数字还能批量操作，例如 `10dd` 删除 10 行。

### 3.4 六步上手流程

| 步骤 | 操作 | 说明 |
| :--- | :--- | :--- |
| 01 | `vim hello.txt` | 打开练习文件 |
| 02 | `i` | 进入插入模式，输入文本 |
| 03 | `Esc` | 返回普通模式 |
| 04 | `:wq` | 保存并退出 |
| 05 | `vim hello.txt` | 再次打开文件 |
| 06 | `dd` `u` `/word` | 删除一行、撤销、搜索 |

---

## 四、Git：本地版本控制

### 4.1 Git 与 GitHub 的分工

多人修改同一个项目时，用版本记录和评审代替互传压缩包。

| | Git | GitHub |
| :--- | :--- | :--- |
| 定位 | 本地版本控制 | 远程托管与协作 |
| 职责 1 | 记录每次提交 | 托管 Git 仓库 |
| 职责 2 | 比较修改、查看历史 | 通过 PR 讨论和评审 |
| 职责 3 | 为新功能创建分支 | 合并团队认可的修改 |

一句话区分：**Git 是工具，装在你电脑上；GitHub 是网站，存在云端。**

### 4.2 Git 的四个位置

```text
工作区 ──git add──→ 暂存区 ──git commit──→ 本地仓库 ──git push──→ GitHub 远程仓库
正在编辑的文件      本次提交的内容清单      已记录的提交历史        远程保存的提交历史
```

> **`commit` 只记录到本地仓库，`push` 才把提交发送到远程。**
> 所以本地提交完不等于队友能看到，必须再 push 一次。

### 4.3 首次使用：提交作者

姓名与邮箱会写入每一条提交记录，回答「这次修改由谁提交」。

```bash
git config --global user.name  "Your Name"
git config --global user.email "you@example.com"

git config --get user.name        # 查询是否设置成功
```

不配置的话 `git commit` 会直接报错：

```text
*** Please tell me who you are.
fatal: unable to auto-detect email address
```

### 4.4 SSH 认证：公钥与私钥

SSH 用来证明你有权访问远程仓库，不用每次输密码。

```bash
ssh-keygen -t ed25519 -C "you@example.com"    # 生成密钥对
cat ~/.ssh/id_ed25519.pub                     # 查看公钥
```

| 文件 | 用途 |
| :--- | :--- |
| `id_ed25519.pub`（公钥） | 复制到 GitHub 的 SSH 设置 |
| `id_ed25519`（私钥） | **不上传、不截图、不分享**，只留在自己电脑上 |

把公钥加到 GitHub：

1. `Settings`
2. `SSH and GPG keys`
3. `New SSH key` → 选择 `Authentication Key`，填写名称，粘贴完整公钥
4. 验证：`ssh -T git@github.com`（首次连接先核对官方主机指纹）

### 4.5 常用命令

**获取与查看**

| 命令 | 作用 |
| :--- | :--- |
| `git clone <仓库地址>` | 下载仓库及提交历史 |
| `git status` | 查看当前分支和文件状态 |
| `git diff` | 查看尚未暂存的修改 |
| `git log --oneline` | 用简短格式查看提交历史 |

**分支与提交**

| 命令 | 作用 |
| :--- | :--- |
| `git switch -c feature/hello` | 创建并切换到功能分支 |
| `git add hello.txt` | 暂存指定文件 |
| `git commit -m "Add greeting"` | 记录一次本地提交 |
| `git push -u origin feature/hello` | 推送并建立跟踪关系 |
| `git switch main` | 切回主分支 |
| `git pull` | 获取并整合当前分支的远程更新 |

### 4.6 分支：为功能保留独立开发线

```text
main          ●────────●────────●────────●
                       \               /
feature/hello           ●────●────●──
                                    PR / Review / Merge
```

`main` 保持稳定，`feature/hello` 承载本次修改，评审后再合并。分支让同一仓库中的开发互不打扰，提交点记录每一步修改。

> **在功能分支上操作、提交和推送时，切换分支前先看 `git status`。**
> 带着未提交的改动切分支，很容易把改动带到不该去的地方。

---

## 五、GitHub：远程托管与协作

### 5.1 团队 PR 工作流（Fork 协作）

默认使用 Fork 协作：远程复制仓库，本地开发，再向团队发起合并请求。

```text
团队仓库 ──fork──→ 个人 Fork ──clone──→ 本地电脑
                                          │
                                    Feature 分支
                                          │
                                   修改 / Add / Commit
                                          │
                                        Push
                                          ↓
团队仓库 ←── Review 评审 ←── 发起 PR ←── 个人 Fork 接收提交
   │
 Merge 到团队仓库
```

> `Fork` 是仓库副本，`Branch` 是仓库内的开发线，两者不是一回事：
> **Fork 复制的是整个仓库，Branch 复制的是某一条时间线。**
> 有团队仓库推送权限时可以省略 Fork，直接用同仓库分支协作。

### 5.2 完整步骤

1. **Fork** 目标仓库到自己账号下
2. **Clone** 到本地：`git clone git@github.com:<你的用户名>/missing-semester.git`
3. 把团队仓库设为 upstream，方便后续同步
4. **新建分支**（不要在 `master` 上直接改）：`git switch -c feature/xxx`
5. 修改文件 → `git add` → `git commit` → `git push`
6. 在 GitHub 上向**目标仓库**发起 **PR**
7. 关注 PR 的 comments，按评审意见继续改，改完再次 push，PR 会自动更新

### 5.3 PR 规范

| 项目 | 要求 |
| :--- | :--- |
| **Commit 消息** | 英文，**首字母大写，结尾带句号**，例如 `Add notes and homework script.` |
| **PR 标题** | 英文，首字母大写 |
| **PR 描述** | 可以用中文，但要清晰说明这个 PR 提交了什么 |

写好一个 `.gitignore` 也需要留意：编译产物、`build/` 目录、本地配置不应该进仓库。

### 5.4 关于 Conflict

后续课程继续用这个仓库交作业，`TASK.md` 会更新，所以 PR 可能出现 Conflict。

> 正确处理：把上游更新合并进自己的分支（`git pull upstream master` 或 `git merge`），
> 手动解决冲突后提交，PR 会自动刷新。
> **不要用「关掉 PR 再重新开一个」的方式规避 Conflict**，这会被扣分。
> PR 的标题、描述、目标分支本来就可以随时修改，新提交也会自动同步到 PR，不需要重开。

---

## 六、命令速查表

```bash
# ── 目录与文件 ──────────────────────────────
pwd                     # 我在哪
ls -la                  # 看有什么（含隐藏文件）
cd ~/linux_demo         # 进目录
mkdir -p a/b/c          # 建目录（自动补父目录）
touch hello.txt         # 建空文件
cp a.txt b.txt          # 复制
mv a.txt b.txt          # 移动 / 重命名
rm a.txt                # 删除（不进回收站！）
cat hello.txt           # 看内容
less hello.txt          # 分页看，q 退出

# ── Shell 组合 ─────────────────────────────
name="RM"; echo "$name"       # 变量
echo hi >  f.txt              # 覆盖
echo hi >> f.txt              # 追加
ls -la | grep txt             # 管道
mkdir -p demo && cd demo      # 条件连接

# ── Vim ────────────────────────────────────
vim hello.txt    # 打开
i                # 插入
Esc              # 回普通模式
:wq              # 保存退出
:q!              # 放弃退出
dd               # 删一行
u                # 撤销
/word            # 搜索

# ── Git ────────────────────────────────────
git config --global user.name  "Name"
git config --global user.email "you@example.com"
ssh-keygen -t ed25519 -C "you@example.com"
git clone git@github.com:user/repo.git
git switch -c feature/hello     # 新建分支
git status                      # 看状态
git diff                        # 看改动
git add hello.txt               # 暂存
git commit -m "Add greeting."   # 提交（首字母大写带句号）
git push -u origin feature/hello
git switch main
git pull
git log --oneline
```

---

## 七、这次作业我踩到的 / 值得记的点

- `>` 和 `>>` 差一个字符，一个清空一个追加，写脚本时最容易错。
- Windows 上编辑的 `.sh` 是 CRLF 换行，拿到 Linux 上跑会报 `bad interpreter: /bin/bash^M`。
  用 `git config --global core.autocrlf input` 让仓库里统一存 LF，可以避免这个问题。
- 脚本里定位同目录文件要用 `$(dirname "$0")`，写死相对路径换个目录运行就崩。
- `history` 只在交互式 Shell 里有内容，脚本里拿不到，需要读 `~/.bash_history`。
- 就算已经开了 PR，后续的提交也会自动追加到 PR 上，所以**不需要**关掉重开。
