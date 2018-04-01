#### 拷贝远程分支到本地
```
// 拷贝远程仓库
git clone xxxxx（远程仓库地址）

// 拷贝远程仓库的制定分支
git clone -b <分支名称> <远程仓库地址> [目录名称]
```

#### 查看分支信息
```
git branch

// -vv 可以查看详情参数，如每个分支关联的远程分支
git branch -vv
```
#### 切换分支
```
git checkout xxxx(分支名)
```

#### 新建本地分支
```
// 新建分支，但不会切换分支，仍停留在当分支
git branch xxxx（分支名）
// 切换分支
git checkout xxx 

// 新建并切换
git checkout -b xxxx（分支名）
```

#### 新建远程分支并关联
```

git checkout -b abc origin/abc

// 如果不存在远程分支，先新建分支，再推送当前分支到远程分支
git checkout -b abc
git push -u origin abc
```

#### 本地分支关联已有远程分支
```
// 第一种
git checkout -b abc origin/abc 

// 第二种 先切换，后关联
git checkout -b abc 
git branch -u origin/abc
```

#### 删除分支
```
// 保证删除分支不能是当在所在分支
git branch -D abc
```

#### 提交修改，并推送
```
// 添加到提交列表中
git add xxx
// 提交，并写上作者
git commit -m 'something' --author=xxx


// 或者在commit 后面直接加上文件，不需要执行add操作
git commit -m 'something' --author=xxx xxx.js xxx.css

// 最后推送到远程
git push
```

#### 撤回修改
```
// 撤回未执行git add 的文件的修改
git checkout xxx # 撤回未执行git add 的文件的修改

// 撤回已执行git add的文件的修改
git reset xxx
```

#### 查看提交日志
```
git log

// 查看更详细
git log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit
```

#### 分支合并
```
git merge abc(分支名)

// 只合并某一次提交
git cherry-pick <commit id>
```

#### 回滚提交,推送
```
// 查看撤回的commit
git log
git revert commitId
git push
```

#### 简写
git有一个全局的配置文件`~/.gitconfig`，还有一个项目的配置文件`.git/config`。

对于命令的简写，可以放在`~/.gitconfig`里：
```
[color]
    ui = true
[alias]
    st = status
    ci = commit
    co = checkout
    br = branch -vv
    lg = log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit
```

对于`author`的选项配置，可以放在项目目录下的配置文件里，不放在全局，是因为不同的项目针对的作者可能是不同的账号：
```
[user]
    name=xxx
    email=xxxx@xx.xx
```





