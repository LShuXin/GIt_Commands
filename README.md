# Git 常用命令整理



## 获取所有分支

```
git pull
```



## 查看所有分支

```
git branch -a
```



## 远程分支

```
remote/origin/master
```



## 切换到对应分支

```
git reset --hard tagName

或者

git checkout branchName

或者

git checkout tabName
```



## 删除远程分支 

```
git push origin --delete branchName
```



## 删除本地分支

```
git branch -d branchName  // 强制删除用-D
```



## 本地分支重命名

```
git branch -m oldBranchName newBranchName
```



## 将分支推送到远程

```
git push origin newName
```



## git push的一般形式

```
git push <远程主机名> <本地分支名> <远程分支名> ，例如 git push origin master:refs/for/master
```

即是将本地的master分支推送到远程主机origin上的对应master分支， origin 是远程主机名，第一个master是本地分支名，第二个master是远程分支名。



如果远程分支被省略，如上则表示将本地分支推送到与之存在追踪关系的远程分支（通常两者同名），如果该远程分支不存在，则会被新建

```
git push origin master
```



如果省略本地分支名，则表示删除指定的远程分支，因为这等同于推送一个空的本地分支到远程分支，等同于 git push origin --delete master

```
git push origin :refs/for/master
```



如果当前分支与远程分支存在追踪关系，则本地分支和远程分支都可以省略，将当前分支推送到origin主机的对应分支 

```
git push origin
```



如果当前分支只有一个远程分支，那么主机名都可以省略，形如 git push，可以使用git branch -r ，查看远程的分支名

```
git push
```



如果当前分支与多个主机存在追踪关系，则可以使用 -u 参数指定一个默认主机，这样后面就可以不加任何参数使用git push

```
git push -u origin master
```



不带任何参数的git push，默认只推送当前分支，这叫做simple方式，还有一种matching方式，会推送所有有对应的远程分支的本地分支， Git 2.0之前默认使用matching，现在改为simple方式

```
git push
```



如果想更改设置，可以使用git config命令。git config --global push.default matching OR git config --global push.default simple；可以使用git config -l 查看配置



不管是否存在对应的远程分支，将本地的所有分支都推送到远程主机

```
git push --all origin
```



git push的时候需要本地先git pull更新到跟服务器版本一致，如果本地版本库比远程服务器上的低，那么一般会提示你git pull更新，如果一定要提交，那么可以使用这个命令

```
git push --force origin
```

refs/for 的意义在于我们提交代码到服务器之后是需要经过code review 之后才能进行merge的，而refs/heads 不需要



以列表形式列出所有tag的版本号,显示出每个版本号对应的附加说明

```
git tag -l -n
```



## git remote add

这里主要 如何将一份已经写好的代码提交到两个git远端为例（远端只需要建立仓库就可以吧本地的代码推上去）， 更好地理解 git remote add 这句；

首先要明白一句代码的意思，以 github 最经常的提示为例：

```
git init

git add README.md

git commit -m “first commit”

git remote add origin your_first_git_address

git push -u origin master
```

git init, git add 和 git commit 都是前期的准备, 相当于将你本地的文件都上传到了本地仓库，但是还没有向远端仓库提交；



这时执行 git remote add 那句话，就是先将本地仓库与远端仓库建立一个链接： git remote add ， 那么add什么呢？ origin就是你为远端仓库所起的名字，一般都是叫origin，your_first_git_address就是你的远端仓库的真实地址；



举个栗子，假设我已经存在一个文件夹，里面存放自己的代码，里面有一个文件叫README.md已经写好， 则

```
git init                                     // 初始化一个git的本地仓库

git add README.md                            // 将我的文件装上武器，准备发射

git commit -m “first commit”                 // 第一次发射，我的README.md 宝贝已经成功进入到本地仓库

git remote add origin your_first_git_address // 将第一个 git address 命名为 origin

git push -u origin master                    // origin 是远端 your_first_git_address 上的仓库， master 是本地分支
```



这时，不要动，准备再次将我的README宝贝发射到火星上去，但是因为我的文件已经存在与本地仓库了，因此我就不需要再多余地commit等，只需要将另一个远端仓库与本地仓库建立一个连接就可以了

```
git remote add origin your_second_git_address  // 将第二个git address 命名为 origin

git push -u origin master                      // origin 是远端 your_second_git_address上的仓库，master 是本地分支
```

至此，就将一份代码上传到了两个远端仓库，但是注意你仍然时只有一个本地仓库哦



## git commit -m 如何支持换行

