- 账户
```
查看用户--git config user.name
查看邮箱--git config user.email
修改用户--git config --global user.name "username"(修改后pull，会让你重新输入用户名密码)
 git config --global user.email "abc@email.com"
检查配置信息--git config --list
```
- 放弃本地修改，强制拉取更新

```
1. 下载远程仓库最新内容，不做合并 -- git fetch --all
2. 把HEAD指向最新版本 -- git reset --hard origin/master
3. 拉取代码 -- git pull
```


- 记录更新到仓库

```
查看文件状态--git status、git status -s
添加修改--git add <files>将未跟踪文件添加跟踪、将不是暂存区的文件添加到暂存区
查看尚未暂存的改动--git diff
查看已暂存将要提交的改动--git diff --staged
提交更新--git commit -m "<des>"
移除文件-- git rm <files>
移动文件-- git mv <file> <file>
```


- 修改远程仓库地址方法有三种：

```
1.修改命令
git remote set-url origin <url>
例如：git remote set-url origin gitlab@gitlab.chumob.com:php/hasoffer.git
2.先删后加
git remote rm origin
git remote add origin <url>
3.直接修改config文件
```


- 连接到远程仓库

```
1.远程新建仓库
2.本地新建项目，通过 git init 初始化本地仓库
3.添加远程仓库 -- git remote add origin <url>
4.添加代码并提交 -- git add、git commit -m ""
5.将代码推送到远程仓库 -- git push -u origin master 第一次上传需要 -u
```

- 分支

```
查看分支--git branch -a(-a可选，可用来查看远程和本地分支)
切换分支--git checkout <name>
创建分支--git branch <name> <tag-name>(可选，从该tag上创建分支)
创建并切换分支--git checkout -b <name> <tag-name>
合并某分支到当前分支--git merge <name>
删除分支--git branch -d <name>
删除远程分支--git push origin --delete <name>(或者git push origin :<name>)
将当前分支替换为另一个
方法一
git checkout dev 切换到当前分支 
git reset –-hard pdl_2.0 将本地当前分支 dev 重置成 pdl_2.0 
git push origin dev –-force 再推送到远程仓库
方法二：把本地的dev分支强制(-f)推送到远程master
git push origin dev:master -f
```


- 标签

```
查看所有标签 -- git tag <-l> [name](<>为可选，比如git tag -l "v1.8.5*")
查看标签信息 -- git show <tagname>
打标签 -- git tag <name> <commit id>
打标签(附注标签) -- git tag -a <name> -m <descript> <commit id>（-a指定标签名，-m指定说明文字）
推送某个标签到远程 -- git push origin <tagname>
推送所有标签到远程 -- git push origin --tags
获取远程标签 -- git fetch origin tag <tagname>
删除标签 -- git tag -d v0.9
删除远程标签 -- git push origin :refs/tags/v0.9（需要先删除本地标签）
```


- 查看提交历史

```
查看提交的commit记录--git log [--pretty=oneline 一行展示]
查看每次提交的diff--git log -p -3（最后的3为最近三次）
查看某文件的commit记录--git log filename
查看某文件每次提交的diff--git log -p filename
版本回退
回退到前N个版本 -- git reset --hard HEAD^
（HEAD表示当前，HEAD^表示上个版本，往前100个版本为HEAD加上100个^，但一般用HEAD~100表示）
（hard选项表示彻底将工作区、暂存区和版本库记录恢复到指定的版本库）
回退到某次提交 -- git reset --hard <commit_id>
回退完后如有需要，强制推送到远程 -- git push origin HEAD --force
（或者用 git reflog 查看本地操作记录，回退到之前的记录）
```

- 版本回退

```
回退到前N个版本 -- git reset --hard HEAD^
（HEAD表示当前，HEAD^表示上个版本，往前100个版本为HEAD加上100个^，但一般用HEAD~100表示）
（hard选项表示彻底将工作区、暂存区和版本库记录恢复到指定的版本库）
回退到某次提交 -- git reset --hard <commit_id>
回退完后如有需要，强制推送到远程 -- git push origin HEAD --force
（或者用 git reflog 查看本地操作记录，回退到之前的记录）
```
    
    
- cherry-pick 挑拣某(几)次提交

```
1.查看提交历史，拿到commit id
2.切换到要合并的分支，执行命令
git cherry-pick [options] <commit_id>
git cherry-pick [options] <start_id> <end_id> 挑选一个区间内的，但是不包含start_id
git cherry-pick [options] <start_id>^ <end_id> 挑选一个区间内的，包含start_id
optons:
    --continue 遇到冲突，继续下一个
    --quit 遇到冲突，退出
    --abort 遇到冲突，直接打回原形，回到pick前的状态
    -n 不自动提交
    -e 编辑提交信息

3.如果出现冲突，解决冲突，然后add添加到暂存区，然后 git cherry-pick --continue
```


- Rebase

```
在master上新拉分支feature，开发完后要合并到master，但是此时master已经有人提交了
1. 切换到feature分支，执行 git rebase master（以master为基础，将feature分支上的修改增加到master分支上，并生成新的版本）
2. 解决冲突
3. git rebase --continue，如果没有冲突，不需要执行
4. 切换到主分支，将feature分支merge过来 git merge feature

git merge 操作合并分支会让两个分支的每一次提交都按照提交时间（并不是push时间）排序，并且会将两个分支的最新一次commit点进行合并成一个新的commit，最终的分支树呈现非整条线性直线的形式

git rebase操作实际上是将当前执行rebase分支的所有基于原分支提交点之后的commit打散成一个一个的patch，并重新生成一个新的commit hash值，再次基于原分支目前最新的commit点上进行提交，并不根据两个分支上实际的每次提交的时间点排序，rebase完成后，切到基分支进行合并另一个分支时也不会生成一个新的commit点，可以保持整个分支树的完美线性

如果俩人同时修改了一个代码，某人先提交了，为了避免出现新的一个merge，可以用rebase 拉取代码
1. 先将修改的代码提交
2. git pull --rebase origin [分支名字]
3. 解决冲突，然后 git add [文件名]（这里不能commit，否则无法continue，如果commit了需要reset到上一次的提交）
4. 解决完冲突后， 继续 git rebase --continue
```


- rebase回退

```
1.先执行命令查看本地记录 git reflog
2.从本地记录中找出rebase之前的id
3.执行命令回退 git reset --hard <id>
```

- 问题汇总
> permission denied publicKey
1. 【解决方案】添加publicKey后，还是报错，试着执行「ssh-add ~/.ssh/id_rsa」

> github下载太慢问题

1. 修改host文件，sudo vim /etc/hosts
1. 利用https://www.ipaddress.com/网站来获取「github.com」和「github.global.ssl.fastly.net」的ip地址
1. 然后将ip写入host文件中
1. 刷新dns缓存 ipconfig /flushdns