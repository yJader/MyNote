# cheatsheet

这是一个 cheatsheet, 用于记录一些在这次实验中常用的命令和技巧。

## log文件操作相关

### less 快速查看文件内容

`less +linenumber filename`: 打开文件, 跳转到line number行

- `-N` 选项: (在每行的左侧)显示行号

### 针对log文件的自制正则表达式

`AppendEntries to .*success[\n\t]*\(sendTimes: [3-9]`: 找到sendTimes过高的AppendEntries
