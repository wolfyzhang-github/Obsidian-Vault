# 基本概念

## 命令组成

- 反斜杠和其后的一串字母

	字母形式的命令会忽略其后的所有空格，若要手动引入空格，则在命令后加一对花括号来阻止其忽略空格：`\TeX{}`

- 反斜杠和其后的单个非字母符号

**环境**：用以使一些命令局部生效，以`\begin`和`\end`划分作用范围

``` latex
\begin{⟨environment name⟩}[⟨optional arguments⟩]{⟨mandatory arguments⟩}
…
\end{⟨environment name⟩}
```

**分组**：用以使一些命令局部生效，以`{`和`}`划分作用范围

## 代码结构

``` latex
\documentclass{...} % 选用的文档类
% 导言区
\begin{document}
% 正文内容
\end{document}
% 此后内容会被忽略
```

## 宏包和文档类

### 文档类

``` latex
\documentclass[⟨options⟩]{⟨class-name⟩}
```

其中 `⟨class-name⟩` 为文档类的名称，如 LaTeX 提供的 article, report, book，在其基础上派生的一些文档类如支持中文排版的 ctexart / ctexrep / ctexbook，或者有其它功能的一些文档类，如 moderncv / beamer 等。

可选参数 `⟨options⟩` 为文档类指定选项，以全局地规定一些排版的参数，如字号、纸张大小、单双面等等。比如调用 article 文档类排版文章，指定纸张为 A4 大小，基本字号为 11pt，双面排版：

``` latex
\documentclass[11pt,twoside,a4paper]{article}
```

### 宏包

在使用 LaTeX 时，时常需要依赖一些扩展来增强或补充 LaTeX 的功能，比如排版复杂的表格、插入图片、增加颜色甚至超链接等等。这些扩展称为宏包。

``` latex
\usepackage[⟨options⟩]{⟨package-name⟩}
\usepackage{tabularx, makecell, multirow}
```

`\usepackage` 可以一次性调用多个宏包，在 `⟨package-name⟩` 中用逗号隔开。

## 文件组成

| file | detail |
| :--: | :--: |
| .sty | 宏包文件。宏包的名称与文件名一致。| 
|.cls| 文档类文件。文档类名称与文件名一致。
|.bib| BibTeX 参考文献数据库文件。
|.bst |BibTeX 用到的参考文献格式模板。
|.log| 排版引擎生成的日志文件，供排查错误使用。
|.aux| LaTeX 生成的主辅助文件，记录交叉引用、目录、参考文献的引用等。
|.toc| LaTeX 生成的目录记录文件。
|.lof |LaTeX 生成的图片目录记录文件。
|.lot| LaTeX 生成的表格目录记录文件。
|.bbl| BibTeX 生成的参考文献记录文件。
|.blg |BibTeX 生成的日志文件。
|.idx |LaTeX 生成的供 makeindex 处理的索引记录文件。
|.ind| makeindex 处理 .idx 生成的用于排版的格式化索引文件。
|.ilg| makeindex 生成的日志文件。
|.out| hyperref 宏包生成的 PDF 书签记录文件。

## 多文件组织

 `\include` 用来在源代码里插入文件：

``` latex
\include{⟨filename⟩}
```

`⟨filename⟩` 为文件名（不带 .tex 扩展名），如果和要编译的主文件不在一个目录中，则要加上相对或绝对路径。

值得注意的是 `\include` 在读入 `⟨filename⟩` 之前会另起一页。

`\input` 命令纯粹是把文件里的内容插入：

``` latex
\input{⟨filename⟩}
```

当导言区内容较多时，常常将其单独放置在一个 .tex 文件中，再用 `\input` 命令插入。复杂的图、表、代码等也会用类似的手段处理。

# 排版文字

## 编码与中文

ctex 宏包本身用于配合各种文档类排版中文，而 ctex 文档类对 LaTeX 的标准文档类进行了封装，对一些排版根据中文排版习惯做了调整，包括 ctexart/ctexrep/ctexbook。

ctex 宏包和文档类能够识别操作系统和 TeX 发行版中安装的中文字体，因此基本无需额外配置即可排版中文文档。下面举一个使用 ctex 文档类排版中文的最简例子：

``` latex
\documentclass{ctexart}
\begin{document}
在\LaTeX{}中排版中文。
\end{document}
```

注意源代码须保存为 UTF-8 编码，并使用 xelatex 或 lualatex 命令编译。

## 字符元素

### 空格和分段

空格键和 Tab 键输入的空白字符视为“空格”。连续的若干个空白字符视
为一个空格。一行开头的空格忽略不计。

