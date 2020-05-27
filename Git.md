# Git 使用向导
## 3个容器：
* 工作区 ：
   * 当前项目目录 文件分为两种状态： 已跟踪 、 未跟踪
   * 已跟踪的文件为以及被纳入Git版本库中的文件，具有快照记录，
   * 已跟踪的文件在一段时候后有 3种 状态： 已提交 、 已修改 、 已暂存
* 暂存区：
    * 当生成Git对象后，从Git版本库中放入Git对象所对应的文件的占存容器
* Git版本库：
    * Git对象的直接存储容器

## 3个对象：
 * Git对象(blob)
    > 将目标文件一git对象的方式存储于Git版本库的objects中，使用hash算法生成一个唯一的SHA-1值来标识当前文件的Git对象.        
 * 树对象(tree)
    > 将多个Git对象（一个项目版本的多个文件）对应的SHA-1索引生成一个树对象，记录文件的层次结构
                  同样使用SHA-1值作为索引，存储于Git版本库中
 * 提交对象(commit)
    > 将一个树对象以及上一个commit对象使用注释打包(标明当前提交信息：时间、地点、人物)，
                      生成一个SHA-1值标注的文件的索引存储于Git版本库中，成为最终的版本备份
## Git 基本操作流程：
 1. 初始化git： 
    > git init
 2. 选择文件生成Git对象：
    > git add ./
### 细节： 
* 将对应文件生成Git对象，并保存到Git版本库(只要工作区中的文件被修改过，就会生成新的Git对象)：
    > git hash-object -w 文件名   
* 将更新后的Git对象(多个)放入暂存区：
    > git update-index 
* 生成一个树对象，并生成一个commit对象并提交：
    > git commit -m "注释内容"
    * 分为2步：
        1. 生成快照： 根据暂存区(index)中的文件SHA-1索引生成树对象，返回生成树对象的SHA-1值，
                     只要文件有改动就会生成新的树对象
            > git write-tree 
        2. 生成commit对象：
            > git commit-tree <树对象SHA-1> -p <上一个commit对象SHA-1>

## Git 高层命令(RCUD)：
* 初始化
    > git init 
* 查看git状态
    > git statue 
* 查看那些更新还没有被暂存
    > git diff --cached 
* 查看那些修改 以及 被暂存了未被提交的部分
    > git diff --staged 
* 将对象提交到暂存区
    > git add ./ 
* 将暂存区提交到版本库
    > git commit -m "注释"
* 直接将工作区中的文件提交到版本库，跳过暂存区
    > git commit -a  
* 查看暂存区内容
    > git ls-files -s 
* 打开提交日志
    > git log 
* 将文件从工作目录中删除并提交删除状态到暂存区
    > git rm <文件名>  
* 将文件从工作目录中 移动 或者 重命名 并提交修改状态到暂存区
    > git mv <文件名>   

## 分支管理：
> 实质：指向最新提交对象的可变指针
* 不加参数，得到分支列表
    >git branch 
* 分支名 ： 在当前的提交对象上，创建一个可移动的新指针，创建后不会自动切换到新指针上
    >git branch 
* 新建一个分支并指向指定的提交对象
    > git branch <分支名> <commit对象SHA-1> ： 
* 查看每个分支的最后一次提交
    > git branch -v 
* 将HEAD指针切换到新分支上
    > git checkout <分支名> ： 
* 新建一个分支，并切换到这个分支
    > git checkout -b <分支名>
* 强制删除分支，不能直接删除当前所在的分支，需要切换到其他分支
    > git branch -D <分支名> ： 
* 合并分支:
   * 快进合并：
        > git merge 分支名 
        * 如果当前分支和将要合并的分支处于同一分支线上(父子或祖孙关系)，则启用快速合并，  
        * 将master指向合并的分支上需要切回需要合并的分支，再执行合并，
        * 执行后主分支(master)会和新分支指向同一个commit 且 之前主分支指向的commit会得以保留
   * 典型合并：
        * 如果当前分支和将要合并的分支没有处于同一分支线上，则启用典型合并，Git需要做额外工作

### 注意:
   * 切换分区所改变的 对象： HEAD 指针 、  暂存区 、 工作目录   
   *  切换分支会改变当前工作目录中的内容，所以每次在切换分支之前最好提交当前分支
   * 在切换分支时，如果当前分支有未跟踪的文件，则分支可以切换成功，
   * 未跟踪或未提交的文件在切换分支后得以保留，但可能会污染分支
   * 文件被改动后未提交 ，则切换分支不会成功
   

