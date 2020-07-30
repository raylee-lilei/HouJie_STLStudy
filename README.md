# HouJie_STLStudy

##  git 记录

1. 本地新建文件HouJie_STLStudy

   进入git  bash here

   $ git  init

   $ echo "# HouJie_STLStudy" >> README.md

2. 在GitHub上新建仓库HouJie_STLStudy

5. 可以开始编写代码了

   $ git status

   $ git add .

   $ git commit -m  "first commit"

   $ git remote add origin  https://github.com/raylee-lilei/HouJie_STLStudy.git

   ​		&emsp;$ git remote -v   可以查看提交origin

   ​		&emsp;$ git remote rm origin  可以删除重新提交
   
   $  git push origin -u master
   
4. 更新文件内容之后

   $  git status

   $ git add .

   $ git commit -m  "update"

   $ git pull --rebase origin master   //合并内容

   $  git push origin -u master

![image-20200730215735416](https://raw.githubusercontent.com/raylee-lilei/PicGoImage/master/imgimage-20200730215735416.png)