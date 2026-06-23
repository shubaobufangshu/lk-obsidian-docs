# Git 钩子完全指南：从原理到实战，手把手带你玩转自动化流程

Git 钩子（Git Hooks）是 Git 提供的一套强大机制，允许你在特定事件发生时自动触发自定义脚本。它们就像是埋藏在 Git 操作流程中的“触发器”，让你能在提交前、推送前、合并后等关键节点执行检查、格式化、通知等任务。本教程将一次性把 Git 钩子的方方面面都讲透，让你看完就能上手。

---

## 一、钩子是什么？为什么你需要它？

简单说，**Git 钩子就是在 Git 工作流中某个特定动作之前或之后自动执行的脚本**。它们完全运行在你的本地环境或远程仓库所在的服务器上，不会被版本控制跟踪（默认情况下），所以每个克隆仓库的人都需要单独配置。

通过钩子，你可以实现：

- **代码质量把关**：提交前自动运行 lint、单元测试。
- **提交规范约束**：强制检查 commit message 格式（如 Conventional Commits）。
- **工作流自动化**：合并后自动安装依赖、生成文档。
- **安全管控**：防止敏感信息（密码、密钥）被意外提交。
- **通知与日志**：推送成功后自动发送消息、生成 changelog。

钩子本质上就是普通的可执行文件（Shell、Python、Ruby、Node.js 脚本等），Git 只是“在正确的时间”去调用它们。

---

## 二、快速认识钩子：存储位置与生命周期

### 1. 钩子文件放在哪里？
每个 Git 仓库都有一个 `.git/hooks/` 目录。初始化仓库时，Git 会在这个目录里放入一些示例脚本（后缀为 `.sample`）。

```bash
ls .git/hooks/
# 输出示例：
# applypatch-msg.sample      pre-push.sample
# commit-msg.sample          pre-rebase.sample
# fsmonitor-watchman.sample  prepare-commit-msg.sample
# post-update.sample         update.sample
# pre-applypatch.sample
# pre-commit.sample
```

**要启用一个钩子，只需：**
- 去掉 `.sample` 后缀（或新建一个无后缀的文件）
- 赋予可执行权限：`chmod +x 钩子名`

### 2. 客户端钩子 vs 服务器端钩子
- **客户端钩子**：运行在你的本地仓库，由提交、合并、推送等操作触发。比如 `pre-commit`、`commit-msg`、`pre-push`。它们只影响本地开发行为，无法强制其他协作者执行（除非每个人都配置）。
- **服务器端钩子**：运行在远程仓库（如 GitLab、GitHub Enterprise 托管服务器）上，在网络操作（如推送）时触发。比如 `pre-receive`、`update`、`post-receive`。它们用于强制实施全团队策略，拒绝不合规的推送。

---

## 三、所有钩子一览：触发时机与用途

### 客户端钩子全列表

| 钩子名                                 | 触发时机                                       | 典型用途                              |
| ----------------------------------- | ------------------------------------------ | --------------------------------- |
| `pre-commit`                        | 在 `git commit` 输入提交信息之前                    | 代码风格检查、运行单元测试、检查是否包含调试语句          |
| `prepare-commit-msg`                | 在启动提交信息编辑器之前，创建了默认信息后                      | 自动生成或修改提交信息模板，如添加分支名、任务编号         |
| `commit-msg`                        | 用户编辑完提交信息后，提交完成前                           | 校验提交信息格式，拒绝不合规的提交                 |
| `post-commit`                       | 整个提交过程完成后                                  | 通知、记录日志、触发本地构建                    |
| `pre-rebase`                        | 执行 `git rebase` 之前                         | 防止对保护分支进行变基                       |
| `post-checkout`                     | `git checkout` 或 `git switch` 后            | 根据分支切换自动安装依赖、还原文件                 |
| `post-merge`                        | 合并成功后                                      | 合并后自动执行依赖更新、权限修复                  |
| `pre-push`                          | 推送前，远程引用更新后                                | 运行完整测试套件，阻止未通过测试的推送               |
| `post-rewrite`                      | 被 `git commit --amend`、`git rebase` 等改写历史后 | 更新日志、重新生成文档                       |
| `pre-auto-gc`                       | `git gc --auto` 执行前                        | 禁止自动垃圾回收                          |
| `post-receive` (其实是客户端/服务器两用，本地也可用) | 如果本地仓库收到推送，这里特指`hooks`目录下也是同名，但通常指服务器端     | 本地可使用 `post-receive` 处理工作树更新（不常见） |

