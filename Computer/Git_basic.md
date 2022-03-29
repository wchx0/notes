# Git

## 说明

本文主要基于廖雪峰老师的博客总结改编而成，原文请参见 [Git教程 - 廖雪峰的官方网站](https://www.liaoxuefeng.com/wiki/896043488029600) 。

## Git 简介

Git是一个极为先进的开源的分布式版本管理系统。它是 Linus Torvalds （没错，就是Linux之父）为了帮助管理Linux内核开发而开发的。

## 准备工作

### 安装配置

1. 安装Git。

2. 设置用户名称和电子邮箱：
   
   ```bash
   $ git config --global user.name "Your Name"
   $ git config --global user.email "Your Email"
   ```

### 创建版本库

版本库又名仓库 (repository) ，是一个由 Git 管理的目录，其中每个文件的修改、删除，Git都能跟踪，以便在任何时刻追踪历史，或者在将来某个时刻回溯。

1. 选择一个空目录（以下用 *`WorkPath/`* 代指该目录的路径）,切换到该目录（用 `cd` 命令），用 `git init` 命令将这个目录初始化：
   
   ```bash
   $ git init
   ```
   
   此时 *`WorkPath/`* 下会多一个 `.git/` 目录，不要动它。
+ *如果使用Windows系统，为了避免莫名其妙的问题，请确保目录名（包括父目录）不包含中文。*

### 添加文件到仓库

1. 在 *`WorkPath/`* 或其子目录下创建一个文本文件，如 `README.md` 。

2. 用命令 `git add` 把文件添加到仓库：
   
   ```bash
   $ git add README.md
   ```

3. 用命令 `git commit` 把文件提交到仓库：
   
   ```bash
   $ git commit -m "wrote a readme file"
   ```
   
   `-m` 后面输入的是本次提交的说明，描述本次提交修改的内容，便于从历史记录里找到改动记录。
+ *所有的版本管理系统都只能追踪文本文件，虽然可以管理二进制文件但是无法追踪其变化。*

+ *Git命令都要在Git仓库目录内执行。*

## 开始工作

### 修改

1. 修改文件，如 `README.md` 。

2. 可随时用命令 `git status` 查看状态。

3. 用命令 `git diff` 查看文件前后差异。

4. 用命令 `git add` 提交修改。

5. 用命令 `git commit` 提交至仓库。

### 版本回退

1. 用 `git log` 命令查看历史记录（加 `--pretty=oneline` 参数，显示摘要信息）。
   
   输出信息中，可以看到文件的版本号 `commit id` ，它是由 SHA1 计算出来的十六进制数字。
   
   + *这因为Git是分布式的版本控制系统，需要多人在同一个版本库里工作，如果全用1，2，3……作为版本号，会引发冲突。*

2. 用 `git reset` 命令回退到历史版本。
   
   `HEAD` 表示当前版本， `HEAD^` 表示上一个版本， `HEAD^^` 表示上上个版本，`HEAD～n` 表示前n个版本。例如，要回到上个版本：
   
   ```bash
   $ git reset --hard HEAD^
   ```
   
   + *此时我们再用命令 `git log` 查看历史记录会发现最新的版本变为了我们回退的版本，而更新的版本都不见了。*

3. 撤销回退
   
   <u>找到之前最新的版本的 `commit id` </u> ，用 `git reset` 命令回退：
   
   ```bash
   $ git reset --hard <commit_id>
   ```
   
   + *`commit id` 不需要写全，只要输前几位（一般4～5位即可），足够Git确定唯一的版本即可。*
   
   当然，关键在于找到之前最新的版本的 `commit id` ：
   
   1. 如果命令行尚未关闭，前滚找到之前输出的 `commit id` 即可。
   
   2. 如果命令行已经关闭，用命令 `git reflog` 查看历史输入命令，找到最后一次修改时的 `commit id` 即可。
+ Git版本回退原理：
  
  Git在内部有个指向当前版本的 `HEAD` 指针，回退版本时，Git把 `HEAD` 从指向 `version3` ：
  
  ```asciidoc
  ┌────┐
  │HEAD│
  └────┘
     └──> ○ version3
          │
          ○ version2
          │
          ○ version1
  ```
  
  改为指向 `version2` ：
  
  ```asciidoc
  ┌────┐
  │HEAD│
  └────┘
     │    ○ version3
     │    │
     └──> ○ version2
          │
          ○ version1
  ```
  
  同时更新工作区的文件。

### 工作区与暂存区

1. 工作区（Working Directory）
   
   即工作目录 `WorkPath` 。

2. 版本库（Repository）
   
   工作区的隐藏目录 `.git` 是Git的版本库。
   
   Git的版本库里存了很多东西，其中最重要的就是称为stage（或者叫index）的暂存区，还有Git为我们自动创建的第一个分支 `master` ，以及指向 `master` 的一个指针叫 `HEAD` 。
   
   将文件提交到Git版本库中是分两步执行的：
   
   1. 用 `git add` 把文件添加进版本库，实际上就是把文件修改添加到暂存区；
   
   2. 用 `git commit` 提交更改，实际上就是把暂存区的所有内容提交到当前分支。
      
      *在创建Git版本库时，Git自动创建了唯一一个 `master` 分支，所以，目前， `git commit` 就是往 `master` 分支上提交更改。*
+ 提交修改时过程如图：
  
  ```asciidoc
                                       Repository 
                               __________________________
                               |                        |
                     git add   |      git commit        |
  Working Directory ----------> stage ----------> master
          |__________________________________________|
                           git diff
  ```

### 撤销修改

1. 用命令 `git checkout -- <file>` 丢弃工作区的修改：
   
   ```bash
   $ git checkout -- README.md
   ```
   
   命令 `git checkout -- README.md` 意思就是，把 `README.md` 文件在工作区的修改全部撤销，这里有两种情况：
   
   1. `readme.txt` 自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态；
   
   2. `readme.txt` 已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。
   
   总之，就是让这个文件回到最近一次 `git commit` 或 `git add` 时的状态。

2. 用命令 `git reset HEAD <file>` 可以把暂存区的修改撤销掉（unstage），重新放回工作区：
   
   ```bash
   $ git reset HEAD README.md
   ```

### 删除文件

1. 用 `rm` 命令（在文件管理器中）将文件删除：
   
   ```bash
   $ rm README.md
   ```
   
   1. 若确实要删除，用命令 `git rm <file>` 或 `git add <file>` 删除，再用 `git commit` 提交修改即可。
   
   2. 若为误删，用命令 `git checkout -- <file>` ，即可恢复。
      
      *`git checkout` 其实就是用版本库里的版本替换工作区的版本。*

## 远程仓库

终于来了，远程仓库！

### Github or Gitee

Github 是 Microsoft 旗下的代码托管平台，许多著名的开源项目都由它托管，但国内访问太慢；Gitee 是开源中国推出的代码托管服务，在国内访问较快。所以。。。以下直接记载如何同时使用 Github 和 Gitee。

1. 创建 Github 和 Gitee 账户。

2. 创建 SSH Key。
   
   在用户主目录下，看有没有 `.ssh/` 目录，如果有，再看看这个目录下有没有 `id_rsa` 和 `id_rsa.pub` 这两个文件，如果有，可直接跳过此步；如果没有，打开 Shell （Windows 下打开 Git Bash），创建 SSH Key：
   
   ```bash
   $ ssh-keygen -t rsa -C "<youremail>"
   ```
   
   把 `youremail` 换成自己的邮件地址，路径默认，可以输入密码（输入密码的话，之后每次 `push` `pull` 都要输密码）。
   
   然后，可以在用户主目录里找到 `.ssh` 目录，里面有 `id_rsa` 和 `id_rsa.pub` 两个文件，这两个就是 SSH Key 的秘钥对， `id_rsa` 是私钥，不能泄露出去； `id_rsa.pub` 是公钥，可以公开。

3. 登陆 GitHub 以及 Gitee，打开设置，找到 SSH Keys：
   
   选择添加新的 SSH Key，填上任意 Title ，在 Key 文本框里粘贴 `id_rsa.pub` 文件里的内容。

### 添加远程库

1. 登陆 GitHub，点击创建一个新的仓库（Create a new repo）：
   
   在 Repository name 填入仓库名，其他保持默认设置，点击 Create repository 按钮，就成功地创建了一个新的 Git 仓库。

2. 在本地的仓库下分别运行以下命令：
   
   ```bash
   $ git remote add github git@github.com:<github_user>/<repo>.git
   
   $ git remote add gitee git@gitee.com:<gitee_user>/<repo>.git
   ```
   
   其中 分别用两个账户的用户名代替， 用仓库名代替。
   
   添加后，远程库的名字就分别是 `github` 和 `gitee` ，也可以改成别的（*改 `add` 后面的名字，但因为有多个远程库，我们需要用不同的名称来标识不同的远程库。*），但是这两个名字容易分辨出两个远程仓库。

3. 用命令 `git remote -v` 查看远程库信息：
   
   ```bash
   $ git remote -v
   github    git@github.com:<github_user>/<repo>.git (fetch)
   github    git@github.com:<github_user>/<repo>.git (push)
   gitee     git@gitee.com:<gitee_user>/<repo>.git (fetch)
   gitee     git@gitee.com:<gitee_user>/<repo>.git (push)
   ```

4. 可以把本地库的内容推送到远程库上：
   
   ```bash
   $ git push -u github master
   
   $ git push -u gitee master
   ```
   
   把本地库的内容推送到远程，用 `git push` 命令，实际上是把当前分支 `master` 推送到远程。
   
   *由于远程库是空的，第一次推送 `master` 分支时，加上 `-u` 参数，Git不但会把本地的 `master` 分支内容推送到远程新的 `master` 分支，还会把本地的 `master` 分支和远程的 `master` 分支关联起来，在以后的推送或者拉取时可以简化命令。
   
   + <mark>SSH 警告</mark>
     
     当你第一次使用 Git 的 `clone` 或者 `push` 命令连接 GitHub (或 Gitee) 时，会得到一个警告：
     
     ```bash
     The authenticity of host 'github.com (xx.xx.xx.xx)' can't be established.
     RSA key fingerprint is xx.xx.xx.xx.xx.
     Are you sure you want to continue connecting (yes/no/[Fingerprint])?
     ```
     
     这是因为Git使用SSH连接，而SSH连接在第一次验证GitHub服务器的Key时，需要你确认GitHub的Key的指纹信息是否真的来自GitHub的服务器，输入 `yes` 、回车即可。
     
     Git会输出一个警告，告诉你已经把GitHub的Key添加到本机的一个信任列表里了：
     
     ```bash
     Warning: Permanently added 'github.com' (RSA) to the list of known hosts.
     ```
     
     这个警告只会出现一次，后面的操作就不会有任何警告了。

5. 之后可以用命令：

```bash
$ git push github master

$ git push gitee master
```

把本地 `master` 分支的最新修改推送至 GitHub 和 Gitee。

6. 此外，可以使用 Gitee 的仓库镜像管理实现 Github 和 Gitee 的双向镜像同步。
   
   详情请参见：[如何更优雅地同步 Gitee 和 GitHub 的代码仓库？ &#8211; Gitee 官方博客](https://blog.gitee.com/2021/07/15/repo-mirror/)

### 删除远程库

如果添加的时地址写错，或者想删除远程库，可以用 `git remote rm <name>` 命令。使用前，建议先用 `git remote -v` 查看远程库信息：

```bash
$ git remote -v
github    git@github.com:<github_user>/<repo>.git (fetch)
github    git@github.com:<github_user>/<repo>.git (push)
gitee     git@gitee.com:<gitee_user>/<repo>.git (fetch)
gitee     git@gitee.com:<gitee_user>/<repo>.git (push)
```

然后，根据名字删除，比如删除 `github`：

```bash
$ git remote rm github
```

此处的“删除”其实是解除了本地和远程的绑定关系，并不是物理上删除了远程库。远程库本身并没有任何改动。要真正删除远程库，需要登录到GitHub，在后台页面找到删除按钮再删除。

### 参与开源项目

如果想要参与从开源项目，可如下操作：

1. 访问项目主页，点击 “Fork” 克隆仓库到自己账户上。

2. 然后就自己的工作往自己的仓库推送。

3. 发起 "pull request" ，然后官方库会选择是否采用你的修改。

## 分支管理

### 创建和合并分支

假如我们已经将自己的项目放在远程仓库并开源了，但是现在需要修改项目中的功能，但是无法在短时间内完成。当完成了一部分时：如果提交这时其他人从仓库获得的项目将是不完整的，甚至完全无法使用；如果不提交，则面临修改的内容丢失的风险。所以，这时候，分支就出现了。

+ 分支管理图解
  
  `HEAD` 一开始指向 `master` :
  
  ```asciidoc
  ┌────┐
  │HEAD│ ──> master ──> ○ v2
  └────┘                │
                        ○ v1
  ```
  
  此时，创建新分区 `dev` ，同时把 `HEAD` 指针移向 `dev` :
  
  ```asciidoc
                                         ┌────┐
             master ──> ○ v2 <── dev <── │HEAD│
                        |                └────┘
                        ○ v1
  ```
  
  此后，我们可以移动 `dev` 的指针（`master` 指针不移动）：
  
  ```asciidoc
                                         ┌────┐
             master ──> ○ v3 <── dev <── │HEAD│
                        |                └────┘
                        ○ v2
                        |
                        ○ v1
  ```
  
  之后，当 `dev` 分支完善了，我们可以把 `master` 的指针移到 `dev` 指针当前所指位置来修改 `master` 分支（这个过程称为合并）：
  
  ```asciidoc
  ┌────┐
  │HEAD│ ──> master ──> ○ v3 <── dev
  └────┘                │
                        ○ v2
                        │
                        ○ v1
  ```
  
  最后，可以将 `dev` 分支删除，就只剩下一个分支了：
  
  ```asciidoc
  ┌────┐
  │HEAD│ ──> master ──> ○ v3
  └────┘                │
                        ○ v2
                        │
                        ○ v1
  ```

#### 步骤

1. 先创建 `dev` 分支，并切换到 `dev` 分支：
   
   ```bash
   $ git checkout -b dev
   ```
   
   `git checkout` 命令加上 `-b` 参数表示创建并切换，相当于以下两条命令：
   
   ```bash
   $ git branch dev
   ```
   
   $ git checkout dev

```
2. 用命令 `git branch` 查看当前分支：

```bash
$ git branch
* dev
  master
```

   `git branch` 命令会列出所有分支，当前分支前面会标一个 `*` 号。

3. 对 `dev` 的操作与对 `master` 分支的操作类似。

4. `dev` 分支的工作完成，切换回 `master` 分支：
   
   ```bash
   $ git checkout master
   ```
   
   *切换回 `master` 分支后，`dev` 分支上做的修改不见了， 及`master` 分支此刻的提交点并没有变。*

5. 用命令 `git merge` 把 `dev` 分支的工作成果合并到 `master` 分支上：
   
   ```bash
   $ git merge dev
   Updating d46f35e..b17d20e
   Fast-forward
   ······
   ······
   ```
   
   合并后，`master` 分支和 `dev` 分支的最新提交变为一样的。
   
   *注意到上面的 `Fast-forward` 信息，它指出这次合并是“快进模式”，也就是直接把 `master` 指向 `dev` 的当前提交，合并速度非常快。*
   
   *不是每次合并都能 `Fast-forward` ，之后介绍其他方式的合并。*

6. 合并完成后，可以删除 `dev` 分支：
   
   ```bash
   $ git branch -d dev
   ```
   
   删除后，查看 `branch` ：
   
   ```bash
   $ git branch
   * master
   ```
   
   *因为创建、合并和删除分支非常快，所以 Git 鼓励你使用分支完成某个任务，合并后再删掉分支，这和直接在 `master` 分支上工作效果是一样的，但过程更安全。*
+ ### switch
  
  切换分支使用 `git checkout <branch>` ，而撤销修改则是 `git checkout -- <file>` ，同一个命令两种作用，确实有点令人迷惑。
  
  实际上，切换分支用 `switch` 更科学：较新版本的 Git 提供了 `git switch` 命令来切换分支：
  
  + 创建并切换到新的 `dev` 分支使用：
    
    ```bash
    $ git switch -c dev
    ```
  
  + 切换到已有的 `master` 分支，可以使用：
    
    ```bash
    $ git switch master
    ```

### 解决冲突

当 `master` 分支和 `dev` 分支各自都分别有新的提交：

```asciidoc
┌────┐                       
│HEAD│ ──> master ──> v3.1 ──┐ ┌── v3.2 <── dev
└────┘                        ○ v2
                              │
                              ○ v1
```

合并分支时，Git 无法执行“快速合并”，只能试图把各自的修改合并起来，但这种合并可能会有冲突。此时，需要先手动解决冲突后再提交。

1. 用命令 `git status` 查看冲突的文件。

2. 直接查看文件内容（Git 用 `<<<<<<<` ， `=======` ， `>>>>>>>` 标记出不同分支的内容），修改并提交。

3. 可以用 `git log [--graph]` 可以看到分支的合并情况。

### 管理策略

Git 尽可能会以 `Fast forward` 模式合并，但此模式下，删除分支后，会丢掉分支信息。

若要强制禁用 `Fast forward` 模式，Git 就会在合并时生成一个新的commit，这样，从分支历史上可以看出分支信息。

1. 使用带参数 `--no-ff` 的 `git merge` 命令：
   
   ```bash
   $ git merge --no-ff -m "merge with no-ff" dev
   ```
   
   *本次合并要创建一个新的commit，所以加上 `-m` 参数，把commit描述写进去。*

2. 合并后，可以用 `git log` 查看分支历史。

#### 开发原则

1. `master`分支应该是非常稳定的，仅用来发布新版本，不随意使用；

2. 修改、测试都在 `dev` 分支进行。`dev` 分支是不稳定的，需要发布新版本时才把它合并到 `master`；

3. 多人开发时，每个人使用自己的分支，并不时地往 `dev` 合并。

### Bug 分区

当正在 `dev` 上进行的工作还没有提交，却需要修复代号为007的 bug 的时候，可以使用 `stash` 功能。

1. 使用命令 `git stash` 将当前为提交的修改隐藏起来。

2. 创建分区 `issue-007` 来修复 bug，提交、合并（建议使用带参数 `--no-ff` 的 `git merge` 命令） 。

3. 使用命令 `git switch` 切换至 `dev` 分支。

4. 使用命令 `git stash list` 列出所有隐藏工作区。

5. 使用命令 `git stash pop` 恢复工作区并把 `stash` 内容删掉。
   
   此命令相当于以下两条命令：
   
   ```bash
   $ git stash apply
   
   $ git stash drop
   ```
   
   + 多次 stash 后，恢复的时候，先用 `git stash list` 查看，然后恢复指定的 stash （n 用指定的数字替代）：
     
     ```bash
     $ git stash apply stash@{n}
     ```
+ 复制特定提交到其它分区
  
  在 `master` 分支上修复了 bug 后，若 `dev` 分区同样存在此 bug ，则可以把提交到 `master` 的修改复制到 `dev` 分支（只复制一个提交，不是把 `master` 合并过来）。
  
  1. 使用命令 `git cherry-pick <commit>` 复制一个特定的提交到当前分支，如：
  
  ```bash
  $ git cherry-pick 4c805e2
  ```
  
  *两个提交只是改动相同，但其实是两个不同的commit。*

### 多人协作

1. 用 `git push <repo> <branch-name>` 推送自己的修改。
   
   `<repo>` 为之前设置的仓库名，默认为 `origin` ，上文设置为 `github` 和 `gitee` 。

2. 若推送失败，是因为远程分支比你的本地更新，需要先用 `git pull` 试图合并。
   
   + 第一次使用 `git pull` 命令前需要先指定本地 `dev` 分支与远程 `origin/dev` 分支的链接。使用如下命令设置 `dev` 和 `origin/dev` 的链接：
     
     ```bash
     $ git branch --set-upstream-to=origin/dev dev
     ```

3. 如果合并有冲突，则解决冲突，并在本地提交；

4. 没有冲突或者解决掉冲突后，再用 `git push <repo> <branch-name>` 推送即可。

### Rebase

1. 使用命令 `git log` 发现很多分叉，难看。

2. 可以尝试使用 `git rebase` 整理。。。

## 标签管理

当我们需要找到某一个 commit ，用它的 id 太麻烦了，可以给特定的 commit 打上标签（tag）。

1. 切换到需要打标签的分支。

2. 使用命令 `git tag <name>` 给最新提交打标签。
   
   使用命令`git tag <name> <commit id>` 给特定提交打标签。
   
   + 可以创建带有说明的标签，用 `-a` 指定标签名，`-m` 指定说明文字：
     
     ```bash
     $ git tag -a v0.1 -m "version 0.1 released"
     ```

3. 使用命令 `git tag` 列出所有标签（字母顺序）。

4. 用命令 `git show <tagname>` 查看说明文字。

*<u>**标签总是和某个commit挂钩。如果这个commit既出现在master分支，又出现在dev分支，那么在这两个分支上都可以看到这个标签。**</u>*

## 自定义 Git

Git 支持定制。

例如，用以下命令使 Git 显示颜色：

```bash
$ git config --global color.ui tru
```

### 忽略特殊文件

可以配置 `.gitignore` 文件来忽略某些放在 Git 工作目录但却不需要或不想提交的文件。

GitHub 提供了各种配置文件，只需要组合使用即可。所有配置文件可以直接在线浏览：[Github ".gitignore"配置文件](https://github.com/github/gitignore)

忽略文件的原则：

1. 忽略操作系统自动生成的文件，比如缩略图等；
2. 忽略编译生成的中间文件、可执行文件等；
3. 忽略带有敏感信息的配置文件，比如存放口令的配置文件。

以下是一个示例：

```asciidoc
# Systerm
Desktop.ini

# Python
*.py[cod]

# My configurations
*.png
```

可能因为文件被 `.gitignore` 忽略而无法添加到 Git：

```bash
$ git add lib.pyc
The following paths are ignored by one of your .gitignore files:
lib.pyc
Use -f if you really want to add them.
```

+ 可以使用 `-f` 强制添加到 Git：
  
  ```bash
  $ git add -f lib.pyc
  ```

+ 可以用 `git check-ignore` 命令检查：
  
  ```bash
  $ git check-ignore -v lib.pyc
  .gitignore:5:*.py[cod]    lib.pyc
  ```
  
  Git 指出 `.gitignore` 的第5行规则忽略了该文件。
  
  此时，可以编写例外规则：
  
  ```asciidoc
  # 不排除lib.pyc
  !lib.pyc
  ```
  
  把指定文件排除在`.gitignore`规则外的写法就是`!`+文件名。

### Git 别名

1. 使用命令 `git config [--global] alias.<别名> "<命令名>"` 设置别名，如： 
   
   ```bash
   $ git config --global alias.st status
   ```
   
   此后，可以使用 `git st` 代替 `git status` 。
   
   可以试试下面这个：
   
   ```bash
   git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"
   ```
   
   然后调用 `git lg` 。

2. `--global` 是指设置全局变量，没有这个参数则只在当前工作区生效。
   
   全局与工作区配置文件分别放在 `~/.gitconfig` 和 `./.git/config` 中。

## 参考资料

[1] [Git教程 - 廖雪峰的官方网站](https://www.liaoxuefeng.com/wiki/896043488029600)
