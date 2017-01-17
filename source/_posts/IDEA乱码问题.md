---
title: IDEA乱码问题
date: 2017-01-16 14:13:57
tags: [IDEA, 乱码]
---

# 代码乱码
打开File-Setting, 找到File Encodings这个选项，把encoding设置成你工程的编码即可，一般是UTF-8。也可以在右下角快捷改变当前文件的编码。改变之后会提示你reload文件。

# 控制台乱码
## 执行main函数时
同样是打开setting，找到 Build,Execution,Deployment > Compiler > Java Compiler， 设置 Additional command line parameters选项为 -encoding utf-8，然后rebuild下，重新运行

## 运行tomcat等容器时
- 打开Run/Debug Configuration,选择你的tomcat

- 然后在  Server > VM options 设置为 -Dfile.encoding=UTF-8 ，重启tomcat

好了，三种解决乱码的方案大概就是这样，有问题大家留言

# 如果上面不管用，可以尝试下面方法修改配置文件
打开IDEA安装目录找到 idea.exe.vmoptions （64位为 idea64.exe.vmoptions ）文件， 在文件末尾加上一行 -Dfile.encoding=UTF-8
然后重启IDEA，一般问题都可以解决