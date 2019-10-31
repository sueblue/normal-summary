1. 删掉当前的commit 
   + git reset --hard head^ 删除工作空间改动代码

2. 删除origin上的最新一条commit
   + git reset --hard head^
   + git push origin master -f 
    
        删除远程仓库的最新一次提交的时候不需要保留历史记录

3. 删除某次历史提交 
    + git log - 查找到想要删除的某次提交的commit id
    + git rebase -i <提交的commitid>^ - **^**,意思是commit id的前一次提交。bash里边编辑一下
    + git push origin master -f 

4. 修改当前的commit 
   +  git commit --amend -m "新的提交信息"

5. add了错误文件，如何unstage
   + git reset HEAD path/to/file
   + git status 

6. 提取commit到另一分支    
   + 首先checkout到此分支，然后cherry-pick xxx 即可
  