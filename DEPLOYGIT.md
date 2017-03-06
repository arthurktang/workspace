### File
* **README.md** : 这个主要描述项目的构建。如何build对第三方库的依赖。（当然包含构建工具的要求）
* **changed.md** : 描述每次发布的主要修改。
* **deploy.md** : 描述项目部署
* **CHANGELOG.md** : 修改记录
* **CONTRIBUTING.md** : 贡献

### Term
* **Feature** ：代表增加的功能
* **Implement** : 对feature 继续进行实现
* **Optimize** : 优化功能，对原始的算法，判断的逻辑做了调整。optimize因为是存在逻辑上的调整的，所以可能会产生问题。
* **Modify** : 调整。这个纯粹的是语法上的等价替换。(例如：原始hardcode现在变成macro；1个文件的拆成多个文件。这个属于等价替换，只是为了维护。)
* **Update** : 就是更新版本号，更新编译参数，切换默认的行为等。
* **Test** ：单元测试代码的更新。
* **Fix** : 代表修复某个已知的bug。
>1. 定义feature, 开始为feature 增加代码。
>2. 持续会有implement， 不断在实现某个feature
>3. 对已经存在的实现会有optimize, 可以看见在优化。
>4. 对工作的代码进行Modify: 代码做内部的调整，重构。Modify 基本等于Refactor.
>5. 对工作的代码产生的bug 开始进行fix.
>6. 对内部的版本好，默认编译进行切换等，产生update。 从默认的debug版本，切换到release版本。等。
>7. 单元测试代码

### Operate
>* git-cheatsheet.pdf
>* git config --global core.editor "atom --wait" （atom设置为git的默认编辑器）
>* git config --global core.editor "C:/Users/Schaepher/AppData/Local/atom/app-1.13.0/atom.exe --new-window --foreground --wait"
>* git checkout -b dev (origin/dev) == git branch dev + git checkout dev（创建一个dev分支（-b），并把远程dev分支（origin/dev）的内容放在该分支内。接着切换到该分支（checkout））
>* git merge --no-ff -m "xxx" dev (禁用fast forward，保留分支信息)
>* git stash/git stash list (修复bug把当前工作现场“储藏”起来，等以后恢复现场后继续工作)
>* git stash pop = git stash apply + git stash drop (恢复+删除)
>* git rebase -i f1f92b (-i或--interactive的参数是不需要合并的commit的hash值)
>* git rebase --abort 撤销修改
>* git pull --rebase = git fetch + git rebase teamone/master (git config --global pull.rebase true)

### Remote
```
git config --global user.name "Your Name"
git config --global user.email "email@example.com"
```
* **master** 分支是主分支，因此要时刻与远程同步；   
* **dev** 分支是开发分支，团队所有成员都需要在上面工作，所以也需要与远程同步；
* **bug** 分支只用于在本地修复bug，就没必要推到远程了，除非老板要看看你每周到底修复了几个bug；
* **feature** 分支是否推到远程，取决于你是否和你的小伙伴合作在上面开发。
* **ssh key** `ssh-keygen -t rsa -C "youremail@example.com" （ssh -T git@github.com）`
* **设置github** `git remote add origin git@server-name:path/repo-name.git`
* **推送github** `git push -u origin master/git push origin master`
>由于远程库是空的，我们第一次推送master分支时，加上了-u参数，Git不但会把本地的master分支内容推送的远程新的master分支，还会把本地的master分支和远程的master分支关联起来，在以后的推送或者拉取时就可以简化命令。
* **克隆github** `git clone git@github.com:michaelliao/gitskills.git`
* **upstream**
>1. 查看远端库`git remote -v`
>2. 设置upstream`git remote add upstream 团队项目地址`
>3. 获取团队项目最新版本`git fetch upstream`
>4. 会将源分支（upstream/dev）合并到当前分支（dev）`git merge upstream/dev`

### Server for Ubuntu or Debian
1. 安装git：`sudo apt-get install git`
2. 创建一个git用户，用来运行git服务：`sudo adduser git`
3. 创建证书登录：收集所有需要登录的用户的公钥（id_rsa.pub文件），把所有公钥导入到/home/git/.ssh/authorized_keys文件里，一行一个。
4. 初始化git仓库：先选定一个目录作为git仓库（/srv/sample.git，在/srv目录下输入命令）`sudo git init --bare sample.git`
  >Git就会创建一个裸仓库，裸仓库没有工作区，因为服务器上的Git仓库纯粹是为了共享，所以不让用户直接登录到服务器上去改工作区，并且服务器上的Git仓库通常都以.git结尾。然后，把owner改为git：`sudo chown -R git:git sample.git`

5. 禁用shell登录：出于安全考虑，第二步创建的git用户不允许登录shell，这可以通过编辑/etc/passwd文件完成。找到类似下面的一行：`git:x:1001:1001:,,,:/home/git:/bin/bash`
改为：`git:x:1001:1001:,,,:/home/git:/usr/bin/git-shell`这样，git用户可以正常通过ssh使用git，但无法登录shell，因为我们为git用户指定的git-shell每次一登录就自动退出。
6. 克隆远程仓库：现在，可以通过git clone命令克隆远程仓库了，在各自的电脑上运行：`git clone git@server:/srv/sample.git`

> **管理公钥** 如果团队很小，把每个人的公钥收集起来放到服务器的/home/git/.ssh/authorized_keys文件里就是可行的。如果团队有几百号人，就没法这么玩了，这时，可以用 **Gitosis** 来管理公钥。这里我们不介绍怎么玩Gitosis了，几百号人的团队基本都在500强了，相信找个高水平的Linux管理员问题不大。

> **管理权限** 有很多不但视源代码如生命，而且视员工为窃贼的公司，会在版本控制系统里设置一套完善的权限控制，每个人是否有读写权限会精确到每个分支甚至每个目录下。因为Git是为Linux源代码托管而开发的，所以Git也继承了开源社区的精神，不支持权限控制。不过，因为Git支持钩子（hook），所以，可以在服务器端编写一系列脚本来控制提交等操作，达到权限控制的目的。**Gitolite** 就是这个工具。
