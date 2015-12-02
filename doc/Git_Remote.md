### Git远程使用技巧
git作为强大的版本管理软件，已经得到了广泛的应用，很多人对于本地的git操作已经非常熟悉了。然而有的时候，我们也需要一个远程的，类似云的仓库来存储我们的一些代码。github给予了我们不限量的空间来使用，今天我也来简单地总结一下在git远程使用时经常会用到的一些命令与技巧。  
##### 1. 初次连接本地与远程主机
```
git init
git add .
git commit -m "first commit"
git remote add origin git@github.com... // 将本地仓库与github连接，并创建远程主机origin
git push origin master:master // 将本地master推送的远程origin/master
```
##### 2. git remote相关命令
git要求每一个远程主机都要有一个独立的主机名，而git remote相关命令，就是用来管理主机名的。  
```
git remote 用来查看当前项目的所有主机名
git remote -v 则可以显示主机名及其网址
git remote show [主机名] 用来查看某个主机的详细信息
git remote add [主机名][github网址] 用来添加一个远程主机
git remote rm [主机名] 则会删除相应的主机
```
##### 3. git push相关命令
```
git push [远程主机名][本地分支名]:[远程分支名] 用于将本地提交内容推送到远程主机上
git push [远程主机名] 将当前分支推送到远程主机的对应分支
```
##### 4. git pull
由于git fetch在取回服务器上的程序更新时，还需要手动进行合并，对于一些小型的程序而言，git pull命令则更为实用。
```
git pull [远程主机名][远程分支名]:[本地分支名] 取回远程某个分支更新，并与本地指定分支合并
git pull [远程主机名][远程分支名] 取回远程分支的更新，并与本地当前分支合并
```
虽然以上命令都有相应的简化版本，但是做为一个版本管理软件，最为重要的特性是清晰地版本与分支管理。因此，我推荐使用完整的git命令，这会使大家对自己的代码版本架构有更清晰地理解。
##### 5. 关于忽略同步一些文件
在使用git的时候，我们往往希望在同步过程中忽略一些涉及私人信息的文件，如使用Mac时的.DS_Store文件。此时也很简单，只需要添加名为`.gitignore`的文件并在其中添加相应的忽略文件即可。
这里的文件名使用通用的正则表达式进行匹配。
- 斜杠"/"表示目录
- 星号"*"表示通配多个字符
- 问好"?"表示通配单个字符
- 方括号"[ ]"包含单个字符的匹配列表
- 叹号"!"表示不忽略匹配到得文件或目录

关于正则表达式，不了解的读者可以戳[百度百科](http://baike.baidu.com/link?url=1mpBMLj2cGNyJVt4a3q8dequpHlfR-oCqBEhRDb4nLLLTMwy4aXbcxF1neUUkTNSlgT9R0yMIvhL4yZxmBrNZ_)，后面也许我会写一篇详细的文章介绍正则表达式，在coding中还是十分常用的。

git命令参考文章：http://www.ruanyifeng.com/blog/2014/06/git_remote.html
***
By tjt
2015.09.06
