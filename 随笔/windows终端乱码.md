在git status时出现乱码, 但是git config中的配置都改成了utf-8字符集了QAQ



可以尝试一下

```shell
[Console]::OutputEncoding = [System.Text.Encoding]::UTF8
```

如果有效, 将它保存到配置文件中 (我是PowerShell)

```shell
vim "$PROFILE"
# 添加到配置文件
[Console]::OutputEncoding = [System.Text.Encoding]::UTF8

# 使配置文件立即生效
. "$PROFILE"

# 验证修改是否完成
[Console]::OutputEncoding.BodyName
```

