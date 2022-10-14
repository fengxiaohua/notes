# 一、本地项目到github

1. 项目内操作

```
1. git init
执行操作，会默认将本项目作为默认master分支
2. git add .
将项目中所有文件添加到仓库
3. git commit -m '注释'
接着，把文件提交到仓库，双引号内是提交注释
```

2. 创建github仓库并Create a new repository

3. 关联github仓库
   
   复制仓库地址，将本地仓库与github仓库连接
   
   git remote add origin + github仓库地址
   
   git push -u origin master
   
   如果出问题，可能创建项目有文件造成了冲突，可以强制覆盖
   
   **git push -f origin master**

https://blog.csdn.net/weixin_44462664/article/details/109562003

如果自己测试想用github的服务器，可以单独成立一个预览分支用于管理dist

```
git branch gh-pages   //创建gh-pages分支
git checkout gh-pages  //切换到gh-pages分支
git add -f dist     //强制把dist文件夹提交到github
git subtree push --prefix dist origin gh-pages  //把dist文件夹单独部署到gh-pages分支
```

更新gh-pages 分支

```
git commit -am "Save local changes"
git checkout -B gh-pages
git add -f dist
git commit -am "Rebuild website"
git filter-branch -f --prune-empty --subdirectory-filter dist && git push -f origin gh-pages && git checkout master
```

https://blog.csdn.net/jessicaiu/article/details/83818412 git 仓库拆分
