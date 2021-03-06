# git使用

* 新建一个git项目并推送到git服务器  

     * 准备条件:  
     
     1. 新建一个文件夹作为项目的根目录，如demo  
     
     2. 在远程服务器上新建一个仓库，并命名，假设新建好的仓库的地址是`https://github.com/johnxue2013/ocdemo.git`    
     
     3. 进入步骤一中新建的文件夹demo，右击选择git bash here（windows上是这样操作，如果在mac或者linux上，需要从终端进入）  
     
     4. 执行如下命令  
     ```
     git init
     git add README.md
     git commit -m "first commit"
     git remote add origin https://github.com/johnxue2013/ocdemo.git
     git push -u origin master
     ```


* 将已有的本地仓库推送到远程服务器
     ```
     git remote add origin https://github.com/johnxue2013/ocdemo.git
     git push -u origin master
     ```


* 推送数据到远程仓库
     ```git push [remote-name] [branch-name]```
   > 将本地仓库中的数据推送到远程仓库,如把本地的master分支推送到origin服务器上的master分支上(再次说明下，克隆操作会自动使用默认的master作为默认分支，origin作为远程仓库在本地的别名), 使用
     ```git push origin master```

* 远程仓库的删除和重命名
在新版Git中可以用 ```git remote rename```命令修改某个远程仓库在本地的简称，比如想把 pb 改成 paul，可以这么运行:  

     ```git remote rename pb paul```
> 对远程仓库的重命名，也会使对应的分支名称发生变化，原来的 pb/master 分支现在成了 paul/master。

* 遇到冲突时的分支合并  

     有时候合并操作并不会如此顺利。如果在不同的分支中都修改了同一个文件的同一部分，Git 就无法干净地把两者合到一起
Git 作了合并，但没有提交，它会停下来等你解决冲突。要看看哪些文件在合并时发生冲突，可以用 git status 查阅,
合并文件，并删除<<<<<<<，======= 和 >>>>>>> 这些行。在解决了所有文件里的所有冲突后，运行 git add 将把它们标记为已解决状态,
此时直接运行git commit -m "XXX"就可以完成此次合并,而无需再次运行git merge {branch-name}

* 远程分支的内容合并到当前分支(git pull)    

     如果要把该远程分支serverfix的内容合并到当前分支，可以运行`git merge origin/serverfix` 如果想要一份自己的serverfix来开发，可以在远程分支的基础上分化出一个新的分支来:运行  
`git checkout -b serverfix origin/serverfix`

git pull 命令的作用是，取回远程主机某个分支的更新，再与本地执行的分支合并，它的完整格式稍稍有点复杂。  

`$ git pull <远程主机名> <远程分支名>:<本地分支名>`
     
比如取回origin主机的next分支，与本地的master分支合并，需要写成  

`$ git pull origin next:master`
    
如果远程分支是与当前分支合并，则冒号和后面的分支名可以省略  

`$ git pull origin next`  
    
上面命令表示取回origin上的next分支，再与当前分支合并。实质上，这等同于先做git fetch，再做 git merge。  
`$ git fetch origin`
`$ git merge origin/next`


>在某些场合，git会自动在本地分支与远程分支之间，建立一种追踪关系(tracking)。比如，在git clone的时候，
所有本地分支默认与远程主机的同名分支，建立追踪关系，也就是说，本地的master分支自动追踪"origin/master分支

* git允许手动建立追踪追踪关系  

     `$ git branch --set-upstream master origin/next`
    >该命令将被舍弃，推荐使用`git branch --set-upstream-to=<远程主机名>/<远程分支名> <本地分支名>`
上面的命令指定本地master分支追踪远程origin/next分支，如果当前分支与远程分支存在追踪关系，git pull就可以省略远程分支名称  

     `$ git pull origin`  
     
上面的命令表示，本地的当前分支自动与对应的origin主机"追踪分支"进行合并。如果当前分支只有一个追踪分支。连接主机名都可以省略。
     `$ git pull`  
     
上面命令表示，当前分支自动与唯一追踪分支进行合并。

* 本地内容提交到远程(git push)  

git push命令用于将本地分支的更新，推送到远程主机。他的格式与git pull命令相仿。  

     `$ git push <远程主机名> <本地分支名称>:<远程分支名>`
     > 分支推送的顺序是<本地分支名称>:<远程分支名>，而git pull是<远程分支名>:<本地分支名>，两者相反。  
     
如果省略远程分支名，则表示将本地分支推送与之存在“追踪关系”的远程分支(通常两者同名), 如果该远程分支不存在，则会被新建。  

     `$ git push origin master`  
     
