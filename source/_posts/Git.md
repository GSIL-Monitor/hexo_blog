#### 安装

```
sudo apt-get install git
```

#### 拉取git 库

```
git clone git@github.com:仓库地址.git
```

#### 添加、删除、提交暂存区

```
git pull
git add ‘目标git_path下的某个文件名’
git rm -f ‘目标git_path下的某个文件名’
git commit -m "备注信息"
git push -u origin 分支名
```

#### 分支管理

```
> 远程
    git branch -r
    git checkout -b 本地分支名 origin/远程分支名
    git push origin --delete 远程分支名
> 本地
    git branch
    git checkout 本地分支名
    git branch -d 本地分支名
```

#### 日志分析

```
git status                              # 获取当前分支状态
git reset --hard 版本号前几位/HEAD^       # 版本回退
git log --graph                         # 查看分支合并图
git rev-parse HEAD                      # 获取commitID号
```
