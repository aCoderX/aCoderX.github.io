---
title: git操作
date: 2016-10-08 20:17:50
tags: git
---
### 基本操作

##### git status  
仓库当前的状态

##### git diff
查看difference(修改的地方)
 <!--more-->
##### git add     
把文件修改添加到暂存区
##### git commit -m ""   
提交（把暂存区的所有内容提交到当前分支）

![git_commit](http://oe9o9e68g.bkt.clouddn.com/git_commit.bmp)

##### git log
查看历史记录
##### git reset --hard HEAD^
用HEAD表示当前版本，上一个版本就是HEAD^，上上一个版本就是HEAD^^，当然往上100个版本写100个^比较容易数不过来，所以写成HEAD~100。
##### git reset --hard 3628164

##### git reflog
如果回退到以前的版本，还想回到原本的最新的版本，并且已经关闭了原本页面看不到原本的commit id，用该命令看id

##### git checkout -- readme.txt 
就是让这个文件回到最近一次git commit或git add时的状态（还没有add）
##### git reset HEAD file  
命令既可以回退版本，也可以把暂存区的修改回退到工作区（add之后）
##### git rm   
从版本库中删除该文件

##### git remote add origin git@github.com:地址/本地项目名.git   
将本地仓库管理guthub远程仓库

##### git push -u origin master   
把本地库的内容推送到远程，用git push命令，实际上是把当前分支master推送到远程。远程库的名字就是origin，这是Git默认的叫法。我们第一次推送master分支时，加上了-u参数，Git不但会把本地的master分支内容推送的远程新的master分支，还会把本地的master分支和远程的master分支关联起来，在以后的推送或者拉取时就可以简化命令

##### git clone git@github.com:地址/项目名.git  
克隆远程库到本地

GitHub给出的地址不止一个，还可以用https://github.com/michaelliao/gitskills.git这样的地址。实际上，Git支持多种协议，默认的git://使用ssh（快），但也可以使用https等其他协议。

### 分支
> 分支在实际中有什么用呢？假设你准备开发一个新功能，但是需要两周才能完成，第一周你写了50%的代码，如果立刻提交，由于代码还没写完，不完整的代码库会导致别人不能干活了。如果等代码全部写完再一次提交，又存在丢失每天进度的巨大风险。

##### git checkout -b dev  
创建dev分支并切换到dev分支

相当于以下两条命令：
* $ git branch dev （创建分支） 
* $ git checkout dev  （切换分支） 

##### git branch命令查看当前分支

##### git merge dev
切换回master分支之后合并dev分支

合并时冲突需要手动修改后再提交     Git用<<<<<<<，=======，>>>>>>>标记出不同分支的内容

##### git branch -d dev  
删除dev分支
##### git log --graph --pretty=oneline --abbrev-commit
查看分支历史

通常，合并分支时，如果可能，Git会用Fast forward模式，但这种模式下，删除分支后，会丢掉分支信息。

如果要强制禁用Fast forward模式，Git就会在merge时生成一个新的commit，这样，从分支历史上就可以看出分支信息。

##### git merge --no-ff -m "merge with no-ff" dev
合并分支时，加上--no-ff参数就可以用普通模式合并，合并后的历史有分支，能看出来曾经做过合并，而fast forward合并就看不出来曾经做过合并。
![merge](http://oe9o9e68g.bkt.clouddn.com/git_merge.bmp)

修复bug时，我们会通过创建新的bug分支进行修复，然后合并，最后删除；

当手头工作没有完成时，先把工作现场git stash一下，然后去修复bug，修复后，再git stash pop，回到工作现场。
（也可用git stash apply恢复，但是恢复后，stash内容并不删除，你需要用git stash drop来删除）
##### git stash list 
用于查看stash保存的现场

如果要丢弃一个没有被合并过的分支，可以通过git branch -D <name>强行删除。

##### 多人合作的提交和拉取更新
1. 首先，可以试图用git push origin branch-name推送自己的修改；
2. 如果推送失败，则因为远程分支比你的本地更新，需要先用git pull试图合并；
3. 如果合并有冲突，则解决冲突，并在本地提交；
4. 没有冲突或者解决掉冲突后，再用git push origin branch-name推送就能成功！

如果git pull提示“no tracking information”，则说明本地分支和远程分支的链接关系没有创建，用命令git branch --set-upstream branch-name origin/branch-name。

### tag
相当于某个版本的快照

tag就是一个让人容易记住的有意义的名字，它跟某个commit绑在一起。
##### git tag <name>
用于新建一个标签，默认为HEAD，也可以指定一个commit id；
##### git tag -a <tagname> -m "blablabla..."
可以指定标签信息；
##### git tag -s <tagname> -m
"blablabla..."可以用PGP签名标签；
##### git tag
可以查看所有标签。
##### git show <tagname>
查看标签信息
##### git push origin <tagname>
可以推送一个本地标签；
##### git push origin --tags
可以推送全部未推送过的本地标签；
##### git tag -d <tagname>
可以删除一个本地标签；
##### git push origin :refs/tags/<tagname>
可以删除一个远程标签。

### 使用github fork
将开源项目fork到自己的github账号，然后，从自己的账号下clone：
git clone git@github.com:michaelliao/bootstrap.git

希望bootstrap的官方库能接受你的修改，你就可以在GitHub上发起一个pull request。当然，对方是否接受你的pull request就不一定了
![github_pull](http://oe9o9e68g.bkt.clouddn.com/git_github_pull.bmp)


##### git config --global color.ui true 
让Git显示颜色


### 忽略特殊文件
忽略某些文件时，需要在Git工作区的根目录下创建一个特殊的.gitignore文件（比如忽略.class文件）

.gitignore文件本身要放到版本库里，并且可以对.gitignore做版本管理！

#####  git add -f App.class
不管忽略文件强制提交
##### git check-ignore -v App.class 
找出忽略文件在哪一行

### 别名
##### git config --global alias.st status 
将status设置成st   --global表名当面的别名适用于整个用户 不加则使用户当前库

别名的删除和修改，通过修改配置文件，库级别的放在每个仓库的Git配置文件都放在.git/config文件中，用户级别的放在当前用户的Git配置文件放在用户主目录下的一个隐藏文件.gitconfig中

[参考地址](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/0013752340242354807e192f02a44359908df8a5643103a000)