上面的命令表示，将本地的master分支推送到origin主机的master分支。如果origin主机不存在master分支，则origin将新建master分支。

如果省略本地分支名，则表示删除指定的远程分支，因为这等同于推送一个空的本地分支到远程分支。  

     `$ git push origin :master`
等同于  

     `$ git push origin --delete master`  
     
如果当前所在分支与远程分支之间存在追踪关系。则push时，本地分支和远程分支都可以省略路。  

     `$ git push origin`  
     
上面命令表示，将当前分支推送到origin对应的分支。

如果当前分支只有一个追踪分支，那么主机名都可以省略。  

     `$ git push`  
     
如果当前分支与多个主机存在追踪关系，则可使用-u选项指定一个默认主机  

     `$ git push -u origin master`  
     
上面的命令将本地的master分支推送到origin主机被追踪的分支。同时指定origin为默认主机，这样后面就可以不加任何参数使用git push了。


* 储藏(Stash)
经常有这样的事情发生，当你正在进行项目中某一部分的工作，里面的东西处于一个比较杂乱的状态，而你想转到其他分支上进行一些工作。问题是，你不想提交进行了一半的工作，否则以后你无法回到这个工作点。解决这个问题的办法就是git stash命令。
“‘储藏”“可以获取你工作目录的中间状态——也就是你修改过的被追踪的文件和暂存的变更——并将它保存到一个未完结变更的堆栈中，随时可以重新应用。


