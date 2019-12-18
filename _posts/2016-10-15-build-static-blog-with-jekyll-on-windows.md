---
layout:     post
title:      Windows下搭建基于jekyll的静态博客
date:       2016-10-15 12:00:00
author:     "Clark Than"
category:   note
tags:       github jekyll
---


目录：
====

+ 安装ruby
+ 配置主题
+ 本机调试
+ github pages部署
+ 域名配置

_**概要**_: 写这篇文章的主要目的是记录我搭建[jekyll][jekyll site]博客的整个流程, 中间省略了git的安装配置操作



## 安装ruby

windows下须安装[RubyInstaller][rubyinstaller site], 安装完成后可使用
`ruby -v` 和 `gem -v` 可查看版本. **注意**:
由于gem默认的镜像源对与国内来说访问速度较慢，所以这里可以替换为国内的镜像地址（比如：https://gems.ruby-china.com）

```
gem sources -r xxx                           (删除原地址)
gem sources -a 'https://gems.ruby-china.com' (添加新地址)
gem install bundler                          (安装bundle)
```


## 安装配置主题
**注意**: 这一步我并没有事先安装好[jekyll][jekyll site] 然后使用 `jekyll new my-site` 来生成默认的网站.  而是在网上找了一款个人比较中意的主题（[链接](https://github.com/agusmakmun/agusmakmun.github.io)）.

使用 `git clone https://github.com/agusmakmun/agusmakmun.github.io lenbo.cc` 下载到本机.



![local git](/uploads/2016-10/local_git_directory_cp.png "本机git目录")

进入目录， 可以看到目录中__Gemfile__和__Gemfile.lock__两个文件，分别修改里面的gem源地址为`https://gems.ruby-china.com`，这两个文件中记录了该主题所需的所有插件及其版本信息，所以接下来就是依据这俩文件来安装对应版本的插件了.
执行命令`bundle install`(如果是年久失修的执行: `bundle update`). 如果安装过程中出现错误（通常是缺失build tools）则可按如下操作：
下载[DevKit][rubyinstaller site]，进入DevKit根目录依次执行以下命令(详细可参照[链接][devkit wiki])：

```
ruby dk.rb init
ruby dk.rb review
ruby dk.rb install
```

完成之后，继续完成之前 `bundle install` 的操作
这样，本机所有的环境配置就OK了
执行`bundle exec jekyll serve`,应该就能看到提示*http://127.0.0.1:4000/*



## 本机调试
这一步我按照个人喜好修改调试前面下载的模板



## github pages部署
首先登陆你的github.com账户, 新建一个repository. 然后用如下命令操作:

```
cd lenbo.cc
git init                       (进入前面调试好的程序目录)
git checkout --orphan gh-pages (新建一个没有父节点的孤儿分支gh-pages)
git remote add origin git@github.com:ClarkThan/lenbo.cc.git (建立远程关联, 将本机的git分支和github上托管的分支对应起来)
git add .                      (将所有内容添加进来[简单起见全部添加])
git commit -m "initial"        (提交git项目)
git push -u origin gh-pages    (然后就可以向远程分支进行推送了)
```

推送完成之后 在github上你就能看得和本机相同的git目录了.


![github目录](/uploads/2016-10/github_directory_cp.png "github目录")



## 域名配置

github pages免费默认提供了 clarkthan.github.io 的域名访问, 但这里我使用自己的域名lenbo.cc.
还需要在git项目中添加一个CNAME文件, 并将域名写入:

```
echo "lenbo.cc" >> CNAME
git add CNAME
git commit -m "add CNAME"
git push -u origin gh-pages
```

我的域名`lenbo.cc`的DNS服务使用的时[dnspod][dnspod site].

进入dnspod域名管理面板, 给`lenbo.cc`添加一条A记录其IP地址映射到`192.30.252.153`(此IP并不固定请使用 `dig pages.github.com` 命令获取最新)


![ping domain](/uploads/2016-10/ping_domain_cp.png "ping domain test")



### 参考:
[Jekyll中文文档](http://jekyll.com.cn/docs/home/)

[搭建一个免费的，无限流量的Blog----github Pages和Jekyll入门](http://www.ruanyifeng.com/blog/2012/08/blogging_with_jekyll.html)

[DNS 原理入门](http://www.ruanyifeng.com/blog/2016/06/dns.html)

[Markdown 语法说明 (简体中文版)](http://wowubuntu.com/markdown/)

[Jekyll和Github搭建个人静态博客](http://pwnny.cn/original/2016/06/26/MakeBlog.html)

[利用github-pages建立个人博客](http://www.ezlippi.com/blog/2015/03/github-pages-blog.html)






[jekyll site]:http://jekyll.com.cn/
[rubyinstaller site]:http://rubyinstaller.org/downloads/
[devkit wiki]:https://github.com/oneclick/rubyinstaller/wiki/Development-Kit
[gh-pages sit]:https://pages.github.com
[dnspod site]: http://dnspod.cn
