## 被封ip

当实验室机器漏洞没有及时处理时, 会导致机器ip被封, 会有以下现象: 
1. 无法连接到机器
2. 机器无法访问外部网络, 但是能访问机房其他机器
(以下以dell03举例)

### 无法连接到机器

可以使用跳板机/任意可用服务器作为代理, 解决方案具体如下
	1. 在ssh命令中指定代理`-J nicexlab`
	2. 在本机`~/.ssh/config`中配上`ProxyJump nicexlab`, 完整如下

```
Host dell03
  HostName 10.26.42.227
  User zhangjiangjie
  ProxyJump nicexlab

Host nicexlab
  HostName nicexlab.top
   # User xmu201
  User zhangjiangjie
   # 指定端口为6000
  Port 6000
```

### 无法访问外网

可以将流量反向代理到本机的clash

在连接命令中添加 `-R [远程机器代理端口,我使用7899]:127.0.0.1:[本地机器clash端口, 我使用7897]`

连接上服务器后, 在命令行配置代理