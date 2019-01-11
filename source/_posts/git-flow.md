---
title: git flow 使用
date: 
tags:
    - git flow
---

# feature开发阶段

## 1、新建项目

* 方式一：管理员在gitlab新建项目后，可以在远程master分支，拉出一个远程develop分支，这样开发者将项目clone下来之后，开发者将develop分支拉到本地。**推荐这种方式**，管理员在创建远程develop分支的时候，可以将其设置为受保护，这样开发者就不能随意向develop分支上提交代码了。

* 方式二：管理员在gitlab新建项目时不创建远程develop分支，而是开发者clone项目后，执行git flow init 会在本地创建develop，然后再将develop分支push到远端。


## 2、feature开发

(1) 开发者clone项目后，进入项目路径，在Terminal中执行git flow init。Terminal中会提示让输入一些内容，我们一路回车就好。

如果之前本地没有develop分支，git flow init命令会在本地创建一个develop分支，并切换到develop分支：

```
git branch develop
git checkout develop
```
> 注:不要直接在develop和master分支上进行代码开发

(2) 调用git flow feature start {feature-name}命令，以开发名为test1的feature为例：
```
git flow feature start test1
```
这个命令的效果是:

- A new branch 'feature/test1' was created, based on 'develop'
- You are now on branch 'feature/test1'

(3) 在feature/test1分支写代码，写完代码之后提交：
``` 
git add .
git commit
```

(4) 将feature/test1分支进行发布：
```
git flow feature publish test1
```
在这个命令中，执行的效果

* The remote branch 'feature/test1' was created or updated
* The local branch 'feature/test1' was configured to track the remote branch
* You are now on branch 'feature/test1'

> 注：如果一个feature/test1是多个开发者共同完成，另一个开发者可以调用如下命令：
```
git flow feature track feature/test1
```
命令效果如下：

* A new remote tracking branch 'feature/test1' was created
* You are now on branch 'feature/test1'

开发完成之后也是采用git flow feature publish test1命令，将更新的内容发布到远程feature/test1分支。

# merge请求

## 1、发送merge请求

在gitlab网页上进行，需要选择源分支（feature分支）、目标分支（develop分支）、assignee、milestone、labels、description等，然后提交merge请求，并通知assignee给你进行merge

## 2、收到merge请求
当指定的assignee收到merge请求后，需要首先搞清楚merge请求中提到的issue。

然后进行review，包括codes、lint、check with design paper、function、冲突等。

如果没有问题，点merge进行合并，关闭这些相关的issues；如果发现问题，通知merge请求人处理，请求人处理之后，通知assignee再次review。

当分支被merge完成之后，merge申请人可以调用如下命令完成这个feature的开发：
```
git flow feature finish test1
```
其效果如下：

* The feature branch 'feature/test1' was merged into 'develop'
* Feature branch 'feature/test1' has been locally deleted; it has been remotely deleted from 'origin'
* You are now on branch 'develop'

# release

需要一个重要的人进行release。

如果项目不是Emergency：

1、调用命令：
```
git flow release start v0.0.1
```
本地生成release/v0.0.1分支

2、调用命令：
```
git flow release publish v0.0.1
```
调用效果：
- The remote branch 'release/v0.0.1' was created or updated
- The local branch 'release/v0.0.1' was configured to track the remote branch
- You are now on branch 'release/v0.0.1'

生成远程release/v0.0.1分支

3、SQA测试

SQA测试远程release/v0.0.1分支上的程序，如果存在问题，通知开发者fixbugs。

开发者收到fixbugs的信息，在本地release/v0.0.1分支上修改，调用git flow release publish v0.0.1命令将变化提交到远程release分支。

重复上面过程，直到没有bugs。

然后前面讲到的重要的人将远程release分支先分别合并到远程release分支和远程develop分支，利用gitlab进行。

并结束这个版本的release调用如下命令：
```
git flow release finish v0.0.1
```
其执行的效果如下：

- Release branch 'release/v0.0.1' has been merged into 'master'
- The release was tagged 'v0.0.1'
- Release tag 'v0.0.1' has been back-merged into 'develop'
- Release branch 'release/v0.0.1' has been locally deleted; it has been remotely deleted from 'origin'
- You are now on branch 'develop'





