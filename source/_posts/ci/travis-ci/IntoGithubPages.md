---
title: Travis-ci+Hexo+GithubPages搭建个人博客
tags: [持续集成, Travis-ci, Hexo, 语雀]
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

2. 创建博客

   ```shell
   # 全局安装hexo
   npm install hexo-cli -g
   # 创建项目
   hexo init Github用户名.github.io
   # 安装依赖
   npm install
   # 安装主题（去Hexo官网选择自己喜欢的博客主题）
   npm install --save hexo-theme-fluid
   # 启动项目
   npm run server
   # 访问http://localhost:4000查看效果
   ```

## 2.Github配置

1. 创建一个`用户名.github.io`的public类型的仓库，不要新建Readme.md文件

2. 创建Github访问令牌：[教程](https://www.cnblogs.com/soldier-cnblogs/p/13386848.html)

3. 创建一个`resource`分支，将其设置为主分支
   ![img](/resource/img/ci/T_GithubPages_1.png)
   
4. ~~手动部署项目~~

   1. 先提交一次到Github

      ```shell
      # 在项目根目录初始化
      git init
      # 设置远程仓库
      git remote add origin https://github.com/soldiergit/soldiergit.github.io.git
      # 添加
      git add .
      # 提交信息
      git commit -m 'init commit'
      # 提交项目(注意这个分支是源码分支，不是GithubPages页面分支-是master)
      git push origin resource
      ```

   2. 配置` _config.yml`

      ```yml
      deploy:
      - type: git
      # 部署到的仓库
        repo: https://github.com/soldiergit/soldiergit.github.io
      # 部署到的分支(GithubPages页面分)
        branch: master
      ```

   3. 部署

      ```shell
      # 项目根目录输入
      hexo deploy
      # 输入GitHub用户名密码即可完成部署
      ```

## 3.Travis-ci配置
1. 使用github账号授权登录 [Travis-ci官网](https://travis-ci.org/) ，点击Sign In
2. 在官网build`用户名.github.io`项目
   点击这里的“+”号
   ![img](/resource/img/ci/T_GithubPages_2.png)
   找到或搜索我们的项目
   ![img](/resource/img/ci/T_GithubPages_3.png)
   完成之后可以在首页见到该项目
   ![img](/resource/img/ci/T_GithubPages_4.png)
3. 进入该项目，点击右侧的`More options`选择`Settings`
   在`Environment Variables`下 add 一个名为`Github_Token`的变量
   ![img](/resource/img/ci/T_GithubPages_5.png)

4.提交代码到Github

1. 在本地项目的根目录下新建一个`.travis.yml`文件
   <details>
     <summary>点击展开</summary>

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
    </details>

   ![img](/resource/img/ci/T_GithubPages_6.png)

2. 将所有代码提交到Github的`resource`上即可
   ![img](/resource/img/ci/T_GithubPages_7.png)
   ![img](/resource/img/ci/T_GithubPages_8.png)
   ![img](/resource/img/ci/T_GithubPages_9.png)

## 4.实现在线写作
### 实现思路
![img](/resource/img/ci/silu.png)

### 实现步骤
1. 获取 [Travis-ci](https://travis-ci.org/account/preferences) 的访问 token
    ![img](/resource/img/ci/travisci_token.png)

2. 编写触发 Travis-ci 的 Serverless 函数：注册[腾讯云](https://cloud.tencent.com/) 并边[创建一个云函数](https://console.cloud.tencent.com/scf/list)
    ![img](/resource/img/ci/serverless.png)
    
    ```py
    # -*- coding: utf8 -*-
    import requests
    import json
    def main_handler(event, context):
        r = requests.post("https://api.travis-ci.org/repo/soldiergit%2Fsoldiergit.github.io/requests",
        json = {"request": {"branch": "src"}},
        headers = {"User-Agent":'curl/7.52.1',
                  'Content-Type': 'application/json',
                  'Travis-API-Version': '3',
                  'Accept': 'application/json',
                  'Authorization': 'travis-ci的token'})
        if r.status_code == 204:
            return "This's OK!" 
        else:
            return r.status_code
    ```
    
3. 创建触发器，将函数发布，复制该函数的访问地址
    ![img](/resource/img/ci/trigger.png)

4. 配置语雀
    1. 注册语雀账号，创建一个`文档知识库`，专门为GithubPages服务
    2. 配置语雀 webhook，将上一步复制的函数访问地址贴到 URL 那栏
        ![img](/resource/img/ci/yuque1.png)
    3. 创建一个[语雀Token](https://www.yuque.com/settings/tokens)，勾选`读取`
        
5. 项目配置
    1. 安装插件
    
    ```shell
    npm i -g yuque-hexo
    ```
    2. 配置 package.json
        ![img](/resource/img/ci/yuque2.png)
        
        ```json
        {
          "name": "hexo-site",
          "version": "0.0.0",
          "private": true,
          "scripts": {
            "sync": "yuque-hexo sync",
            "clean:yuque": "yuque-hexo clean",
            "build": "hexo generate",
            "clean": "hexo clean",
            "deploy": "hexo deploy",
            "server": "hexo server"
          },
          "hexo": {
            "version": "5.2.0"
          },
          "yuqueConfig": {
          "postPath": "source/_posts/yuque",
          "cachePath": "yuque.json",
          "mdNameFormat": "slug",
          "adapter": "hexo",
          "concurrency": 5,
          "baseUrl": "https://www.yuque.com/api/v2",
          "login": "soldier_yuque",
          "repo": "blog",
          "token": "语雀的token",
          "onlyPublished": true,
          "onlyPublic": true
        },
          "dependencies": {
            "hexo": "^5.0.0",
            "hexo-deployer-git": "^2.1.0",
            "hexo-generator-archive": "^1.0.0",
            "hexo-generator-category": "^1.0.0",
            "hexo-generator-index": "^2.0.0",
            "hexo-generator-tag": "^1.0.0",
            "hexo-renderer-ejs": "^1.0.0",
            "hexo-renderer-marked": "^3.0.0",
            "hexo-renderer-stylus": "^2.0.0",
            "hexo-server": "^2.0.0",
            "hexo-theme-fluid": "^1.8.4",
            "yuque-hexo": "^1.7.0"
          }
        }
        ```
    
    3. 配置 `.travis.yml`
        <details>
            <summary>点击展开</summary>
            
        ```yml
        # 设置语言
        language: node_js
        
        # 设置相应的版本（必须12以上）
        node_js: 
          - 15
        
        cache:
          apt: true
          directories:
            - node_modules # 缓存不经常更改的内容
        
        # 更改时区
        before_install:
          - export TZ='Asia/Shanghai'
        
        # 安装hexo及插件
        install:
          - npm install hexo-cli
          - npm install yuque-hexo
          - npm install
        
        # 下拉代码后执行的操作
        script:
          # 同步语雀文章到本地
          - yuque-hexo clean
          - yuque-hexo sync
          - hexo generate # 生成
        
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
          # 【2021.05.13】修改gitee的用户名
          - git config user.name "${Gitee_Username}"
          # 【2021.05.13】也提交到码云
          - git push --force --quiet https://${Gitee_Username}:${Gitee_Token}@${GE_REF} master:master
        
        # 只监测resource分支，resource是我存放源码的分支的名称，可根据自己情况设置
        branches:
          only:
            - resource
        
        # 配置常量
        env:
          global:
            # 设置GH_REF，注意更改yourname及末尾加上.git
            - GH_REF: github.com/soldiergit/soldiergit.github.io.git
            # 【2021.05.13】码云的
            - GE_REF: gitee.com/soldiergitee/soldiergitee.git
        
        # 配置通知（电子邮件、IRC、campfire等）
        # https://docs.travis-ci.com/user/notifications/
        notifications:
          email:
            - 583403411@qq.com
            - soldier_wyyx@163.com
          on_success: change
          on_failure: always
        ```
        </details>

配置完成将其提交到你的 github 仓库。

<font color=#FF000>**注意：**</font>语雀不要开启保存并发布，否则无法触发 webhook