> 注：`post-applypatch`、`pre-applypatch` 等与邮箱补丁流程相关，日常使用较少，略过。

### 服务器端钩子（在远程裸仓库上）

| 钩子名 | 触发时机 | 用途 |
|--------|----------|------|
| `pre-receive` | 接收到推送，在所有 ref 更新前 | 全仓库级检查，如文件大小、权限策略 |
| `update` | 每个 ref 被推送更新前（每个分支单独触发） | 分支级权限控制，防止 force push 特定分支 |
| `post-receive` | 所有 ref 更新完成后 | 发送邮件、触发 CI、部署 |

---

## 四、手把手写钩子：从 Hello World 开始

### 1. 创建第一个钩子：pre-commit
进入你的项目仓库，创建文件 `.git/hooks/pre-commit`：

```bash
#!/bin/bash
echo "你好，这个钩子在提交前执行！"
exit 0
```

给它执行权限：
```bash
chmod +x .git/hooks/pre-commit
```

现在执行一次提交：
```bash
git commit -m "test"
```
你会看到“你好，这个钩子在提交前执行！”出现在终端。说明钩子已经生效。

### 2. 钩子的退出码决定操作是否继续
- **退出码 0**：成功，Git 继续执行后续操作。
- **退出码 非零**（通常是 1）：失败，Git 中止当前操作。比如 `pre-commit` 返回非 0，提交就会失败。

因此，你可以在脚本末尾根据检查结果 `exit 0` 或 `exit 1`。

### 3. 钩子如何接收参数？
有些钩子在执行时，Git 会传递参数给它们。比如：
- `pre-commit`：无参数
- `commit-msg`：接收一个参数，即提交信息文件的路径
- `post-checkout`：接收三个参数：之前的 HEAD 引用、新的 HEAD 引用、是否为分支切换（1 为分支，0 为文件检出）

你可以通过 `$1`、`$2` 等在脚本中获取。

---

## 五、核心钩子深度解析与实战示例

下面我们逐一详细讲解最常用的钩子，并提供可直接使用的脚本范例。

### 5.1 `pre-commit` —— 提交前的最后防线

**触发**：输入 `git commit` 后，编辑器弹出前。
**参数**：无。
**核心用法**：检查暂存区中将要提交的内容，不是工作区未暂存的修改。

#### 示例 1：禁止提交包含 `console.log` 的 JavaScript 文件
```bash
#!/bin/bash
# 获取本次提交中所有新增或修改的 js 文件
files=$(git diff --cached --name-only --diff-filter=ACM | grep '\.js$')
if [ -n "$files" ]; then
    # 逐文件检查是否包含 console.log（未注释的）
    for file in $files; do
        if grep -n "console.log" "$file"; then
            echo "❌ 提交失败：$file 中包含 console.log，请移除或注释后重试。"
            exit 1
        fi
    done
fi
exit 0
```

> **注意**：`git diff --cached` 只检查暂存区内容，避免检查到工作区尚未暂存的修改。

#### 示例 2：结合 lint-staged 运行 ESLint（需要 Node 环境）
```bash
#!/bin/bash
# 仅当安装了 eslint 且目录有 .eslintrc 时运行
if [ -f node_modules/.bin/eslint ]; then
    # 只 lint 暂存区的 js 文件
    STAGED_JS=$(git diff --cached --name-only --diff-filter=ACM | grep '\.js$')
    if [ -n "$STAGED_JS" ]; then
        echo "Running ESLint on staged files..."
        echo "$STAGED_JS" | xargs node_modules/.bin/eslint
        if [ $? -ne 0 ]; then
            echo "❌ ESLint 检查不通过，提交中止。"
            exit 1
        fi
    fi
fi
exit 0
```

