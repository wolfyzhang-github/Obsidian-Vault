# 安装管理软件

## 软件安装与移除

### 包管理器

包管理器前端（apt、dnf、yum 等）可以自动下载、配置和安装二进制或者源代码格式的软件包，常常用来安装软件、自动处理软件包之间的依赖关系、升级软件包以至升级发行版，自动处理升级发行版所需的依赖关系等等。

相对于前端而言，后端（如 `dpkg`）会更加底层，`apt` 是一个包管理器的前端，并不直接执行软件包的安装工作，相反的则是交由 `dpkg` 完成。`dpkg` 反馈的依赖信息则会告知 `apt` 还需要安装的其他软件包，并从软件仓库中获取到相应的软件包进行安装，从而完成依赖管理问题。

常见包管理器前后端及发行版的应用关系：

| front end | package manager | major distribution |
| :----: | :----: | :----: |
| apt | dpkg | Debian 系（deb） |
| dnf/yum | rpm | Fedora/CentOS |
|   | Pacman | Arch Linux |

通过包管理器安装的软件都来源于对应的软件源，在官方的软件源中已经包含了丰富的软件，apt 的软件源列表在 `/etc/apt/sources.list` 下。

Ubuntu 替换清华源：
``` bash
sudo sed -i "s@http://.*archive.ubuntu.com@https://mirrors.tuna.tsinghua.edu.cn@g" /etc/apt/sources.list

sudo sed -i "s@http://.*security.ubuntu.com@https://mirrors.tuna.tsinghua.edu.cn@g" /etc/apt/sources.list
```

有时候，由于种种原因，官方软件源中并没有我们需要的软件，但是第三方软件提供商可以提供自己的软件源。在将第三方软件源添加到 `/etc/apt/sources.list` 中之后，就可以获取到第三方提供的软件列表，再通过 `sudo apt install package-name` 安装我们需要的第三方软件。一般可以在需要的第三方软件官网找到对应的配置说明。

### 常用操作

#### 搜索包

例：`sudo apt search firefox`

#### 安装包

* 在线安装。
	例：`sudo apt install firefox`

* 离线安装。
	安装软件包需要相应的软件包管理器，deb 格式的软件包对应的是 `dpkg`。直接通过 `dpkg` 安装 deb 并不会安装需要的依赖，只会报告出相应的依赖缺失了
	
	如果执行 `sudo dpkg -i *.deb` 导致系统依赖出现了问题，可以再执行 `sudo apt -f install` 来调用 `apt` 帮助修复。
	
	直接调用 `apt` 来离线安装软件包：`apt install ./<file>.deb` （`<file>.deb` 为下载得到的 deb 文件）

* 安装预编译的可执行文件
	比如解压 clang 得到的目录中有 `clang` 和 `clang++` 两个可执行文件，但我们不希望每次都进入如此深的路径去运行这两个命令，可行的两个办法如下：
	
	将 `clang/bin` 路径加入 `PATH` 环境变量中，使得在 shell 中执行时可以寻找到该路径，从而可在路径外执行该对应命令。
	
	或者执行：`sudo cp -R * /usr/local/`，将 clang 目录下的所有内容复制到 `usr/local/` 下。为什么是 `/usr/local` 呢？因为 `/usr/local/bin` 处在 PATH 环境变量下。
	同时，在上面的复制过程中，源目录和目标目录的两个 `bin` 目录会相互合并，`clang` 和 `clang++` 两个可执行文件也就被复制到了 `/usr/local/bin/` 目录中。
	此外，在复制的时候 lib、doc 等文件夹也会和 `/usr/local` 下的对应目录合并，将 clang 的库和文档加到系统中。

#### 移除包

-   `apt remove`：删除二进制文件，但不删除配置文件。
-   `apt purge`：删除二进制文件和配置文件。
-   `apt autoremove` 删除无用的关联的依赖。

## 软件更新

在计算机本地，系统会维护一个包列表，在这个列表里面，包含了软件信息以及软件包的依赖关系，在执行 `apt install` 命令时，会从这个列表中读取出想要安装的软件信息，包括下载地址、软件版本、依赖的包，同时 apt 会对依赖的包递归执行如上操作，直到不再有新的依赖包。`apt install some-package` 最终会安装所有检索到的包。

在获取到了新的软件列表后，可以进行软件更新，这时使用的是 `apt upgrade` 命令。`apt upgrade` 会根据软件列表中的版本信息与当前安装的版本进行对比，解决新的依赖关系，完成升级。

#### 更新软件列表

例：`sudo apt update`

#### 更新软件包

例：`sudo apt upgrade`