```
git commit -m '       // 在这里直接输入回车即可

> 1、第一项改动         // 以下的这些真正的comment可以在其他文本编辑器中写好粘贴过来

> 2、

> i、第二项第一个小改动

> ii、第二项第二个小改动

> iii、第二项第三个小改动

> 3、第三项改动

> 4、第四项改动

> '                   // 输入这个结尾单引号后，再输入回车即可完成本次commit的提交
```



## stash

```
git stash 命令使用场景

1.当正在dev分支上开发某个项目，这时项目中出现一个bug，需要紧急修复，但是正在开发的内容只是完成一半，还不想提交，这时可以用git stash命令将修改的内容保存至堆栈区，然后顺利切换到hotfix分支进行bug修复，修复完成后，再次切回到dev分支，从堆栈中恢复刚刚保存的内容。

2.由于疏忽，本应该在dev分支开发的内容，却在master上进行了开发，需要重新切回到dev分支上进行开发，可以用git stash将内容保存至堆栈中，切回到dev分支后，再次恢复内容即可。

总的来说，git stash命令的作用就是将目前还不想提交的但是已经修改的内容进行保存至堆栈中，后续可以在某个分支上恢复出堆栈中的内容。这也就是说，stash中的内容不仅仅可以恢复到原先开发的分支，也可以恢复到其他任意指定的分支上。git stash作用的范围包括工作区和暂存区中的内容，也就是说没有提交的内容都会保存至堆栈中。
```



## 查看stash 列表

```
git stash list
```



## 保存Stash

```
git stash

或者

git stash stashName
```



## 恢复Stash

```
git stash pop

或者

git stash pop stash@{0}

或者

git stash pop stashName
```



## 清空stash列表

```
git stash clear
```



## 删除指定的stash

```
git stash drop stash@{0}
```



## 修改.gitignore后生效