### 5.2 `commit-msg` —— 确保提交信息规范

**触发**：在用户编辑完提交信息后，提交生效前。
**参数**：`$1` 为包含提交信息的临时文件路径。
**核心用法**：读取该文件内容，用正则校验格式。

#### 示例：强制 Conventional Commits 格式
```bash
#!/bin/bash
# 要求格式：type(scope): subject
# type: feat, fix, docs, style, refactor, test, chore
commit_msg_file=$1
commit_msg=$(cat "$commit_msg_file")

# 常规提交格式正则
pattern="^(feat|fix|docs|style|refactor|test|chore|perf|ci)(\(.+\))?: .{1,50}"
if ! echo "$commit_msg" | grep -qE "$pattern"; then
    echo "❌ 提交信息格式错误，请遵循 Conventional Commits 规范。"
    echo "正确示例：feat(用户模块): 添加登录功能"
    exit 1
fi
exit 0
```

### 5.3 `pre-push` —— 推送前的终极检查

**触发**：`git push` 时，在本地数据发送到远程之前。
**参数**：通过标准输入提供两行：`<本地 ref> <本地 SHA1> <远程 ref> <远程 SHA1>`，每个要推送的引用一行。
**核心用法**：运行完整测试套件，阻止推送上包含失败测试的分支。

#### 示例：推送前运行单元测试
```bash
#!/bin/bash
# 读取推送的引用信息
while read local_ref local_sha remote_ref remote_sha; do
    # 只有在推送当前分支时才运行测试（可定制）
    if [ "$remote_ref" = "refs/heads/main" ] || [ "$remote_ref" = "refs/heads/master" ]; then
        echo "检测到向 main/master 推送，运行测试..."
        npm test
        if [ $? -ne 0 ]; then
            echo "❌ 测试未通过，推送取消。"
            exit 1
        fi
    fi
done
exit 0
```

> 需要理解标准输入循环：`while read ...` 逐个处理推送的引用。

### 5.4 `prepare-commit-msg` —— 自动填充提交信息模板

**触发**：默认提交信息生成后，编辑器启动前。
**参数**：`$1` 提交信息文件路径，`$2` 提交来源（merge/template/commit/message/squash），`$3` 额外 SHA（合并时的源提交）。
**核心用法**：根据当前分支名自动添加前缀。

#### 示例：自动将分支名加入提交信息
```bash
#!/bin/bash
COMMIT_MSG_FILE=$1
COMMIT_SOURCE=$2

# 只在普通提交时修改（不处理合并、模板、amend）
if [ -z "$COMMIT_SOURCE" ] || [ "$COMMIT_SOURCE" = "message" ]; then
    BRANCH_NAME=$(git rev-parse --abbrev-ref HEAD)
    # 排除 HEAD 游离状态
    if [ "$BRANCH_NAME" != "HEAD" ]; then
        # 提取分支名中的任务编号，如 feature/PROJ-123-xxx 中的 PROJ-123
        TICKET=$(echo "$BRANCH_NAME" | grep -oE '[A-Z]+-[0-9]+' | head -1)
        if [ -n "$TICKET" ]; then
            # 在提交信息前添加 "[PROJ-123] "
            sed -i.bak "1s/^/[$TICKET] /" "$COMMIT_MSG_FILE"
        fi
    fi
fi
exit 0
```

### 5.5 `post-checkout` —— 切换分支后的环境准备

**触发**：`git checkout` 或 `git switch` 之后（工作树已更新）。
**参数**：`$1` 之前的 HEAD，`$2` 新的 HEAD，`$3` 是否分支切换（1 表示切换分支，0 表示文件检出）。
**核心用法**：根据分支不同自动安装依赖、还原数据库、显示待办事项。

