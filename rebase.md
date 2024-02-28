# git rebase 用法详解与工作原理

以前对 `git rebase -i` 的用法一直是一知半解，一次在需要合并多个提交时刚好用到，一顿操作差点把提交都搞丢了，幸好后面顺利找回，因此记录一下学习 `rebase` 命令的过程。

## [理解 Rebase 命令](https://waynerv.com/posts/git-rebase-intro/#contents:理解-rebase-命令)

`git rebase` 命令的文档描述是 `Reapply commits on top of another base tip`，从字面上理解是「在另一个基端之上重新应用提交」，这个定义听起来有点抽象，换个角度可以理解为「将分支的基础从一个提交改成另一个提交，使其看起来就像是从另一个提交中创建了分支一样」，如下图：

![git-rebase.png](.\img\git-rebase-visual.png)

假设我们从 `Master` 的提交 A 创建了 `Feature` 分支进行新的功能开发，这时 A 就是 `Feature` 的基端。接着 `Matser` 新增了两个提交 B 和 C， `Feature` 新增了两个提交 D 和 E。现在我们出于某种原因，比如新功能的开发依赖 B、C 提交，需要将 `Master` 的两个新提交整合到 `Feature` 分支，为了保持提交历史的整洁，我们可以切换到 `Feature` 分支执行 `rebase` 操作：

```
`git rebase master `
```

`rebase` 的执行过程是首先找到这两个分支（即当前分支 `Feature`、 `rebase` 操作的目标基底分支 `Master`） 的最近共同祖先提交 A，然后对比当前分支相对于该祖先提交的历次提交（D 和 E），提取相应的修改并存为临时文件，然后将当前分支指向目标基底 `Master` 所指向的提交 C, 最后以此作为新的基端将之前另存为临时文件的修改依序应用。

我们也可以按上文理解成将 `Feature` 分支的基础从提交 A 改成了提交 C，看起来就像是从提交 C 创建了该分支，并提交了 D 和 E。但实际上这只是「看起来」，在内部 Git 复制了提交 D 和 E 的内容，创建新的提交 D' 和 E' 并将其应用到特定基础上（A→B→C）。尽管新的 `Feature` 分支和之前看起来是一样的，但它是由全新的提交组成的。

`rebase` 操作的实质是丢弃一些现有的提交，然后相应地新建一些内容一样但实际上不同的提交。

## [主要用途](https://waynerv.com/posts/git-rebase-intro/#contents:主要用途)

`rebase` 通常用于重写提交历史。下面的使用场景在大多数 Git 工作流中是十分常见的：

- 我们从 `master` 分支拉取了一条 `feature` 分支在本地进行功能开发
- 远程的 `master` 分支在之后又合并了一些新的提交
- 我们想在 `feature` 分支集成 `master` 的最新更改

### [rebase 和 merge 的区别](https://waynerv.com/posts/git-rebase-intro/#contents:rebase-和-merge-的区别)

以上场景同样可以使用 `merge` 来达成目的，但使用 `rebase` 可以使我们保持一个线性且更加整洁的提交历史。假设我们有如下分支：

```
  D---E feature
 /
A---B---C master
```

现在我们将分别使用 `merge` 和 `rebase`，把 `master` 分支的 B、C 提交集成到 `feature` 分支，并在 `feature` 分支新增一个提交 F，然后再将 `feature` 分支合入 `master` ，最后对比两种方法所形成的提交历史的区别。

- 使用 `merge`

  1. 切换到 `feature` 分支： `git checkout feature`。
  2. 合并 `master` 分支的更新： `git merge master`。
  3. 新增一个提交 F： `git add . && git commit -m "commit F"` 。
  4. 切回 `master` 分支并执行快进合并： `git chekcout master && git merge feature`。

  执行过程如下图所示：

  ![Dec-30-2020-merge-example](.\img\Dec-30-2020-merge-example.gif)

  我们将得到如下提交历史：

  ```
  * 6fa5484 (HEAD -> master, feature) commit F
  *   875906b Merge branch 'master' into feature
  |\  
  | | 5b05585 commit E
  | | f5b0fc0 commit D
  * * d017dff commit C
  * * 9df916f commit B
  |/  
  * cb932a6 commit A
  
  ```

  

- 使用 `rebase`

  步骤与使用 `merge` 基本相同，唯一的区别是第 2 步的命令替换成： `git rebase master`。

  执行过程如下图所示：

  ![Dec-30-2020-rebase-example](.\img\Dec-30-2020-rebase-example.gif)

  我们将得到如下提交历史：

  ```
  * 74199ce (HEAD -> master, feature) commit F
  * e7c7111 commit E
  * d9623b0 commit D
  * 73deeed commit C
  * c50221f commit B
  * ef13725 commit A
  ```

  

可以看到，使用 `rebase` 方法形成的提交历史是完全线性的，同时相比 `merge` 方法少了一次 `merge` 提交，看上去更加整洁。

### [为什么要保持提交历史的整洁](https://waynerv.com/posts/git-rebase-intro/#contents:为什么要保持提交历史的整洁)

一个看上更整洁的提交历史有什么好处？

1. 满足某些开发者的洁癖。
2. 当你因为某些 bug 需要回溯提交历史时，更容易定位到 bug 是从哪一个提交引入。尤其是当你需要通过 `git bisect` 从几十上百个提交中排查 bug，或者有一些体量较大的功能分支需要频繁的从远程的主分支拉取更新时。

使用 `rebase` 来将远程的变更整合到本地仓库是一种更好的选择。用 `merge` 拉取远程变更的结果是，每次你想获取项目的最新进展时，都会有一个多余的 `merge` 提交。而使用 `rebase` 的结果更符合我们的本意：我想在其他人的已完成工作的基础上进行我的更改。