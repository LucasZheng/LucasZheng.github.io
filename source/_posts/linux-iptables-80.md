title: Linux iptables开启80端口
description: Linux环境中如何打开80端口
date: 2014-12-28 20:22:30
categories:
- linux

tags: [linux]
---
在具有root权限的linux环境中，可以使用如下方法来开启80端口：<!-- more -->
{% codeblock lang:plain %}
/sbin/iptables -I INPUT -p tcp –dport 80 -j ACCEPT	#开启80端口命令
/etc/rc.d/init.d/iptables save	#保存配置命令
/etc/rc.d/init.d/iptables restart	#重启服务命令
/etc/init.d/iptables status	#查看已经开放的端口
{% endcodeblock %}

出现如下则表示开启成功：
{% codeblock lang:plain %}
ACCEPT tcp -- 0.0.0.0/0 0.0.0.0/0 tcp dpt:80
{% endcodeblock %}