#### 示例：切换到特定分支自动运行 `npm install`
```bash
#!/bin/bash
PREV_HEAD=$1
NEW_HEAD=$2
BRANCH_SWITCH=$3

if [ "$BRANCH_SWITCH" = "1" ]; then
    NEW_BRANCH=$(git rev-parse --abbrev-ref HEAD)
    echo "切换到了分支：$NEW_BRANCH"
    if [ -f package.json ] && ! diff <(git show "$PREV_HEAD:package.json" 2>/dev/null) package.json >/dev/null; then
        echo "package.json 已变更，自动安装依赖..."
        npm install
    fi
fi
```

### 5.6 `post-merge` —— 合并后自动执行任务

**触发**：`git merge` 成功完成后。
**参数**：`$1` 是否为压缩合并（1 表示是）。
**核心用法**：自动更新子模块、重新编译资源。

```bash
#!/bin/bash
# 合并后如果 package.json 有变动就安装依赖
if git diff HEAD@{1} --name-only | grep -q "package.json"; then
    echo "合并后 package.json 发生了变化，运行 npm install..."
    npm install
fi
```

---

## 六、服务器端钩子：全团队策略的强制执行器

当你管理一个中央仓库（裸仓库，如通过 `git init --bare` 创建的），可以在服务器上的 `hooks/` 目录放置脚本。这些钩子在每次推送时由 Git 服务端调用（注意：GitHub、GitLab 等托管平台不直接暴露这些钩子文件，它们提供自己的 Webhook 或集成方式，但自建的 Git 服务器完全支持）。

### 6.1 `pre-receive` —— 全引用级检查

**触发**：客户端推送，服务端接收数据后，在更新任何引用之前。
**输入**：标准输入，格式 `<old-sha> <new-sha> <ref-name>`，每行一个引用。
**用途**：全局限制，如禁止推送超过 100MB 的文件，或强制所有提交者邮箱必须为公司邮箱。

```bash
#!/bin/bash
while read oldrev newrev refname; do
    # 检查该分支上所有新增提交的作者邮箱
    for commit in $(git rev-list $oldrev..$newrev); do
        author_email=$(git log -1 --pretty=format:'%ae' $commit)
        if ! echo "$author_email" | grep -q "@mycompany.com$"; then
            echo "❌ 拒绝推送：提交 $commit 的作者邮箱为 $author_email，必须使用公司邮箱。"
            exit 1
        fi
    done
done
exit 0
```

### 6.2 `update` —— 每个分支单独检查

**触发**：每个被推送的引用更新时分别调用一次。
**参数**：`$1` 引用名（如 `refs/heads/main`），`$2` 旧对象 SHA，`$3` 新对象 SHA。
**用途**：精细控制，如禁止 force push 到 main 分支。

```bash
#!/bin/bash
refname=$1
oldrev=$2
newrev=$3

if [ "$refname" = "refs/heads/main" ] || [ "$refname" = "refs/heads/master" ]; then
    # 检查是否是非快进式推送（force push）
    if [ "$oldrev" != "0000000000000000000000000000000000000000" ]; then
        # 如果不是新分支创建，检查能否快进
        if ! git merge-base --is-ancestor "$oldrev" "$newrev"; then
            echo "❌ 禁止强制推送到 $refname 分支。"
            exit 1
        fi
    fi
fi
exit 0
```

### 6.3 `post-receive` —— 收工后的自动化

**触发**：所有引用成功更新后。
**输入**：与 `pre-receive` 相同，标准输入每行 `<old> <new> <ref>`。
**用途**：部署到生产环境、发送通知、触发 CI 构建。典型 Git 部署流程即基于此钩子。

```bash
#!/bin/bash
while read oldrev newrev refname; do
    if [ "$refname" = "refs/heads/main" ]; then
        echo "主分支已更新，开始部署..."
        GIT_WORK_TREE=/var/www/myapp git checkout -f main
        cd /var/www/myapp && npm install && pm2 restart app
        echo "部署完成。"
    fi
done
```

