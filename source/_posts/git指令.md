---
title: git指令
date: 2022-08-12 10:27:53
tags: 工具
categories: 工具
---

| 命令                                                         | 功能                                                         | 说明                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| git init                                                     | 初始化当前文件夹为仓库                                       |                                                              |
| git add &lt;file&gt;<br />git add &lt;file&gt; --edit        | 将文件添加到staged区<br />第二条是编辑后将部分内容添加到staged区 | 总共分成了工作目录(untracked)、暂存区(staged)和提交区(committed) |
| git commit -m "message"                                      | 提交暂存区的文件                                             | -m 后跟的是本次提交的信息                                    |
| git clone &lt;URL&gt;<br />git clone &lt;URL&gt; &lt;folder&gt; | 克隆远程仓库到本地第二条命令是克隆到文件夹                   |                                                              |
| git status                                                   | 查询文件状态                                                 |                                                              |
| git rm &lt;file&gt;<br />git rm --cached your-file           | 第一条：删除文件<br />第二条：从暂存区删除                   | 注意如果不用git rm直接rm的话git无法追踪到这个文件的变化      |
| git stash<br />git stash list<br />git stash pop             | 第一条：隐藏当前环境（将本地没提交的内容(git commit的内容不会被缓存 但git add的内容会被缓存)进行缓存并从当前分支移除）<br />第二条：列出被隐藏的环境<br />第三条：恢复被隐藏的环境 |                                                              |
| git mv &lt;name&gt; &lt;newname&gt;                          | 文件改名                                                     |                                                              |
| git log                                                      | 查看历史日志                                                 |                                                              |
| git tag &lt;your-tag&gt;                                     | 给最近一次提交打标签                                         |                                                              |
| git checkout &lt;file&gt;                                    | 撤销操作，用最后一次提交的文件覆盖工作目录的同名文件         | 注意这种操作无法被撤销                                       |
| git remote                                                   | 查看项目连接了什么远程仓库                                   | 后面加-v可以查看远程仓库URL                                  |
| git pull &lt;repo&gt; &lt;branch&gt;                         | 抓取远程仓库的分支内容                                       |                                                              |
| git remote add &lt;remote-name&gt; &lt;remote-url&gt;        | 手动添加远程仓库                                             |                                                              |
| git pushgit push &lt;remote-name&gt; &lt;remote-url&gt;      | 本地文件推送到远程仓库                                       | -                                                            |
| git diffgit diff &lt;file&gt;                                | 查看仓库里文件的改动                                         |                                                              |
| git blame &lt;file&gt;                                       | 查看文件的开发者更新记录                                     |                                                              |
| git branchgit branch &lt;branch-name&gt;<br />git branch -d &lt;branch-name&gt; | 第一个命令用于查看所有分支<br />第二个命令用于创建分支第三个命令用于删除分支 |                                                              |
| git checkout &lt;branch-name&gt;<br />git checkout -b &lt;branch-name&gt;<br />git checkout - | 第一条：切换到指定分支<br />第二条：创建一个分支并且切换过去<br />第三条：切换到上一个分支 |                                                              |
| git checkout &lt;tag-name&gt;<br />git checkout tags/&lt;tag-name&gt; | 给分支打上标签                                               | 当分支和tag的名字冲突，优先切换到分支，若想切换到tag则用第二条命令 |
| git branch &lt;branch-name&gt; &lt;hashcode&gt;              | 以某一次提交为基础创建分支                                   | <hash-code>是某一次提交的hash码                              |
| git merge &lt;branch-name&gt;                                | 将某一分支合并到当前分支上                                   | 若两个分支同时操作一行会冲突                                 |
| git fetch                                                    | 抓取远程仓库的数据但不合并                                   | git pull 的命令包含了git fetch和git merge，而git fetch不合并数据 |
| git rebase                                                   |                                                              |                                                              |
| git repackgit repack -d                                      | 第一条：手动将对象打包第二条：打包并删除作废的对象           |                                                              |
| git cherry-pick &lt;hashcode&gt;                             | 只想选取1个提交合并到主线                                    |                                                              |
| git grey &lt;keyword&gt;                                     | 查询关键词                                                   |                                                              |
| git reflog                                                   | 查看在git上的历史操作                                        |                                                              |
| git revert &lt;hashcode&gt;                                  | 创建一个删除某次提交的提交                                   |                                                              |
| git submodule add &lt;module-url&gt;                         | 把第三方的库作为模块引入自己的项目                           |                                                              |

