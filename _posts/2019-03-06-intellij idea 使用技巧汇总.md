---
layout:     post
title:      intellij idea 使用技巧汇总
subtitle:   
date:       2019-03-06
author:     nine-free
header-img: img/post-bg-cook.jpg
catalog: true
tags:
    - intellij idea
    - 开发工具
---
### intellij idea 安装

有能力的同学建议购买正版服务 [intellij idea](https://www.jetbrains.com/idea/download/#section=windows)

破解版请自行百度

###  环境配置
idea安装前需要配置java开发环境
##### java开发环境配置
File-Project Structure->SDKs
![image](https://nine-free.github.io/img/idea-settings-jdk.jpg)

##### maven配置
File->Settings->Maven
![image](https://nine-free.github.io/img/idea-settings-maven.jpg)

##### git配置
File->Settings->version controll->git
![image](https://nine-free.github.io/img/idea-settings-git.jpg)

###  优化配置

##### 代码编辑器主题风格

1、从网上下载已有[主题](http://www.riaway.com/theme.php?page=1)，直接导入
File->Import Settings 选择下载的主题即可

2、使用默认主题简单编辑之后保存自己的主题风格
File->Settings->Editor->Color&Font->Font->Scheme
![image](https://nine-free.github.io/img/idea-settings-scheme.jpg)
特别贡献一下自己使用的字体包[百度网盘](https://pan.baidu.com/s/1ePTvWc0ajWSIatgrgmvGPg) 提取码：hw5b
                  
##### 文件编码设置
建议将编码设置UTF-8
File->Settings->Editor->File Encodings
![image](https://nine-free.github.io/img/idea-settings-file-encoding.jpg)

##### 模板添加&修改
File->Settings->Editor->File and Code templates
File->Settings->Editor->live templates
![image](https://nine-free.github.io/img/idea-settings-template.jpg)

##### 阿里规范插件
File->Settings->Plugins->Alibaba Java Coding Guidelines
![image](https://nine-free.github.io/img/idea-settings-alibaba-code-guide.jpg)
注意：老项目可能比较坑，各种规范提示

##### 隐藏项目下文件（不是git的.gitignore文件）
IntelliJ IDEA项目会自动生成一个.idea文件夹和.iml文件，存在项目里能看到，但是又不需要操作，看上去不够美观，故这里将其隐藏
File->Settings->Editor->File Types下的”Ignore files and folders”一栏添加 *.idea;*.iml;
![image](https://nine-free.github.io/img/idea-settings-file-ignore.jpg)
待验证:添加之后导致编译错误，放开之后恢复正常




