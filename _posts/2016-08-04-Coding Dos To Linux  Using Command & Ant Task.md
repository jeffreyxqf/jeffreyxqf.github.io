---
layout: post
title: Coding Dos To Linux  Using Command & Ant Task
tags: Linux　Ant
category: Linux
---

最近在将工程放在Linux环境中遇到个问题，在window编辑的shell 放在Linux环境中会将换行符转义成^M,
Google了下有很多解决方法, 可以通过
- sed command

    sed -e 's/^M$//' filename 

注意^M 的输入方式是Ctrl+v，Ctrl+m，是一个字符，不是两个字符

- 利用工具dos2Unix

    dos2unix filename

- shell 工程文件是在Linux环境中创建的，换行符为"\r\n",可以直接替换

    sed -i 's/\r$//g' filename 
 
因为是为了在Jenkins上运行脚本进行测试，^M 在windows上无法输入，又不想安装dos2unix
所以我在项目中使用了第三种方式

同时项目是利用ant 进行打包，可以利用ant task fixcrlf 命令在项目打包过程中就将其转换好.

    <fixcrlf srcdir="${pack.dir}" includes="**/*.sh" eol="lf" eof="remove" />

 
效果是一样滴.
