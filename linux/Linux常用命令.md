# Linux常用命令

记录一些常用&容易忘记的Linux命令(工具)

- ~~真常用还会忘记吗?~~ 

很可能不全, 请多看手册(`man xxx`)

## find

find - 遍历文件层次(搜索文件)

### **常用参数**

- `-name "filename"`: 想要查找的文件名, 支持通配符`*`和`?`



### **使用例**

```shell
.
├── 实训报告001_桌号D10
│   ├── 实训报告001_桌号D10.pdf
│   ├── 实验短视频
│   └── 程序源文件
├── 实训报告002_桌号D10
│   ├── 实训报告002_桌号D10.pdf
│   ├── 实验短视频
│   └── 程序源代码
├── 实训报告003_桌号D10
│   ├── 实训报告003_桌号D10.pdf
│   ├── 实验短视频
│   └── 程序源代码
├── 实训报告004_桌号D10
│   ├── 实训报告004_桌号D10.pdf
│   ├── 实验短视频
│   └── 程序源代码
├── 实训报告005_桌号D10
│   ├── 实训报告005_桌号D10.pdf
│   ├── 实验短视频
│   └── 程序源代码
├── mvshell.txt
└── 实训报告&总结合集
```

想把`*.pdf`复制到`./实训报告&总结合集`文件夹下

```shell
find ../ -name "*.pdf" | xargs -I {} cp {} ./
```

## ln

ln 是 Linux 和类 Unix 系统中的命令，用于创建链接（link）。链接是指向文件或目录的引用，有两种主要类型：

### 硬链接（Hard Link）

**命令格式**：`ln <源文件> <目标文件>`

- 硬链接是文件系统中的另一个文件名，指向同一个文件的物理数据（inode）。
- 硬链接与原文件共享相同的 inode，删除其中一个，另一个仍然有效，文件数据不会丢失。
- 不能跨不同文件系统创建硬链接。
- 不能对目录创建硬链接（普通用户一般无权限）。

###  符号链接（软链接，Symbolic Link）

**命令格式**：`ln -s <source> <target>`

- 符号链接是一个特殊文件，内容是指向另一个文件或目录的路径。
- 类似 Windows 的快捷方式。
- 可以跨文件系统，也可以指向目录。
- 如果原文件被删除或移动，符号链接会“失效”。

### 常用参数

| **参数** | **含义**                       |
| -------- | ------------------------------ |
| -s       | 创建符号链接                   |
| -f       | 强制删除已存在的目标文件       |
| -n       | 不跟随符号链接（通常用于目录） |
| -v       | 显示详细信息                   |

### **示例**

```shell
ln /home/user/file.txt file_hardlink.txt     # 创建硬链接
ln -s /home/user/file.txt file_symlink.txt   # 创建符号链接
```

### 关于重复执行ln -s的bug (即幂等性)
> Given one or two arguments, ln creates a link to an existing file `source_file`.  If `target_file` is given, the link has that name; **`target_file` may also be a directory in which to place the link**; otherwise it is placed in the current directory.  If only the directory is specified, the link will be made to the last component of source_file.

重复执行`ln -s <source_dir> <link_name>`时, 会导致在文件夹(`test_dir`)内部创建一个同名链接`test_dir -> ./test_dir`

这有可能导致后续的一些操作出错(如递归检索文件夹)

```shell
# 1. 创建一个链接test_link, 指向test_dir
❯ ln -s ./test_dir ./test_link && tree
.
├── test_dir
│   └── test.txt
└── test_link -> ./test_dir

# 2. 重复执行, 将test_link视为目录, 在目录内创建一个链接, 指向source
❯ ln -s ./test_dir ./test_link && tree
.
├── test_dir
│   ├── test_dir -> ./test_dir
│   └── test.txt
└── test_link -> ./test_dir

# 3. 再次重复, 此时test_link文件夹内部已有test_dir, 报错
❯ ln -s ./test_dir ./test_link && tree
ln: ./test_link/test_dir: File exists
```

解决方案: 使用`-n`参数, 将`test_link`视为普通文件, 而非一个文件夹

```shell
# 加上-f强制覆盖
❯ ln -sfn ./test_dir ./test_link && tree
.
├── test_dir
│   └── test.txt
└── test_link -> ./test_dir

❯ ln -sn ./test_dir ./test_link && tree
ln: ./test_link: File exists

# 注: -sf并没有效果, 依然会将test_link解析为文件夹, 导致创建出嵌套的link
```