## Git 存储：
   > 如果不想当前工作仅仅做了一半而提交，然后切换分支的话，Git存储命令会帮助储存当前未提交的文件(被改动的)
  * 将未提交的修改保存到一个栈上
    > git stash : 
  * 查看栈中存储的文件
    > git stash list 
  * 从栈中指定位置恢复文件，如果不指定位置默认为栈顶，而且不会删除栈中文件
    > git stash apply <栈号(可选)>
  * 立即弹出栈顶的文件到工作区，并且将那个文件从栈中删除
    > git stash pop
  * 删除栈中指定的位置的文件
    > git stash drop <栈号> 

## Git 撤回提交：
* 工作区：
    * 撤回工作目录中的刚刚保存的文件
        > git checkout -<要撤回修改的文件名>
* 暂存区：
    * 撤回暂存区中刚刚放入的文件
        > git reset HEAD <暂存区中要撤回的文件名>
* 版本库：
    * 撤销提交了的commit对象(因为提交内容有误，注释写错等)
        > git commit --amend  
                                         
        * 撤销上一次提交，回到写入注释的地方，重新提交当前暂存区的内容，
        可使用:q! 退出注释，然后重新进行提交

## Git 撤回分支：
* 携带撤回到某一个分支
    > git reset --soft <提交对象SHA-1>  
    * 移动HEAD指向的提交对象 ,会携带分支一起移动，
    * 本质上是撤销了上一次的commit命令,但是重新提交后，错误的提交对象不会被删除，
    * 这个命令只更改HEAD以及分支指向的commit对象,不会修改工作区和暂存区的内容  
     
* 携带暂存区，撤回到某一个分支
    > git reset --mixed <提交对象SHA-1> :  
    * 和上个命令相似 ，不仅更改HEAD以及分支指向的commit对象，而且会将暂存区修改为当前HEAD指向的commit 
* 携带工作目录、暂存区，撤回到某一个分支
    > git reset --hard  <提交对象SHA-1>
    * 撤销最后的提交，并将 暂存区和工作区 修改为当前HEAD指向的commit对象(不推荐)
    
## 使用Github远程仓库
>  远程仓库是指托管在因特网或其他网络中的你的项目的版本库。你可以有好几个远程仓库，
  通常有些仓库对你只读，有些则可以读写。
### 远程协作基本流程 
* 项目经理操作
   1. 使用命令创建本地仓库
   2. 为远程仓库配置别名 和 用户信息
       * 添加一个新的远程Git仓库，将次仓库设置一个别名：
            > git remote add <仓库别名> url
       * 查看 远程仓库别名 对应的URL：
            > git remote –v
       * 查看某一个远程仓库的更多信息：
            > git remote show <仓库别名>
       * 重命名仓库别名：
            > git remote rename <仓库别名> <新别名>
       * 应为某种原因，要移除一个远程仓库：
            > git remote rm <仓库别名>
   3. 推送本地项目到远程仓库
         * 推送分支到远程仓库：
            > git push <仓库别名> <分支名>
            >> 在推送分支到远程仓库后，本地仓库默认会生成 红色标注 的远程跟踪分支
   4. 获取成员提交的新内容到本地
         * 同步新数据到本地：
            > git fetch <仓库别名>
            >> 将修改同步到远程跟踪分支上
   4. 获取成员提交的新内容到本地
         * 同步新数据到本地：
            > git fetch <仓库别名>

* 项目成员操作
   1. 成员在工作目录中使用git配置自己的用户信息
      * 配置当前 工作目录 下的 用户名(name)以及邮箱(Email):
        > git config user.name <用户名>  
          git config user.email <邮箱>
      * 删除配置的默认用户信息：
        > git config --global --unset user.name  
          git config --global --unset user.email
   2. 成员克隆远程仓库到本地
      * 克隆远程仓库(将远程仓库同步到本地),不需要初始化git  
        克隆到本地的仓库别名默认为Origin
        > git clone url
      * 修改默认远程仓库名： 
        > git clone -o <自定义仓库名>      
   3. 成员推送提交到远程仓库：
      * 推送分支到远程仓库
        > git push <仓库别名> <本地分支名>  
        >> 需要成员在Github上拥有远程仓库的写入权限，并且当前分支没有被推送过