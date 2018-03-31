title: hexo博客建成后的一些功能添加
author: liuhc
tags:
  - hexo
categories:
  - 建站
date: 2018-03-31 23:16:00
---
1. 添加hexo-admin

   ```
   cd yourblog
   cnpm install --save hexo-admin
   hexo server -d
   open http://localhost:4000/admin/
   ```

2. 增加搜索功能

   1. 我用的是淘宝的cnpm，不然国内的速度太慢了，如何安装cnpm请看这里：[淘宝cnpm](https://npm.taobao.org/)

   2. 在Hexo根目录执行如下命令

      ```
      $ npm install hexo-generator-searchdb --save
      ```

   3. 编辑站点配置文件,在站点配置文件(Hexo根目录的配置文件)_config.yml中添加

      ```
      search:
        path: search.xml
        field: post
        format: html
        limit: 10000
      ```

   4. 编辑主题配置文件,启用本地搜索,在主题配置文件(Next主题根目录的配置文件)_config.yml中启用

      ```
      local_search:
       enable: true
      ```

3. 去掉coding.me的广告

   1. 进入你的Hexo文件夹\themes\next\layout\ _partials\footer.swig，打开该文件

   2. 在
      ```
      <span class="author" itemprop="copyrightHolder">{{ theme.footer.copyright || config.author }}</span>
      ```
      下面增加

      ```
      <!--以下4句为一条竖线和Coding Page-->
      <span class="post-meta-divider">|</span>
      <div class="powered-by">  
      </div>
      <span>Hosted by <a href="https://pages.coding.me" style="font-weight: bold">Coding Pages</a></span>
      ```