# 操作文件和目录

## 增

### 创建文件

例：`touch file_name_1 file_name_2` （摸了一下文件）

### 创建目录

例：`mkdir -p dir_1/dir_2/dir_3/` （参数 `-p`：创建中间目录）

## 删

### 删除文件或目录

`rm [OPTION] FILE...`

| option | meaning |
| :----: | :----: |
| `-f`, `--force` | 强制删除 |
| `-r`, `-R`, `--recursive` | 递归删除 |
| `-d`, `--dir` | 删除空目录 |

例：

``` bash
# 删除 file1.txt 文件
rm file1.txt 
# 删除 test 目录及其下的所有文件
rm -r test/ 
# 删除 test1/、test2/、file1.txt 这些文件、目录。其中，这些文件或者目录可能不存在、写保护或者没有权限读写
rm -rf test1/ test2/ file1.txt 
```

## 改

### 复制文件和目录

``` bash
# 将 SOURCE 文件拷贝到 DEST 文件，拷贝得到的文件即为 DEST
cp [OPTION] SOURCE DEST

# 将 SOURCE 文件拷贝到 DIRECTORY 目录下，SOURCE 可以为不止一个文件
cp [OPTION] SOURCE... DIRECTORY
```

| option | meaning |
| :----: | :----: |
|`-r`, `-R`, `--recursive`|递归复制，常用于复制目录|
|`-f`, `--force`|覆盖目标地址同名文件|
|`-u`, `--update`|仅当源文件比目标文件新才进行复制|
|`-l`, `--link`|创建硬链接|
|`-s`, `--symbolic-link`|创建软链接|

 软、硬链接参考[linux 磁盘结构中 inode 相关的内容](../linux-theory/disk-structure)

### 移动文件和目录

`mv` 与 `cp` 的使用方式相似，效果类似于 Windows 下的剪切。

``` bash
# 将 SOURCE 文件移动到 DEST 文件
mv [OPTION] SOURCE DEST

# 将 SOURCE 文件移动到 DIRECTORY 目录下，SOURCE 可以为多个文件
mv [OPTION] SOURCE... DIRECTORY
```

| option | meaning |
| :----: | :----: |
| `-r`, `-R`, `--recursive` | 递归移动，常用于移动目录 |
| `-f`, `--force` | 覆盖目标地址同名文件 |
| `-u`, `--update` | 仅当源文件比目标文件新才进行移动 |

## 查

### 查看文件

* cat 

```bash
# 输出 file.txt 的全部内容
cat file.txt

# 查看 file1.txt 与 file2.txt 连接后的内容
cat file1.txt file2.txt
```

* less

``` bash
# 在可交互的窗口内输出 FILE 文件的内容
less FILE
```

