一。第一次提交代码到github：
   1.git init 
   2.git add 文件名字(如果全部提交则加 . )
   3.git commit -m "提交说明"

4. git remote add origin git@github.com:qiangyongyang/test-code.git
   若是失败，原因是出现错误的主要原因是github中的README.md文件不在本地代码目录中
     执行：git pull --rebase origin master
   5.git push -u origin master

/******************二次提交***********************/
1.git init 
2.git add .
3.git commit -m " "
4.git remote add origin git@github.com:qiangyongyang/test.git
5.git push origin master

二.修改并再次提交代码：
1.git status:可以让我们时刻掌握仓库当前的状态，上面的命令输出告诉我们，仓库里的文件被修改过了，但还没有准备提交的修改
2.git diff:	查看具体修改了什么内容
3.git log:查看版本控制系统中提交的历史记录

三.回退及返回版本
4.回到过去：git reset --hard 版本号（当前版本：head; 上一版本head^; 以此类推）
5.返回将来：git reset --hard 版本号 (去上面找开始的版本号，只写前几位就好了)
   /*若是关机了，找不到开始的版本号*/
	git reflog: 记录每一次命令=>可以查看开始版本的id了
四.撤销修改
1.git checkout -- 文件名：这个文件回到最近一次git commit或git add时的状态。(还在工作区)
2.若是已经git add 到了暂存区，但是还未commit：
   git reset HEAD -- 文件名      ：把暂存区的修改撤销掉，重新放回工作区
   git checkout -- 文件名：    在工作去撤销

五.删除文件：
1.现在本地删除，再去版本库中删除：
rm  文件名
git rm 文件名
git commit -m "提交说明"
2.如果删错了，版本库里还有，可以把误删的文件恢复到最新版本：
git checkout -- 文件名



#  