---

## 七、如何在团队中共享钩子？

`.git/hooks` 目录不受版本控制，所以团队成员不会自动获得你编写的钩子。解决方案：

### 7.1 使用 `core.hooksPath` 改变钩子目录（Git 2.9+）
把所有钩子放在仓库的一个可跟踪目录中（如 `.githooks`），然后让每个成员运行：
```bash
git config core.hooksPath .githooks
```
也可以在 README 中说明，或提供初始化脚本。

### 7.2 使用 Git 模板目录
创建一个模板目录，内含 `hooks/`，然后用 `git init --template=模板路径` 或设置 `init.templateDir` 配置，让新克隆的仓库自动带上钩子。（只对新仓库有效，已存在的需手动复制）

### 7.3 借助现代工具（最推荐）
- **Husky**（Node.js 生态）：在 `package.json` 中配置，安装时自动设置 Git 钩子。
- **lefthook**（多语言）：快速、跨平台，支持命令行及 YAML 配置。
- **pre-commit** 框架：基于 `.pre-commit-config.yaml`，管理 lint 等钩子，无需手写 Shell。
- **GitHub Actions / GitLab CI/CD**：代替部分服务器端钩子的功能，更灵活强大。

---

## 八、调试钩子：脚本不工作怎么办？

### 1. 确认脚本有执行权限
```bash
chmod +x .git/hooks/your-hook
```

### 2. 避免使用导致环境差异的命令
钩子运行时，环境变量与你的终端可能不同。特别是 `PATH` 可能不完整。在脚本开头加上：
```bash
PATH=$PATH:/usr/local/bin:/usr/bin:/bin
```
或者使用绝对路径调用程序，如 `/usr/local/bin/node`。

### 3. 输出调试信息
在钩子中用 `echo` 或 `echo >&2` 输出到终端，这些信息会在 Git 操作时显示。如果是 GUI 工具，请查看它们的控制台或日志。

### 4. 模拟钩子执行环境
手动传入参数测试：
```bash
.git/hooks/commit-msg .git/COMMIT_EDITMSG
```
对于 `pre-receive`，可以用管道模拟输入：
```bash
echo "oldsha newsha refs/heads/main" | .git/hooks/pre-receive
```

### 5. 利用 `set -x` 追踪
在 bash 脚本顶部加入 `set -x`，会在执行时打印每一行命令，便于定位问题。

---

## 九、注意事项与常见陷阱

- **钩子会阻断操作**：`pre-commit`、`commit-msg` 等非零退出会直接中止提交，可能导致丢失已输入的提交信息。Git 会将信息保存在 `.git/COMMIT_EDITMSG`，可以恢复。
- **不要滥用在生产关键路径**：避免在 `pre-push` 中执行耗时过长的测试，否则会严重影响开发体验。考虑异步或后台作业。
- **跨平台兼容性**：Shell 脚本在 Windows 下默认无法运行。要么使用跨平台工具（Husky、lefthook），要么要求使用 Git Bash 或 WSL。
- **不能依赖钩子保证绝对安全**：客户端钩子可被 `--no-verify` 参数跳过：
  ```bash
  git commit --no-verify
  git push --no-verify
  ```
  需要强制策略时，必须使用服务器端钩子或远程仓库平台的 Webhook/CI 检查。

- **权限问题**：服务器端钩子脚本必须以 Git 服务运行用户的身份可执行。

---

## 十、总结与最佳实践

Git 钩子可以提升开发质量、规范流程的利器。建议按以下原则使用：

1. **客户端用于轻量级快速反馈**：pre-commit 运行 linter，commit-msg 检查格式。
2. **服务器端用于强制团队策略**：禁止大文件、邮箱校验、分支保护。
3. **用专业工具管理共享钩子**，避免手动维护 `.git/hooks`。
4. **保持钩子脚本简短、高效**，避免阻塞提交流。
5. **务必提供 `--no-verify` 的应急方案说明**，防止关键时刻被阻断。