比如，你运行git status命令,出现如下结果
![image](https://github.com/johnxue2013/tools/blob/master/images/git1.png)  

现在你想切换分支,git会报错，无法完成分支切换。若想完成切换，需要使用git stash命令。  

![image](https://github.com/johnxue2013/tools/blob/master/images/git2.png)

此时再运行git status 命令，将显示你的分支是干净的  

![image](https://github.com/johnxue2013/tools/blob/master/images/git3.png)  

此时你就可以切换到其他分支进行工作了，如果想要恢复stash时的场景，使用git stash apply, 可以使用git stash list命令列出所有的
stash记录。  

![image](https://github.com/johnxue2013/tools/blob/master/images/git4.png)  

你也可以使用git stash apply stash@{2}，去指定恢复哪一个stash。默认情况下git stash apply使用最近一次的stash。

apply选项只是尝试应用存储的工作---stash的内容仍然在栈上。要移除它，你可以运行git stash drop <stash-name>，  

![image](https://github.com/johnxue2013/tools/blob/master/images/git5.png)  

你也可以运行git stash pop <stash-name>来重新应用存储，同时立刻将其从堆栈中移走。

> 若想要stash时携带注释，可使用git stash save <注释>。如: git stash save "暂存bug123"

* git branch命令的使用

 `git branch` 不带参数：列出本地已经存在的分支，并且在当前分支的前面加“*”号标记

 `git branch -r` 列出远程分支，例如：
   ```
   #git branch -r
   m/master -> origin_apps/m1_2.3.4
   origin_apps/hardware/test
   origin_apps/m1
   origin_apps/m1_2.3.4
   origin_apps/master
   ```

   `git branch -a` 列出本地分支和远程分支，例如：
   ```
   #git branch -a
   * master
   newbranch
   remotes/m/master -> origin_apps/m1_2.3.4
   remotes/origin_apps/hardware/test
   remotes/origin_apps/m1
   remotes/origin_apps/m1_2.3.4
   remotes/origin_apps/master
   ```

   `git branch` 创建一个新的本地分支，需要注意，此处只是创建分支，不进行分支切换，例如：
   ```
   #git branch newbranch2
   #git branch
   * master
   newbranch
   newbranch2
   ```
   当前的分支依然是master，不进行切换。

   `git branch -m | -M oldbranch newbranch` 重命名分支，如果newbranch名字分支已经存在，则需要使用-M强制重命名，否则，使用-m进行重命名。

   `git branch -d | -D branchname` 删除branchname分支

   `git branch -d -r branchname` 删除远程branchname分支

* 将服务器端的版本强制恢复(回退)到某一次提交
     1. 首先使用`git log`命令，找到你想恢复到哪一次的提交的commit id  
     
     ![image](https://github.com/johnxue2013/tools/blob/master/images/git6.png)  

     2. 使用`git reset --hard <commit_id>`重置

     3、使用`git push <clone下来的服务器别名> HEAD --force`，如 git push origin HEAD --force


* 将本地项目纳入git管理，并使用命令行将本地项目导入github
```
git initgit add README.mdgit commit -m "first commit"
git remote add origin https://github.com/johnxue2013/medicinerecommendation.git
git push -u origin master
```

* 将已经纳入git管理的项目导入github
```
git remote add origin https://github.com/johnxue2013/medicinerecommendation.git
git push -u origin master
```

* 一个项目同时与多个远程服务器保持同步
      开发项目的时候，一般公司会搭建自己git服务器，我们上班时都用这个服务器进行同步。一般这个服务器都是局域网内可访问，并不会有域名。
这样就存在一个问题，如果下班之后想要继续编写代码，而且想在第二天上班时，同步到自己下班之后写的代码该怎么办？此时可以在公司的电脑上将公司的项目同时与两个远程服务器进行同步。一般第一次从git上拉取项目时(准确的说是clone)，git默认会将拉取下来的项目服务器名命名为origin，具体可以在git clone <项目url> 之后运行git remote show，或者git remote -v 进行查看。  

![image](https://github.com/johnxue2013/tools/blob/master/images/git7.png) 

如上图，此时我的medicinerecommendation项目保持与两个远程服务器进行同步，一个是10.1.64.87，这个是公司的git服务器，另一个是github服务器。

> 默认情况下，clone下来的项目只会一个git服务器保持同步(即可以与该服务器进行push和fetch)，运行git remote add <远程服务器别名> <远程仓库所在URL>命令，添加一个远程服务器。若无错误(服务器别名不可以重名，即如果有一个叫做origin的服务器别名，此时添加第二个远程服务器时，不可以再叫做origin)，再运行git remote show,将出现多个远程分支.  

在一个项目有多个远程服务器的情况下，push和fetch都是和一个项目有一个远程服务器一样的。如想要提交代码到mr，则运行git push mr <本地分支名>:<服务器分支名>,你也可以指定一个默认的远程分支，通过git push -u mr dev:dev 命令提交本地分支dev到远程服务器同名分支dev，并指定mr为默认服务器。 此时mr服务器将作为本项目的默认服务器，当push不指明服务器时，mr将作为缺省值。

* git强制覆盖服务器
     当服务器上的代码变的很乱或有错误时，但本地的代码确实完整且正确的，此时可以使用本地代码强制覆盖服务器代码，
     使用 git push <服务器别名> <本地分支名>:<服务器分支名> --force
     
* git如何忽略已经提交的文件
     如果想忽略某些文件或文件夹且该文件或文件夹从未被提交过，可以直接在项目根目录下在git命令行窗口中使用touch .gitignore命令新建一个文件，在该文件中添加想要忽略的文件或文件夹，
如忽略项目根目录下的.idea文件夹，则.gitignore文件的内容应该是下面这个样子。

     /.idea

       有时候不小心提交了不想提交的文件，如某些配置文件。当提交之后，再在.gitignore配置忽略将不起作用，因为该文件只能用作与Untrack Files，
也就是从来没有被git记录过的文件(自添加以后，从未add以及commit过的文件)。也就是说一旦add或者commit之后，该文件或者文件夹就处于track file，
此时再修改.gitignore当然就不起作用了。此时正确的做法是使用
     git rm --cached <文件名>
然后修改.gitignore配置忽略，最后
     git add .gitignore
再
     git commit -"注释"
再
     git push <服务器别名> <本地分支名>:<服务器分支名>
至此服务器将不包含被忽略的文件或者文件夹。

说明: git rm --cached <文件名>  删除的是追踪状态，而不是物理文件；如果你真不想要了，你可以使用直接使用rm 命令删除该文件或文件夹


* tag打标签
      同大多数 VCS 一样，Git 也可以对某一时间点上的版本打上标签。人们在发布某个软件版本（比如 v1.0 等等）的时候，经常这么做。本节我们一起来学习如何列出所有可用的标签，如何新建标签，以及各种不同类型标签之间的差别。
    * 列出已有的标签列出现有标签的命令非常简单，直接运行 git tag 即可：
```
$ git tag
v0.1
v1.3
```
显示的标签按字母顺序排列，所以标签的先后并不表示重要程度的轻重。
我们可以用特定的搜索模式列出符合条件的标签。在 Git 自身项目仓库中，有着超过 240 个标签，如果你只对 1.4.2 系列的版本感兴趣，可以运行下面的命令：
```
$ git tag -l 'v1.4.2.*'
v1.4.2.1
v1.4.2.2
v1.4.2.3
v1.4.2.4
```
* 新建标签Git  
    使用的标签有两种类型：轻量级的（lightweight）和含附注的（annotated）。轻量级标签就像是个不会变化的分支，实际上它就是个指向特定提交对象的引用。而含附注标签，实际上是存储在仓库中的一个独立对象，它有自身的校验和信息，包含着标签的名字，电子邮件地址和日期，以及标签说明，标签本身也允许使用 GNU Privacy Guard (GPG) 来签署或验证。一般我们都建议使用含附注型的标签，以便保留相关信息；当然，如果只是临时性加注标签，或者不需要旁注额外信息，用轻量级标签也没问题。

* 含附注的标签创建一个含附注类型的标签非常简单，用 -a （译注：取 annotated 的首字母）指定标签名字即可：
```
$ git tag -a v1.4 -m 'my version 1.4'
$ git tag
v0.1
v1.3
v1.4
```
而 -m 选项则指定了对应的标签说明，Git 会将此说明一同保存在标签对象中。如果没有给出该选项，Git 会启动文本编辑软件供你输入标签说明。
可以使用 git show 命令查看相应标签的版本信息，并连同显示打标签时的提交对象。
```
$ git show v1.4
tag v1.4
Tagger: Scott Chacon <schacon@gee-mail.com>
Date:   Mon Feb 9 14:45:11 2009 -0800

my version 1.4
commit 15027957951b64cf874c3557a0f3547bd83b3ff6
Merge: 4a447f7... a6b4c97...
Author: Scott Chacon <schacon@gee-mail.com>
Date:   Sun Feb 8 19:02:46 2009 -0800
```  

我们可以看到在提交对象信息上面，列出了此标签的提交者和提交时间，以及相应的标签说明。

* 分享标签  

默认情况下，git push 并不会把标签传送到远端服务器上，只有通过显式命令才能分享标签到远端仓库。其命令格式如同推送分支，运行  
`git push origin [tagname] `  
即可。  
```
$ git push origin v1.5
Counting objects: 50, done.
Compressing objects: 100% (38/38), done.
Writing objects: 100% (44/44), 4.56 KiB, done.
Total 44 (delta 18), reused 8 (delta 1)
To git@github.com:schacon/simplegit.git
* [new tag]         v1.5 -> v1.5
```
如果要一次推送所有本地新增的标签上去，可以使用 --tags 选项：  

```
$ git push origin --tags
Counting objects: 50, done.
Compressing objects: 100% (38/38), done.
Writing objects: 100% (44/44), 4.56 KiB, done.
Total 44 (delta 18), reused 8 (delta 1)
To git@github.com:schacon/simplegit.git
 * [new tag]         v0.1 -> v0.1
 * [new tag]         v1.2 -> v1.2
 * [new tag]         v1.4 -> v1.4
 * [new tag]         v1.4-lw -> v1.4-lw
 * [new tag]         v1.5 -> v1.5
```
现在，其他人克隆共享仓库或拉取数据同步后，也会看到这些标签。

* git使用https方式连接，在命令行模式下记住密码设置方式：

1. 在命令行中输入  

   `git config credential.helper store`  
   
   回车
2. 设置后，只要在推送一次，以后就不需要用户名和密码了 只要运行后，下次push的时候再输入一次密码，git就会记住  

* 忽略已经跟踪的文件
有时想要忽略一些已经追踪的文件，你会发现将已经追踪的文件放入.gitignore是没有效果的，当你修改想要忽略的文件后，`git status` 还是可以看到提示该文件已被修改。此时可以使用如下命令解决  
`git rm --cached <filename>`  
命令行中的<filename> 是想要忽略的文件名如:  
`git rm --cached web/test.html`  
将忽略已经追踪的web下的test.html文件。此时修改test.html后再使用`git status` 将不提示该文件被修改




## 附录：
1. `git status`    //获取你当前所在的分支上未track的文件，为track的文件需要使用git add [文件名]进行track
2.  git add  ...     //把你修改的东西加到git中 //可以使用批量提交，如 提交src下所有未track的文件使用命令 git add src/
3. `git commit -m "这是注释"`      //把你的修改提交到本地
4. `git fetch origin [分支名称]`      //把服务器上的文件拉到本地
5. `git merge origin/[分支名称]`      //把你拉来的文件和你本地的分支合并，/两边没有空格， 如果有冲突解决冲突，重复步骤1,2
6. `git push -u origin [分支名称]`

> 如果设置了本地与远程的追踪关系，以上步骤4、5可以合并为`git pull`语句。
> 步骤6中的 -u 参数为将origin 远程仓库设置为默认推送仓库(当本地项目追踪多个远程仓库时可以通过此参数指定默认远程仓库后，可以直接执行git push)