在使用git的时候我们有时候需要忽略一些文件或者文件夹。我们一般在仓库的[根目录](https://so.csdn.net/so/search?q=根目录&spm=1001.2101.3001.7020)创建.gitignore文件

在提交之前，修改.[gitignore](https://so.csdn.net/so/search?q=gitignore&spm=1001.2101.3001.7020)文件，添加需要忽略的文件。然后再做add commit push 等

但是有时在使用过称中，需要对.gitignore文件进行再次的修改。这次我们需要清除一下缓存cache，才能使.gitignore 生效。

具体做法：

```
git rm -r --cached . #清除缓存

git add . #重新trace file

git commit -m "update .gitignore" #提交和注释

git push origin master #可选，如果需要同步到remote上的话
```





## Git全局递归忽略.DS_Store

Mac下，只要在Finder访问过的文件夹，都会生成一个.DS_Store的文件，Mac用它来存储当前文件夹的一些Meta信息。对于Git来说，不经意间总是会干扰到其他正常的提交和本地仓库状态，是很烦恼，找了半天，终于有一个比较好的办法处理了



流程大致是这样的：

- 对于项目内已经提交了.DS_Store到仓库的情况

```
 find . -name .DS_Store -print0 | xargs -0 git rm --ignore-unmatch
```

 搜索一下项目内所有的.DS_Store，全部rm掉，然后再push一把



- 对于今后的项目，做全局的配置

如果没有~/.gitignore_global文件，echo也会为你生成一个，这里的主要目的是覆盖所有可能的OS X版本生成的.DS_Store，逐一执行一次就可以了，之后cat一下看是否正常写入了

```
echo ".DS_Store" >> ~/.gitignore_global

echo "._.DS_Store" >> ~/.gitignore_global

echo "**/.DS_Store" >> ~/.gitignore_global

echo "**/._.DS_Store" >> ~/.gitignore_global
```

然后设置一下全局的配置

```
git config --global core.excludesfile ~/.gitignore_global
```



- 附1：

1、删除当前目录下的所有的.DS_Store：

```
find . -name '*.DS_Store' -type f -delete
```

2、禁止.DS_store生成：

```
defaults write com.apple.desktopservices DSDontWriteNetworkStores -bool TRUE
```

3、恢复.DS_store生成：

```
defaults delete com.apple.desktopservices DSDontWriteNetworkStores
```



- 附2：.gitignore的语法

**gitignore规范**

```
所有空行或者以注释符号 ＃ 开头的行都会被 Git 忽略。

可以使用标准的 glob 模式匹配。

匹配模式最后跟反斜杠（/）说明要忽略的是目录。

要忽略指定模式以外的文件或目录，可以在模式前加上（!）取反。
```

**glob模式要点**

```
*          任意个任意字符,

[]         匹配任何一个在方括号中的字符,

?          匹配一个任意字符，

[0-9]      匹配字符范围内所有字符
```



## git 强制放弃本地修改（新增、删除文件）

本地修改了一些文件，其中包含修改、新增、删除的，不需要了想要丢弃，于是做了git check .操作，但是只放弃了修改的文件，新增和删除的仍然没有恢复，使用如下命令可以放弃所有修改、新增、删除文件：

```
git checkout . && git clean -df
```

git checkout .             // 放弃本地修改，没有提交的可以回到未修改前版本

git clean                     // 从工作目录中移除没有track的文件.

通常的参数是git clean -df: -d表示同时移除目录, -f表示force, 因为在git的配置文件中, clean.requireForce=true, 如果不加-f, clean将会拒绝执行.



## git 永久删除文件

**背景**： commit了某些私密的文件或者不必要追踪的大文件等，想要将这些文件永远从版本控制系统中删除；



**步骤一：**

从资料库中清除文件

```
git filter-branch --force --index-filter 'git rm --cached --ignore-unmatch path-to-your-remove-file' --prune-empty --tag-name-filter cat -- --all
```

其中, path-to-your-remove-file 就是你要删除的文件的相对路径(相对于git仓库的跟目录), 替换成你要删除的文件即可. 注意一点，这里的文件或文件夹，都不能以 '/' 开头，否则文件或文件夹会被认为是从 git 的安装目录开始。

如果你要删除的目标不是文件，而是文件夹，那么请在 `git rm --cached' 命令后面添加 -r 命令，表示递归的删除（子）文件夹和文件夹下的文件，类似于 `rm -rf` 命令。

如果你看到类似下面这样的, 就说明删除成功了:

```
Rewrite 48dc599c80e20527ed902928085e7861e6b3cbe6 (266/266)
# Ref 'refs/heads/master' was rewritten
```

如果显示 xxxxx unchanged, 说明repo里没有找到该文件, 请检查路径和文件名是否正确.

注意: 补充一点, 如果你想以后也不会再上传这个文件或文件夹, 请把这个文件或文件夹添加到.gitignore文件里, 然后再push你的repo.



**步骤二：**

推送我们修改后的repo

以强制覆盖的方式推送你的repo, 命令如下:

```
git push origin master --force --all
```

这个过程其实是重新上传我们的repo, 比较耗时, 虽然跟删掉重新建一个repo有些类似, 但是好处是保留了原有的更新记录, 所以还是有些不同的. 如果你实在不在意这些更新记录, 也可以删掉重建, 两者也差不太多, 也许后者还更直观些.

执行结果类似下面:

```
Counting objects: 4669, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (4352/4352), done.
Writing objects: 100% (4666/4666), 35.16 MiB | 51 KiB/s, done.
Total 4666 (delta 1361), reused 0 (delta 0)
To https://github.com/defunkt/github-gem.git
 + beb839d...81f21f3 master -> master (forced update)
```

为了能从打了 tag 的版本中也删除你所指定的文件或文件夹，您可以使用这样的命令来强制推送您的 Git tags：

```
$ git push origin master --force --tags
```



**步骤三: 清理和回收空间**

虽然上面我们已经删除了文件, 但是我们的repo里面仍然保留了这些objects, 等待垃圾回收(GC), 所以我们要用命令彻底清除它, 并收回空间.

命令如下:

```
$ rm -rf .git/refs/original/
$ git reflog expire --expire=now --all
$ git gc --prune=now
Counting objects: 2437, done.
# Delta compression using up to 4 threads.
# Compressing objects: 100% (1378/1378), done.
# Writing objects: 100% (2437/2437), done.
# Total 2437 (delta 1461), reused 1802 (delta 1048)
$ git gc --aggressive --prune=now
Counting objects: 2437, done.
# Delta compression using up to 4 threads.
# Compressing objects: 100% (2426/2426), done.
# Writing objects: 100% (2437/2437), done.
# Total 2437 (delta 1483), reused 0 (delta 0)
```

 

## git 如何设置代理，如何取消代理

```
1. 设置scoks代理
全局配置代理
git config  --global http.proxy socks5://127.0.0.1:10808
git config  --global https.proxy socks5://127.0.0.1:10808
 
当前项目文件夹内
git config  --local http.proxy socks5://127.0.0.1:10808
git config  --local https.proxy socks5://127.0.0.1:10808

2.设置http代理
全局配置代理
git config  --global http.proxy http://127.0.0.1:1081
git config  --global https.proxy https://127.0.0.1:1081

当前项目文件夹内
git config  --local http.proxy http://127.0.0.1:1081
git config  --local https.proxy https://127.0.0.1:1081


取消代理设置
git config --global --unset http.proxy
git config --global --unset https.proxy
```

