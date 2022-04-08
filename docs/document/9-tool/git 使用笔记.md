# git 使用笔记

## 入门篇

1. github 上创建仓库 GitTest（勾不勾选创建文件readme均可）

2. 本地找到合适位置（例如F:）打开 git bash

3. 若已有项目代码文件夹，则copy至F:路径下，e.g. GitTest,

   没有就mkdir -> cd

4. git init

5. git status  //（可选）查看状态

6. git add .

7. git commit -m "first commit - add a project"

8. git remote add origin https://github.com/XXX/xxx.git

9. git push -u origin master

## 命令行解释

4. git init  

   建好一个空仓库以后，多了一个`.git`的目录。这个目录是Git来跟踪管理版本库的，没事千万不要手动修改这个目录里面的文件，不然改乱了，就把Git仓库给破坏了。 

5. 最开始git status，会发现很多untracked files, git add . 之后就好了

6. git add . 

   告诉git，把文件添加到仓库

7. git commit -m "add files" 

   告诉git，把文件提交到仓库。 -m后是本次提交的说明，可以用来介绍自己做了哪些改动。

   执行成功后会告诉你多少文件做了哪些改动 

   注：可以分次add，然后一步commit

   即

   ```git
   $ git add file1.txt
   $ git add file2.txt file3.txt
   $ git commit -m "add 3 files."
   ```

8. git remote add origin https://github.com/XXX/xxx.git

   ①关于本命令

   把已有的本地仓库与github上创建的远程库关联

   origin 是远程仓库的别名，以后可以使用这个短的别名代指长的url，这个是默认别名，也可以起自己喜欢的。

   ②如果前面遇到一些问题，可能第二次remote的话会遇到

   ```
   fatal: remote origin already exists. 
   ```

   解决办法：  

   git remote rm origin 

   git remote add origin https://github.com/XXX/xxx.git 即可

9. git push -u origin master

   ①关于本命令

   把本地库所有内容推送到远程库上

   第一次加 -u

   之后只要 git push origin master

   ②可能会遇到

   ```git
   To https://github.com/xxx/xxx.git
    ! [rejected]        master -> master (fetch first)
   error: failed to push some refs to 'https://github.com/xxx/xxx.git'
   hint: Updates were rejected because the remote contains work that you do
   hint: not have locally. This is usually caused by another repository pushing
   hint: to the same ref. You may want to first integrate the remote changes
   hint: (e.g., 'git pull ...') before pushing again.
   hint: See the 'Note about fast-forwards' in 'git push --help' for details.
   ```

   出现这个问题的其中一个原因就是在远程创建仓库的时候，勾选了Initialize  this repository with a README这项，导致远程仓库不为空。

   解决办法：先将远程仓库的内容合并到本地，然后再上传，命令如下： 

   1. git pull --rebase origin master
   2. git push -u origin master

   

   （被大佬姐姐强行拷问rebase含义，拔苗助长，萌新不应该这么早接触这个命令吧捂脸

   rebase简介：

   ***\*git pull --rebase****

   1. 把本地 repo. 从上次 pull 之后的变更暂存
   2. 恢复到上次 pull 时的状态
   3. 合并远端的变更到本地
   4. 最后再合并刚刚暂存下來的本地变更

   就是你本地的工作仓库的默认分支是master，origin/master是远程默认分支，本地和远程关联，由于远程有一个readme，相当于修改了远程分支，导致本地和远程不一致，这时，git pull --rebase origin master，把本地变更暂存，合并远端的变更（也就是 readme文件）到本地，再合并刚刚暂存的本地变更

## 参考链接

 https://blog.csdn.net/JK198310/article/details/37881033  git rebase merge 区别 

https://blog.csdn.net/weixin_30954607/article/details/98890341 git --rebase 实现的具体过程

 https://blog.csdn.net/u013262534/article/details/81383058  git rebase 不同情况下的合并

(e.g.) 有一条代码线master线，同时有条新需求开发线dev线。master线从B开出分支dev线后，一直在做bug修复（C,D）。如下图： 

​	E F G  （dev）

A B C D    (master)

 此时，dev线需要合并master线修复的bug，以免重复开发。那我们可以用到git rebase命令。 

 git rebase --onto master dev，成功后，dev线就会合并C和D提交的记录。 

​		EFG (dev)

ABCD       (master)