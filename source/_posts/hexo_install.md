---
title: Github个人博客搭建
tags: 
 - Blog
 - Github
 - Hexo
date: 
---

## 要求

+ Git
+ Node.js

## 安装

### Hexo 安装

{% codeblock lang:bash %}
$ npm install -g hexo-cli
$ hexo init <folder>
$ cd <folder>
$ npm install
{% endcodeblock %}

配置参考 [Hexo Docs](https://hexo.io/zh-cn/docs/)

### Butterfly 主题安装

{% codeblock lang:bash %}
git clone -b master https://github.com/jerryc127/hexo-theme-butterfly.git themes/butterfly
{% endcodeblock %}

配置 `_config.yml`

{% codeblock lang:yaml %}
theme: butterfly
{% endcodeblock %}

补充插件

{% codeblock lang:bash %}
npm install hexo-renderer-pug hexo-renderer-stylus --save
{% endcodeblock %}

从 `themes/butterfly/_config.yml` 复制到 `_config.yml`

配置参考 [Butterfly 文档](https://butterfly.js.org/posts/21cfbf15/)
