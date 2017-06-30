---
title: deploy-hexo-to-VPS
date: 2017-06-30 15:58:40
tags: hexo deploy
---
简要总结本博客所使用的版本管理方法及部署

1. Hexo 基本使用方法按下不表，官网文档还是比较详细的。

2. 部署选择了 `hexo-deployer-git`，然后在 *_config.yml* 中重点添加以下配置：
```yml
deploy:
  type: git
  repo: https://github.com/DevinXian/devinxian.github.io.git
   branch: master
```

 说明： `hexo-deployer-git` 会把 `hexo deploy` 生成的 *.deploy\_git* 目录下的文件推送到 github，通过 username.github.io 可以访问最终结果。但是注意项目源文件并没有提交到版本库，可以使用建立新分支的方法，将源代码推送到 github 的分支源码上去（注意忽略 *node_modules* 及不需要提交的文件）

3. 如何部署到 VPS——推荐使用ssh方式；ssh key 可以使用 scp 等工具加账号密码直接复制过去，现在很多 VPS 服务商在初始化 VPS 时候就提供了注入 ssh key 的入口，省却后续麻烦。此时 *_config.yml* 配置可以修改为如下格式：
```yml
deploy:
  type: git
  repo: {home}@{vps ip or hostname}:{distination directory} # 比如 xixi@luofeng.me:www/blog.git
  branch: master 
  # 其实就是将本地的 hexo 输出最终结果代码推送到 VPS 仓库
```

 VPS 上 repo 对应目录要存在，而且需要用 `git init --bare` 命令初始化为 git repo，`--bare` 的意思是本目录不支持修改，不是 git 工作目录，只是一个版本记录库，接受外部推送和历史查看。如上示例，则对应用户名 xixi， 主机名 luofeng.me，目录为 *~/www/blog.git*（以下称 *blog.git*）。  
 本地推送的时候，hexo 生成的最终文件会落入 *blog.git*。但 *blog.git* 不是 git 工作目录（要想读取文件，只能将版本库检出为工作目录）。  

4. 推送完成，我们什么时候进行部署呢？没错，git hooks，在 *blog.git/hooks* 下编写 *post-receive* bash 脚本，并添加执行权限，文件内容示例如下：
```bash
#!/bin/bash
git --work-tree=/var/www/blog --git-dir=~/blog.git checkout -f 
```

 意思其实就是刚才提到的，将 git 版本库内容检出到一个工作目录。

5. 最后一步，使用 nginx 对检出工作目录进行静态文件 serve 就可以，保证 nginx 运行时有权限读取或执行已检出的工作目录就好。

6. 过程很简单，参考资源很多，就不列出了，谢谢知识的散播者。
 