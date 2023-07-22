##### Git基本命令

```shell
git init 初始化项目
git status 查看当前目录下文件的状态
git add （文件名 | .）添加到暂存盘，文件名代表某文件，"."代表所有文件
git commit -m "备注"  提交到git本地仓库，产生新版本，引号中的备注必须写
git log  查看所有提交的记录
git clone 链接  克隆远程仓库，进行连接
git checkout -b [branch]  新建一个分支，并切换到该分支
git checkout [branch]  切换到某分支
git branch -d [branch-name]  删除分支
git merge [branch]  合并指定分支到当前分支
git push  将本地仓库push到远程仓库

git diff 	# 用于比较工作区和暂存区的区别
git diff --cached	# 比较的是暂存区和版本库的区别
git diff HEAD 	# 查看工作区和版本库的区别，但这三个都需要文件已经纳入Git的管理才可以看出区别
```



##### 分支管理命令

```shell
git branch		# 查看本地所有分支
git checkout main	# 切换到指定分支
git checkout -b master	# 创建一个本地分支，并切换到此分支上
git branch -d master	# 删除一个本地分支，要先切换到其他分支上
git push -f maze-zero master	# 将本地master分支推送到远程
git push maze-zero --delete master	# 删除远程分支
```



##### 一般Git执行流程

```shell
git init 	# 初始化本地仓库
git clone <repository URL>	# 从GitHub上克隆一个仓库
# 之后可以添加并且提交到本地库
git push <remote><branch> -f 	# 向远程仓库强制提交
```

