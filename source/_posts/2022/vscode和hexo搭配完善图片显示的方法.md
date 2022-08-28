---
title: vscode和hexo搭配完善图片显示的方法
top: false
cover: false
toc: true
mathjax: true
date: 2022-06-17 21:21:13
password:
summary:
tags: hexo
categories: 使用记录
---
使用vscode写Markdown文件虽然很方便，但是搭配hexo却不能很好的显示图片。就像这样：
![](vscode和hexo搭配完善图片显示的方法/2022-06-18-09-18-50.png)
我在网上找了较多的教程和解决方法，算是找到一个还行的办法:
在一篇博文中看到这样的解释
“在hexo3.0版本中编写md文件时新的图片插入方式：只要在_config.yml中添加`post_asset_folder: true`，就会在生成新post时创建一个同名文件夹；在md中输入`{% asset_img xxx %}`，就可以插入这个文件夹中的图片”
<!--more-->
于是我们需要安装vscode的插件`Paste Image`和`Markdown Preview Enhanced`并配置一些设置
在`Paste Image`配置中一些设置，
按住Ctrl+shift+p打开设置选项
![asset_img](vscode和hexo搭配完善图片显示的方法/2022-06-18-09-14-14.png)

先贴个Markdown的插图语法
`![图片alt](图片链接 "图片title")`方便理解
在setting.json中最后插入如下
`"pasteImage.path": "${currentFileNameWithoutExt}/",
 "pasteImage.insertPattern": "![asset_img](${imageFilePath})"`
这样我们通过插件快捷键Ctrl+alt+v粘贴后的会直接保存到同名文件名下,并且会在文档中插入hexo的插图代码

## 分享一个hexo new post "文章名"的懒人技巧
在跟目录中打开文件script 或者新建也行
![asset_img](vscode和hexo搭配完善图片显示的方法/2022-06-18-10-12-45.png)
随便起一个脚本
![asset_img](vscode和hexo搭配完善图片显示的方法/2022-06-18-10-13-04.png)
输入:
```js
let spawn = require('hexo-util/lib/spawn');

hexo.on('new', (data) => {
  spawn('code', [hexo.base_dir, data.path]);
});
```
这样每次 new 文章后自动用vscode打开同名文件
