---
title: Travis-ci+Hexo+GithubPages搭建个人博客
tags: [持续集成, Travis-ci, Hexo]
index_img: /resource/img/ci/Travis-ci+Hexo+GithubPages.jpg
date: 2021-05-12 16:05:00
---

## 1.本地搭建博客框架
1. 安装node.js，最好是10这个版本的

   1. windows10下
      链接：https://pan.baidu.com/s/1gS0UEbEeicHlMaXUe-pgYw  提取码：4n2c 

   2. Ubuntu下：

      ```shell
      # 更新软件 
      sudo apt remove --purge nodejs npm
      sudo apt clean
      sudo apt autoclean
      sudo apt install -f
      sudo apt autoremove
      # 安装
      sudo apt install curl
      curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash -
      sudo apt-get install -y nodejs
      curl -sL https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
      sudo apt-get update && sudo apt-get install yarn
      ```

   3. npm换源

      ```shell
      # npm换源
      npm config set registry https://registry.npm.taobao.org
      #如果只是临时改变源，可以这样
      npm --registry=https://registry.npm.taobao.org
      # 更新
      npm install -g npm
      ```

      

2. 安装 hexo

   ```shell
   npm install hexo-cli -g
   ```

   创建一个`Github用户名.github.io`的文件夹，进入到这个文件夹下

   ```shell
   hexo init
   npm install
   ```

   

## 2.Github配置

1. 创建一个`用户名.github.io`的public类型的仓库，不要新建Readme.md文件

2. 创建Github访问令牌：[教程](https://www.cnblogs.com/soldier-cnblogs/p/13386848.html)

3. 创建一个`resource`分支，将其设置为主分支
   ![image-20210512153500126](C:\Users\dev\AppData\Roaming\Typora\typora-user-images\image-20210512153500126.png)

   

## 3.Travis-ci配置

1. 使用github账号授权登录 [Travis-ci官网](https://travis-ci.org/) ，点击Sign In
2. 在官网build`用户名.github.io`项目
   点击这里的“+”号
   ![](https://img2020.cnblogs.com/blog/1622529/202004/1622529-20200412154627421-1979272790.png)
   找到或搜索我们的项目
   ![img](https://img2020.cnblogs.com/blog/1622529/202105/1622529-20210512154342364-1133638796.png)
   完成之后可以在首页见到该项目
   ![img](https://img2020.cnblogs.com/blog/1622529/202105/1622529-20210512154713803-1412253504.png)
3. 进入该项目，点击右侧的`More options`选择`Settings`
   在`Environment Variables`下 add 一个名为`Github_Token`的变量
   ![img](https://img2020.cnblogs.com/blog/1622529/202105/1622529-20210512155348476-1110897466.png)



4.提交代码到Github

1. 在本地项目的根目录下新建一个`.travis.yml`文件
   根据自己的实际情况修改：

   ```yml
   # 设置语言
   language: node_js
   
   # 设置相应的版本
   node_js: stable
   
   cache:
     apt: true
     directories:
       - node_modules # 缓存不经常更改的内容
   
   # 更改时区
   before_install:
     - export TZ='Asia/Shanghai'
   
   # 安装hexo及插件
   install:
     - npm install
   
   # 下拉代码后执行的操作
   script:
     - hexo clean # 清除
     - hexo g # 生成
   
   after_script:
     - cd ./public
     - git init
     # 修改github的用户名
     - git config user.name "soldiergit"
     # 修改email
     - git config user.email "soldier_wyyx@163.com"
     - git add .
     # 提交记录包含时间 跟上面更改时区配合
     - git commit -m "Travis CI Auto Builder at `date +"%Y-%m-%d %H:%M"`"
     # Travis_Token是在Travis官网中配置环境变量的名称（more options 选择 setting添加的值）
     - git push --force --quiet "https://${Github_Token}@${GH_REF}" master:master
   
   # 只监测resource分支，resource是我存放源码的分支的名称，可根据自己情况设置
   branches:
     only:
       - resource
   
   # 配置常量
   env:
     global:
       # 设置GH_REF，注意更改yourname及末尾加上.git
       - GH_REF: github.com/soldiergit/soldiergit.github.io.git
   
   # 配置通知（电子邮件、IRC、campfire等）
   # https://docs.travis-ci.com/user/notifications/
   notifications:
     email:
       - 583403411@qq.com
       - soldier_wyyx@163.com
     on_success: change
     on_failure: always
   ```

   ![img](https://img2020.cnblogs.com/blog/1622529/202105/1622529-20210512155733300-1805568823.png)

2. 将所有代码提交到Github的`resource`上即可