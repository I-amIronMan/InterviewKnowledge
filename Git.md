### Git常用命令
#### git全局设置
1. git config --global user.name “XXX”(用户名)
2. git config --global user.email “XXX”(邮箱)
3. git config --list

#### 本地仓库git常用命令
##### 本地初始化git仓库  
git init

##### 克隆远程仓库  
git clone 【远程仓库地址】

##### 本地仓库常用命令
+ git status：查看当前文件的状态
+ git add "文件名"/*：将修改的文件加入暂存区
+ git reset "文件名"：讲暂存区的文件取消暂存
+ git commit -m "XXX" 文件名：将指定文件提交版本库(本地仓库)，XXX表示提示信息
+ git log：查看日志

#### 远程仓库常用命令
+ git remote / git remote -v：查看远程仓库
+ git remote add 【shortname】【url】：添加远程仓库，将本地仓库和指定远程仓库相关联
+ git clone【url】：克隆远程仓库到本地
+ git pull origin master：拉取远程仓库的文件到本地
+ git push：将本地仓库的文件推送到远程仓库

#### git分支操作
+ git branch：查看分支  
git branch：列出所有的本地分支  
git branch -r：列出所有的远程分支  
git branch -a：列出所有的本地分支和远程分支  
+ git branch 【name】：创建分支
+ git checkout 【name】：切换分支
+ git push 【shortname】【name】：将本地分支推送至远程仓库分支
+ git merge 【name】：合并分支；输入:wq推出分支合并信息编辑
+ 合并分支时冲突解决:在不同分支下修改相同的文件，合并时会产生冲突，git commit -m "XXX" [文件名] -i

#### git标签操作
+ git tag：列出已有的标签
+ git tag【name】 创建标签
+ git push 【shortname】【name】
+ git checkout -b 【branch】【name】 检出标签

#### git冲突解决
1. git config pull.rebase false
2. git pull origin master
3. 代码里有冲突的部分会显示：
  + <<<< HEAD/=====/>>>>> 版本
  + 从<<<< HEAD/到=====是本地代码，从=====到>>>>> 版本是拉取下来的代码
  + 手动解决冲突
4. 冲突解决
