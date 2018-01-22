---
title: 搭建博客
---
今天通过hexo+github搭建了个人博客，分享我的搭建博客的过程以及遇到的坑：

首先，[按照这个来]("https://www.jianshu.com/p/4eaddcbe4d12")。然后，在这个配置中
deploy:
type: git
 repo: https://github.com/username/username.github.io.git

注意：这里用https访问的话可能会发生：
*fatal: could not read Username for 'https://github.com'*

解决方法是：
1.通过ssh来访问


2.修改为：https://username:password@github.com/username/username.github.io.git

为防止换电脑导致网站资料丢失，把\source\_posts的内容上传至github，步骤为
git init
git add README.md
git commit -m "first commit"
git remote add origin git@github.com:simlefeng/blog.git
git push -u origin master