行末的换行符视为一个空格；但连续两个换行符，也就是空行，会将文字分段。多个空行被视为一个空行。

### 注释

`%` 作为注释。在这个字符之后直到行末，所有的字符都被忽略，行末的换行符也不引入空格。

### 特殊字符

直接输入这些字符得不到对应的符号，还往往会出错，如果想要输入以上符号，需要使用以下带反斜线的形式输入，类似编程语言里的“转义”符
号：
``` latex
#   $   %   &  {  }  _  ^     ~     \
\# \$ \% \& \{ \} \_ \^{} \~{} \textbackslash
```

### 标点符号

单引号 ‘ 和 ’ 分别用 \` 和 ' 输入；双引号 “ 和 ” 分别用 \`\` 和 '' 输入。

## 断行与断页

### 单词间距

可以使用字符 `~` 输入一个不会断行的空格（高德纳称之为 tie，“带子”），通常用在英文人名、图表名称等上下文环境。

``` latex
Donald~E. Knuth
```

### 手动断行断页

确实需要手动断行，可使用如下命令：

``` latex
\\[⟨length⟩]
\\*[⟨length⟩]
\newline
```

有两点区别：

一是 `\\` 可以带可选参数 `⟨length⟩`，用于在断行处向下增加垂直间距，而 `\newline` 不带可选参数；二是 `\\` 也在表格、公式等地方用于换行，而 `\newline` 只用于文本段落中。

带星号的 `\\` 表示禁止在断行处分页。

断页的命令有两个：
``` latex
\newpage
\clearpage
```

通常情况下两个命令都起到另起一页的作用，区别在于：

第一，在双栏排版模式中 `\newpage` 起到另起一栏的作用，`\clearpage` 则能够另起一页；第二，在涉及浮动体的排版上行为不同。

# 文档元素

## 章节与目录

### 章节标题

通过不同的命令分割为章、节、小节。三个标准文档类 article、report 和 book 提供了划分章节的命令：

``` latex
\chapter{⟨title⟩}

\section{⟨title⟩}
\subsection{⟨title⟩}
\subsubsection{⟨title⟩}

\paragraph{⟨title⟩}
\subparagraph{⟨title⟩}
```

其中 \chapter 只在 report 和 book 文档类有定义。这些命令生成章节标题，并能够自动编号。上述命令除了生成带编号的标题之外，还向目录中添加条目，并影响页眉页脚的内容。

每个命令有两种变体：

- 带可选参数的变体：`\section[⟨short title⟩]{⟨title⟩}`

	标题使用 `⟨title⟩` 参数，在目录和页眉页脚中使用 `⟨short title⟩` 参数；

- 带星号的变体：`\section*{⟨title⟩}`

	标题不带编号，也不生成目录项和页眉页脚。

较低层次如 `\paragraph` 和 `\subparagraph` 即使不用带星号的变体，生成的标题默认也不带编号。

事实上，除 \part 外：

article 文档类带编号的层级为 \section / \subsection / \subsubsection 三级；report / book 文档类带编号的层级为 \chapter / \section / \subsection 三级。

LaTeX 及标准文档类并未提供为 \section 等章节命令定制格式的功能，这一功能由 titlesec 宏包提供。

### 目录

### 文档结构划分

## 标题页

## 交叉引用

## 脚注

## 特殊环境

### 列表

### 引用

### 代码

## 表格

## 图片

## 盒子

### 水平盒子

### 垂直盒子

### 标尺盒子

## 浮动体

### 标题

### 并排和子图表

# 数学公式

## AmS 宏集

## 公式排版基础

# 元素样式设定

## 字体字号

### 基本设置

### 选用字体宏包

### ctex 宏包或文档类中设置

## 文字强调

## 段落格式和间距

### 行距

### 段落格式

## 页面

### geometry 宏包

### 元素垂直对齐

## 页眉页脚

### 基本样式

### 更改内容

### fencyhdr 宏包

# 超链接功能

## 参考文献和 BibTex 工具

### 基本设置

### BibTex 数据库和样式

### 应用 BibTex 排版参考文献

### natbib 宏包

### biblatex 宏包

## 索引和 makeindex 工具

### makeindex 工具基础

### 索引项

## 特殊颜色

### 颜色设置

### 带颜色的文本和盒子

## 设置超链接

### hyperref 宏包

### PDF 书签

# 自定义环境

## 自定义命令和环境

### 定义新命令

### 定义环境

### xparse 宏包

## 自定义宏包和文档类

### 定义宏包

### 宏包互相调用

### 定义文档类

## 计数器

### 定义修改计数器

### 计数器的输出格式

## 其他可定制命令和参数