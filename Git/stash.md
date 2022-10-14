## 应用场景

在某分支开发，但是需要将开发内容缓存起来，等待需要使用的时候使用

1. `git stash`

   能够将所有未提交的修改（工作区和暂存区）保存至堆栈中，用于后续恢复当前工作目录

2. `git stash save 'xx'`

   作用等同于git stash，区别是可以加一些注释

3. `git stash list`

   查看当前stash中的内容

4. `git stash pop`

   将当前stash中的内容弹出，并应用到当前分支对应的工作目录上。
   注：该命令将堆栈中最近保存的内容删除（栈是先进后出）

5. `git stash apply stashName`

   可以使用git stash apply + stash名字（如stash@{1}）指定恢复哪个stash到当前的工作目录

   并不删除堆中内容

6. `git stash drop stashName`

   从堆栈中移除某个指定的stash

7. `git stash clear`

   清除堆栈中的所有 内容

8. `git stash show stashName`

   查看堆栈中最新保存的stash和当前目录的差异