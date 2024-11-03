## 阅读说明

文档中以'<>'括起来的表示必填的参数，'[]' 表示可选参数，未做特殊说明均表示次意思。

### 一、安装git

	进入下载git下载首页地址：https://git-scm.com/

### 二、初始化
	1、配置本地基本信息
		git config --global user.name '<your name>'
		git config --global user.email '<123@abc.com>'
		以上两段脚本配置本地的用户名和邮箱，用于记录当前git的使用基本信息。可以使用
		git config --list 
		查看所有的配置信息。
	2、初始化本地项目
		在指定的目录下创建项目文件夹,此处我们定义项目名称叫learngit
		mkdir learngit
		进入到该项目文件夹内,这样所有的项目文件都存在该文件夹下
		cd learngit
		初始化项目
		git init
	经过以上步骤，初步完成了项目文件的创建
### 三、使用git
	git工作区、暂存区、本地仓库、远程仓库。对于项目文件的修改都是逐级按照顺序传递。新增、修改后的文件都保存在工作区，git会记录当前的变更状态。调用git add  将当前的文件提交到暂存区,调用git commit将当前文件提交到本地仓库，调用git push将本地仓库上传到远程仓库
	1、新增文件
	   在项目文件夹下新建一个文件readme.txt;
	   打开git bash
	   输入 git add readme.txt	将readme.txt从工作区加载到暂存区  
	   
	2、提交文件
	   在git bash中输入 git commit -m 'add readme' readme.txt将文件提交到本地的仓库中
	   
	3、丢弃修改
	   打开,并修改readme.txt ,新增一行 the document for git
	   调用 git reset --hard HEAD回退到最后更新的一版 或者 git reset --hard <commit_id>回退到对应的
	4、查看修改日志
		git log --pretty=oneline 查看分支下的所有提交记录
		git reflog -- 查看当前分支下的所有提交记录
	5、删除文件
		git rm <filename> 
		git commit -m 'delete file'
	  以上的操作也可以先在对应的分支文件目录下删除相应的文件
		git add <filename>
		git commit -m 'delete file'
	6、创建分支
		git checkout -b <branchname>  通过这个命令可以创建一个分支，并切换到该分支下
		等价于
		git branch <branchname>
		git checkout <branchname>
	7、切换分支
		git branch [--list] --查看当前所有的分支,在分支前标记为*号的表示为当前在操作的分支。
	8、合并分支
		git merge <branchname> 直接将当前分支的HEAD指针指向<branchname>分支上
		git merge --no-ff -m 'merge descriptin' <branchname> --推荐使用该种方法取合并分支，这样会保留现场
		git cherry-pick -m <pid> <commit_id> 将某个对应的commit合并到当前的分支 
	9、删除分支
		git branch -d <branchname> 根据分支名称删除对应的分支，删除分支前,被删除的分支暂存区应该为空
		有时候会提示使用参数-D来删除分支
		git branch -D <branchname>
	10、查看分支合并
		git log --graph --pretty=online --abbrev-commit 可以查看当前分支所有的合并情况
	11、保留开发现场
		git stash
		有时候我们需要停下手头上的工作去临时处理紧急的事情，但是当前分支下的项目又没有结束，而且又不能提交，可以使用这个命令。
		git stash 保留开发现场
		git checkout <branchname> 切换到其他的开发分支，开始处理紧急事物,并完成
		git checkout <current_branch> 切换会之前的开发分支
		git stash list 查看当前分支上的开发状态
		git stash pop 恢复开发现场,或者 git stash apply + git stash drop 两个命令组合 
	12、tag操作
		git tag <tagname> [commit_id] 在对应的commit版本上打上标签，默认是HEAD
		git tag -a <tagname> -m 'tag description' [commit_id] 给标签添加说明
		git tag -d  <tagname> 删除标签
		git tag -a <tagname> <commit_id> -m 'tag description' 给一个commit打tag
		针对远程标签
		git push origin <tagname> 推送指定的标签到远程
		git push origin --tags 一次性将全部标签推送到远程分支
		git push origin :refs/tags/<tagname> 删除远端的tag 
### 四、连接到远程服务
	连接远程配置需要首先获取ssh,不做详细讲解
	1、clone远程项目[ssh方式]
		git clone git@github.com:88070173/learngit.git
	2、创建remote分支
		git remote 查看远程信息
		git remote -v 查看更详细的信息
		git checkout -b dev origin/dev 将远程的dev分支拉取到本地
	3、将本地分支关联到远程分支上
		git branch --set-upstrean-to=origin/[branchname] [branchname] 将本地对应的分支关联到远程分支上

failed to push some refs to 解决方法
git pull --rebase origin master
git push -u origin master

本地创建一个分支，然后把本地分支上到远程分支
	git remote add origin  git@github.com:88070173/learngit.git

将本地分支推送到远程分支上，同时进行关联，参数u表示将本地分支与远程分支关联	
	git push -u origin master
