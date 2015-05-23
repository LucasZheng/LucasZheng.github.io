title: linux下删除乱码文件名所对应的文件
description: 在linux下如何删除乱码文件
date: 2014-12-28 20:15:33

categories:
- linux

tags: [linux]
---
通常情况下，在linux上删除文件一般用如下命令：<!-- more -->
{% codeblock lang:plain %}
rm xxx
rm -rf xxx
{% endcodeblock %}

但有时候，从其他平台上拉取文件到linux上时，会产生文件大小为0且文件名乱码的文件，此时用rm命令就不管用了，下面介绍一种通过inode号来删除文件的方法：

通过：
{% codeblock lang:plain %}
ll -i
{% endcodeblock %}

找到乱码文件名所对应的inode号，如969746，然后：
{% codeblock lang:plain %}
find . -inum 969746 -exec rm -i {} \;
{% endcodeblock %}

会提示是否删除，输入y就可以删除文件了。
