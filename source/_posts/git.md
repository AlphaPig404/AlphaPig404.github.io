---
tags: [Tools] title: git常用命令 
---
>git是用于Linux内核开发的版本控制工具。与CVS、Subversion一类的集中式版本控制工具不同，它采用了分布式版本库的作法，不需要服务器端软件，就可以运作版本控制，使得源代码的发布和交流极其方便。git的速度很快，这对于诸如Linux内核这样的大项目来说自然很重要。git最为出色的是它的合并追踪（merge tracing）能力。  

## 本地操作

### 创建版本库

**git init**  

创建一个本地文件夹dir，通过git init命令把这个文件夹编程git可以管理的仓库。在仓库里面创建的文件都能通过git操作。  

<!--more-->
### 工作区、暂存区和提交区  

仓库中创建的文件默认是工作区。   
 
**git add <file**   

通过git add命名可以把文件的修改添加到暂存区；  
git add .可以一次性添加所有的文件修改；  
git add “*.txt"添加指定格式的文件修改。
  
**git commit** 

通过git commit提交更改，实际上就是把暂存区的所有内容提交到当前分支(branch).正常的使用是git commit -m ”提交信息“，加上提交信息来提交，方便管理。
  
**git status**  

可以用来追踪文件状态。  

**git diff** 

可以看到文件修改过的地方，在add之前使用。每次的文件修改都需要add到缓存区才能保存修改。 
 
### 撤销操作

**git reset HEAD <file**

可以将暂存区的修改回退到工作区，且会保留修改。
  
**git checkout --<file**   

可以将暂存区的修改回退到工作区，且会丢弃工作区的修改。实际上是用版本库里的版本替代工作区的版本，无论工作区是修改还是删除，都能被还原到之前的状态。  

**git reset --[hard|soft|mixd|merge|keep]<commit_id|HEAD^n**  

将当前版本回退到指定的commit_id(绝对路径)或者HEAD前n次提交的版本(相对路径)。  
通过git log命令能够看到提交历史，包含每次提交的commit_id，通过git reflog能看到所有HEAD的历史，也就是reset，checkout等操作的操作记录。  
参数hard会直接丢弃回退路径上的历史提交；  
参数soft只是将HEAD指向回退的版本，路径上的提交内容会回退到暂存区...  

**git rm <file**  

删除指定文件   

### 关联仓库

**git remote add origin** 

通过git remote add origin git@github.com:user/repository可以将本地仓库与远程仓库相关联，利用git push -u origin <branch 将本地库所有内容推送到远程库上，-u只在第一次推送时使用。

<!--more-->

## 远程仓库(以GitHub为例) 

### 创建版本库

**git clone** 

git clone git@github.com:user/repository  
git clone ssh|https://github.com/repositoryURL 
 
**git remote** 

通过clone操作得到的本地仓库与远程仓库自动对应，可以通过git remote -v来查看远程仓库的地址。  

**git push**

通过git push origin branch-name来推送修改的内容。 
 
**git pull**

获取远程仓库来更新本地仓库并合并到远程仓库。相当于git fetch加上git merge。  

PS：通过git push origin :branch-name能删除远程的分支。

## 分支操作  

### 分支的增删查改

**git checkout -b dev**  

创建dev分支，并切换到dev分支。相当于git branch dev和git checkout dev两个操作。  

#### git branch 

查看当前所有分支与当前所在分支。  

**git checkout dev**  

切换到dev分支

**git merge dev** 

把dev分支的提交结果合并到指定checkout的分支，并不一定是master分支。  

**git branch -d dev**  

删除dev分支，在dev分支合并后才能执行。如果要丢弃一个没有合并的dev，可以通过git branch -D dev强行删除。  

### 合并冲突  

执行合并分支到远端命令时，会因为远端分支的改动而出现与本地原分支不匹配的情况，需要手动改动冲突文件，提交到本地分支，然后再合并。在合并改动前，可以使用git diff <source_branch <target_branch来预览差异。

### 多人协作

多人协作的工作模式通常是这样：  
首先，可以试图用git push origin branch-name推送自己的修改；  
如果推送失败，则因为远程分支比你的本地更新，需要先用git pull试图合并；  
如果合并有冲突，则解决冲突，并在本地提交；  
没有冲突或者解决掉冲突后，再用git push origin branch-name推送就能成功！  
如果git pull提示“no tracking information”，则说明本地分支和远程分支的链接关系没有创建，用命令git branch --set-upstream branch-name origin/branch-name。

#### 参考链接

[极客学院git教程](http://wiki.jikexueyuan.com/project/git-tutorial/collaboration.html)  
[git简明指南](http://rogerdudler.github.io/git-guide/index.zh.html)   
[慕课网git技巧](http://www.imooc.com/article/1426)




  