| keys | meaning |
| :----: | :----: |
| d/u | 向下/上滚动半页（**d**own/**u**p）|
| Page Down / Page Up <br>  `f` / `b` | 向下/上滚动一整页（**f**orward/**b**ack）|
|`g` / `G`|跳转到文件开头/结尾|
| `/PATTERN`|在文件中搜索 PATTERN|
| `n` / `N`| 跳转到下一个/上一个找到的 PATTERN|
| `q`| 退出|

### 搜索文件和目录

``` bash
# 在 PATH（路径）中根据 EXPRESSION（表达式）搜索文件
\find [OPTION] PATH [EXPRESSION]
```

|option| meaning|
|:--:|:--:|
|`-name '*.ext'`|文件名后缀为 ext。其中 `*` 是任意匹配符|
|`-type d`|文件类型为目录，其他的类型例如 `f`（普通文件）|
|`-size +1M`|大于 1M 的文件，`+` 代表大于这个大小，对应地，`-` 代表小于之后的大小|
|`-or`|或运算符，代表它前后两个条件满足一个即可|

例：

``` bash
# 在当前目录搜索名为 report.pdf 的文件：
find . -name 'report.pdf'

# 全盘搜索大于 1G 的文件：
find / -size +1G

#在用户目录搜索所有名为 node_modules 的文件夹：
find ~/ -name 'node_modules' -type d
```

## 模式匹配

许多现代的 shell 都支持一定程度的模式匹配。举个例子，bash 的匹配模式被称为 glob，支持的操作如下：

| keys| meaning|
|:--:|:--:|
|`*`|任意字符串|
|`foo*`|匹配 foo 开头的字符串|
|`*x*`|匹配含 x 的字串|
|`?`|一个字符|
|`a?b`|`acb`、`a0b` 等，但不包含 `a00b`|
|`*.[ch]`|以 .c 或 .h 结尾的文件|

和上面提到的命令结合，可以显著提高操作效率。例如：

| command | meaning|
|:--:|:--:|
|`rm *.tar.gz`|删除所有以 `tar.gz` 结尾的压缩文件|
|`mv -r *.[ch] /path`|将当前及子目录下所有以 `.c` 或 `.h` 结尾的代码移动到 `/path`|

除了上面提到的 glob，bash 还支持 extglob，不过需要先用 `shopt -s extglob` 启用，其他 shell 例如 zsh 还支持[正则表达式](text-processing-and-regular-expressions)。

# 压缩解压文件

``` bash
# 命令格式如下，请参考下面的使用样例了解使用方法
tar [OPTIONS] FILE...
```

| 选项                     | 含义                                         |
| :------------------------: | :--------------------------------------------: |
| `-A`                     | 将一个存档文件中的内容追加到另一个存档文件中 |
| `-r`, `--append`         | 将一些文件追加到一个存档文件中               |
| `-c`, `--create`         | 从一些文件创建存档文件                       |
| `-t`, `--list`           | 列出一个存档文件的内容                       |
| `-x`, `--extract, --get` | 从存档文件中提取出文件                       |
| `-f`, `--file=ARCHIVE`   | 使用指定的存档文件                           |
| `-C`, `--directory=DIR`  | 指定输出的目录                               |

添加压缩选项可以使用压缩算法进行创建压缩文件或者解压压缩文件：

| 选项                                   | 含义                        |
| -------------------------------------- | --------------------------- |
| `-z`, `--gzip`, `--gunzip`, `--ungzip` | 使用 gzip 算法处理存档文件  |
| `-j`, `--bzip2`                        | 使用 bzip2 算法处理存档文件 |
| `-J`, `--xz`                           | 使用 xz 算法处理存档文件    |

例：

``` bash
# 将 file1、file2、file3 打包为 target.tar
tar -c -f target.tar file1 file2 file3

# 将 target.tar 中的文件提取到 test 目录中
tar -x -f target.tar -C test/

# 将 file1、file2、file3 打包，并使用 gzip 算法压缩，得到压缩文件 target.tar.gz
tar -cz -f target.tar.gz file1 file2 file3

# 将压缩文件 target.tar.gz 解压到 test 目录中
tar -xz -f target.tar.gz -C test/

# 将 archive1.tar、archive2.tar、archive3.tar 三个存档文件中的文件追加到 archive.tar 
tar -Af archive.tar archive1.tar archive2.tar archive3.tar

# 列出 target.tar 存档文件中的内容
tar -t -f target.tar

# 打印出文件的详细信息
tar -tv -f target.tar
```

后缀名并不能决定文件类型，但后缀名通常用于帮助人们辨认这个文件的可能文件类型，从而选择合适的打开方法。

在第一个例子中，创建得到的文件名为 `target.tar`，后缀名为 `tar`，表示这是一个没有进行压缩的存档文件。

在第二个例子中，创建得到的文件名为 `target.tar.gz`。将 `tar.gz` 整体视为后缀名，可以判断出，为经过 gzip 算法压缩（`gz`）的存档文件（`tar`）。可知在提取文件时，需要添加 `-z` 选项使其经过 gzip 算法处理后再进行正常 tar 文件的提取。

同样地，通过不同压缩算法得到的文件应该有不同的后缀名，以便于选择正确的参数。如经过 `xz` 算法处理得到的存档文件，其后缀名最好选择 `tar.xz`，这样可以知道为了提取其中的文件，应该添加 `--xz` 选项。

tar 名字来源于英文 **t**ape **ar**chive，原先被用来向只能顺序写入的磁带写入数据。tar 格式本身所做的事情非常简单：把所有文件（包括它们的“元数据”，包含了文件权限、时间戳等信息）放在一起，打包成一个文件。**注意，这中间没有压缩的过程。**

为了得到更小的打包文件，方便存储和网络传输，就需要使用一些压缩算法，缩小 tar 文件的大小。这就是 tar 处理它自己的打包文件的逻辑。在 Windows 下的一部分压缩软件中，为了获取压缩后的 tar 打包文件的内容，用户需要手动先把被压缩的 tar 包解压出来，然后再提取 tar 包中的文件。

# 查找软件手册

* man

通过 `man command` 可以得到大部分安装在 Linux 上的软件的用户手册。

* tldr

`tldr` 软件中包含有一个由社区维护的精简版文档，通过几个简单的例子让用户可以快速地一窥软件的使用方法。在 Debian 系下，可以直接通过 `apt` 进行安装：`sudo apt install tldr`。