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
---
### intellij idea 安装

有能力的同学建议购买正版服务 [intellij idea](https://www.jetbrains.com/idea/download/#section=windows)

破解版请自行百度

###  环境配置

##### java开发环境配置

##### maven配置

##### git配置


###  优化配置

##### 隐藏项目下文件（不是git的.gitignore文件）
IntelliJ IDEA项目会自动生成一个.idea文件夹和.iml文件，存在项目里能看到，但是又不需要操作，看上去不够美观，故这里将其隐藏
File->Settings->Editor->File Types下的”Ignore files and folders”一栏添加 *.idea;*.iml;
![image](https://nine-free.github.io/img/idea-ignore.jpg)

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





