---
title: hexo+next主题配置
date: 2022-07-03 11:48:55
categories:
- Blog
tags:
- hexo
- next
---
记录博客网站的美化操作，用的是hexo和next主题。
<!-- more -->

## hexo配置
### hexo配置文件 *_config.yml*  
#### 修改网站基本配置信息  
```
# Site  
title: ANKIIMA  
subtitle: 'Blog'  
description: 'ANKIIMA的博客'  
keywords: 'OpenGL CV'  
author: 'ANKIIMA'  
language: zh-CN  
timezone: ''`  
```
#### 修改主题配置
```
# Extensions  
theme: NEXT  
```
## next配置
#### 选择框架  
```
# Schemes
# scheme: Muse
#scheme: Mist
#scheme: Pisces
scheme: Gemini
```
#### dark mode启用  
```
# Dark Mode  
darkmode: false  
```

#### 网页图标更换（使用[此网站](https://realfavicongenerator.net/)创建图标）替换路径为对应图片  
```
# Site Information Settings  
favicon:
  small: /images/favicon-16x16-next.png  
  medium: /images/favicon-32x32-next.png  
  apple_touch_icon: /images/apple-touch-icon-next.png  
  safari_pinned_tab: /images/logo.svg  
  #android_manifest: /manifest.json  `  
```
#### 菜单设置 自定义添加菜单项
```
menu:  
  home: / || fa fa-home  
  # about: /about/ || fa fa-user  
  tags: /tags/ || fa fa-tags  
  categories: /categories/ || fa fa-th  
  archives: /archives/ || fa fa-archive  
  #schedule: /schedule/ || fa fa-calendar  
  #sitemap: /sitemap.xml || fa fa-sitemap  
  #commonweal: /404/ || fa fa-heartbeat`
```
next提供上面的可选菜单项，格式最后一项是显示图标，可以在[该网站](https://fontawesome.com/)查询对应的值并修改。如果想添加其他菜单项应该也可以按照上面的格式，不过对我来说够用了。
### 侧边栏设置   

#### 头像 需要gif格式
```
# Sidebar Avatar
avatar:
  # Replace the default image and set the url here.
  url: /images/1.gif
  # If true, the avatar will be displayed in circle.
  rounded: false
  # If true, the avatar will be rotated with the cursor.
  rotated: false
```
#### 其他页面链接
```
# Social Links
# Usage: `Key: permalink || icon`
# Key is the link label showing to end users.
# Value before `||` delimiter is the target permalink, value after `||` delimiter is the name of Font Awesome icon.
social:
  GitHub: https://github.com/ANKIIMA || fab fa-github
```
### 页脚设置  
#### 图标
```
# Icon between year and copyright info.
  icon:
    # Icon name in Font Awesome. See: https://fontawesome.com/icons
    name: fa-solid fa-cannabis
    # If you want to animate the icon, set it to true.
    animated: false
    # Change the color of icon, using Hex Code.
    color: "#A9A9A9"
```
#### copyright
```
# If not defined, `author` from Hexo `_config.yml` will be used.
  copyright: ANKIIMA
```
### 文章显示设置
#### 摘要  
按照[hexo文档](https://hexo.io/docs/tag-plugins#Post-Excerpt)的格式在文章中设置摘要部分，否则自动全部显示
```
# Automatically excerpt description in homepage as preamble text.
excerpt_description: true
```
#### 阅读全文按钮
```
# Read more button
# If true, the read more button will be displayed in excerpt section.
read_more_btn: true
```
#### 文章末尾标签符号显示
```
# Use icon instead of the symbol # to indicate the tag at the bottom of the post
tag_icon: true
```
#### 文末推广链接
```
# Subscribe through Telegram Channel, Twitter, etc.
# Usage: `Key: permalink || icon` (Font Awesome)
follow_me:
  #QQ: 1667791793 || fa-brands fa-qq
  GitHub: https://github.com/ANKIIMA || fa-brands fa-github
  #Twitter: https://twitter.com/username || fab fa-twitter
  #Telegram: https://t.me/channel_name || fab fa-telegram
  #WeChat: /images/wechat_channel.jpg || fab fa-weixin
```
上面的icon设置同上
### 主题其他设定
#### 颜色设置
```
# Browser header panel color.
theme_color:
  light: "#222"
  dark: "#222"
  ```
#### 书签 
显示在右上角，自动保存读者阅读进度
```
# Bookmark Support
bookmark:
  enable: true
  # Customize the color of the bookmark.
  color: "#222"
  # If auto, save the reading progress when closing the page or clicking the bookmark-icon.
  # If manual, only save it by clicking the bookmark-icon.
  save: auto
  ```
  ### 添加菜单项页面
  如果仅在菜单设置中添加选项，点击后由于没有资源所以不会打开页面，所以不用修改配置文件，而应该在博客文件夹中应该使用hexo添加对应的页面资源：
  ````
  $ hexo new page custom-name
````
添加以后博客文件中会出现对应名称的文件夹，包含子集目录index，生成一个md文件。  举例来说，如果添加的是标签和分类页面，那么先进入md文件，在文首的hexo信息栏中添加type关键字，值为对应名称(tags | categories)，然后更新博客就可以看到效果了。
```
---
# tag页面信息栏
title: tags
date: 2022-07-03 22:13:01
type: tags
---
```
添加完成后，标签页面和分类页面会自动统计并显示已经存在的标签或类别，不用进一步更改；点击对应标签或类别也会显示对应文章。  

要注意的是，撰写文章的同时也要将对应的标签和分类关键字按照hexo要求写在信息栏中，例如本篇博客：  
```
---
title: hexo+next主题配置
date: 2022-07-03 11:48:55
categories:
- Blog
tags:
- hexo
- next
---
```
  ## 更多信息
  上面是我博客配置的大致信息，更多配置方法可以参考[next文档](https://theme-next.js.org)，next在配置文件中给出了一系列配置接口，即使不懂前端知识也能进行个性化设置，还可以在博客页面中添加聊天室、评论等功能，如果以后有精力会再进行记录。
