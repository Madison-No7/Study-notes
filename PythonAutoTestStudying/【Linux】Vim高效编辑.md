**目录**

[一、vim的基本概念](#%E4%B8%80%E3%80%81vim%E7%9A%84%E5%9F%BA%E6%9C%AC%E6%A6%82%E5%BF%B5)

[💡 Vim 的核心定位与优势：](#%F0%9F%92%A1%C2%A0Vim%20%E7%9A%84%E6%A0%B8%E5%BF%83%E5%AE%9A%E4%BD%8D%E4%B8%8E%E4%BC%98%E5%8A%BF%EF%BC%9A)

[二、vim的使用](#%E4%BA%8C%E3%80%81vim%E7%9A%84%E4%BD%BF%E7%94%A8)

[2.1 进入vim](#2.1%20%E8%BF%9B%E5%85%A5vim)

[2.2 Vim 的核心工作模式（三种）](#2.2%C2%A0Vim%20%E7%9A%84%E6%A0%B8%E5%BF%83%E5%B7%A5%E4%BD%9C%E6%A8%A1%E5%BC%8F%EF%BC%88%E4%B8%89%E7%A7%8D%EF%BC%89)

[✨三种模式之间的切换图：](#%E2%9C%A8%E4%B8%89%E7%A7%8D%E6%A8%A1%E5%BC%8F%E4%B9%8B%E9%97%B4%E7%9A%84%E5%88%87%E6%8D%A2%E5%9B%BE%EF%BC%9A)

[2.2.1 vim正常模式命令集](#2.2.1%C2%A0vim%E6%AD%A3%E5%B8%B8%E6%A8%A1%E5%BC%8F%E5%91%BD%E4%BB%A4%E9%9B%86)

[🌸 进入插⼊模式：](#%F0%9F%8C%B8%C2%A0%E8%BF%9B%E5%85%A5%E6%8F%92%E2%BC%8A%E6%A8%A1%E5%BC%8F%EF%BC%9A)

[🌸 光标移动：](#%F0%9F%8C%B8%C2%A0%E5%85%89%E6%A0%87%E7%A7%BB%E5%8A%A8%EF%BC%9A)

[🌸 编辑文本：](#%F0%9F%8C%B8%C2%A0%E7%BC%96%E8%BE%91%E6%96%87%E6%9C%AC%EF%BC%9A)

[⚡复制与粘贴：（可批量化操作）](#%E2%9A%A1%E5%A4%8D%E5%88%B6%E4%B8%8E%E7%B2%98%E8%B4%B4%EF%BC%9A%EF%BC%88%E5%8F%AF%E6%89%B9%E9%87%8F%E5%8C%96%E6%93%8D%E4%BD%9C%EF%BC%89)

[⚡撤销上⼀次操作：](#%E2%9A%A1%E6%92%A4%E9%94%80%E4%B8%8A%E2%BC%80%E6%AC%A1%E6%93%8D%E4%BD%9C%EF%BC%9A)

[⚡剪切：](#%E2%9A%A1%E5%89%AA%E5%88%87%EF%BC%9A)

[⚡行内剪切：](#%E2%9A%A1%E8%A1%8C%E5%86%85%E5%89%AA%E5%88%87%EF%BC%9A)

[⚡替换模式：](#%E2%9A%A1%E6%9B%BF%E6%8D%A2%E6%A8%A1%E5%BC%8F%EF%BC%9A)

[⚡大小写字母之间的切换：](#%E2%9A%A1%E5%A4%A7%E5%B0%8F%E5%86%99%E5%AD%97%E6%AF%8D%E4%B9%8B%E9%97%B4%E7%9A%84%E5%88%87%E6%8D%A2%EF%BC%9A)

[⚡批量化注释：](#%E2%9A%A1%E6%89%B9%E9%87%8F%E5%8C%96%E6%B3%A8%E9%87%8A%EF%BC%9A)

[⚡批量化删除：](#%E2%9A%A1%E6%89%B9%E9%87%8F%E5%8C%96%E5%88%A0%E9%99%A4%EF%BC%9A)

[⚡视图模式：](#%E2%9A%A1%E8%A7%86%E5%9B%BE%E6%A8%A1%E5%BC%8F%EF%BC%9A)

[⚡查找:](#%E2%9A%A1%E6%9F%A5%E6%89%BE%3A)

[2.2.2 vim底行模式命令集](#2.2.2%20vim%E5%BA%95%E8%A1%8C%E6%A8%A1%E5%BC%8F%E5%91%BD%E4%BB%A4%E9%9B%86)

[🌸 保存⽂件：](#%F0%9F%8C%B8%C2%A0%E4%BF%9D%E5%AD%98%E2%BD%82%E4%BB%B6%EF%BC%9A)

[🌸 离开vim:](#%F0%9F%8C%B8%C2%A0%E7%A6%BB%E5%BC%80vim%3A)

[🌸 列出⾏号：](#%F0%9F%8C%B8%C2%A0%E5%88%97%E5%87%BA%E2%BE%8F%E5%8F%B7%EF%BC%9A)

[🌸 查找字符：](#%F0%9F%8C%B8%C2%A0%E6%9F%A5%E6%89%BE%E5%AD%97%E7%AC%A6%EF%BC%9A)

[🌸 不退出vim，执行命令：](#%F0%9F%8C%B8%C2%A0%E4%B8%8D%E9%80%80%E5%87%BAvim%EF%BC%8C%E6%89%A7%E8%A1%8C%E5%91%BD%E4%BB%A4%EF%BC%9A)

[🌸 批量化的替换:](#%F0%9F%8C%B8%C2%A0%E6%89%B9%E9%87%8F%E5%8C%96%E7%9A%84%E6%9B%BF%E6%8D%A2%3A)

[🌸 分屏操作：](#%F0%9F%8C%B8%C2%A0%E5%88%86%E5%B1%8F%E6%93%8D%E4%BD%9C%EF%BC%9A)

[✨五种常用模式之间的切换图：](#%E2%9C%A8%E4%BA%94%E7%A7%8D%E5%B8%B8%E7%94%A8%E6%A8%A1%E5%BC%8F%E4%B9%8B%E9%97%B4%E7%9A%84%E5%88%87%E6%8D%A2%E5%9B%BE%EF%BC%9A)

[2.3 使用Vim的一个小技巧：](#2.3%20%E4%BD%BF%E7%94%A8Vim%E7%9A%84%E4%B8%80%E4%B8%AA%E5%B0%8F%E6%8A%80%E5%B7%A7%EF%BC%9A)

[三、vim的配置](#%E4%B8%89%E3%80%81vim%E7%9A%84%E9%85%8D%E7%BD%AE)

[3.1 vim配置的文件](#3.1%20vim%E9%85%8D%E7%BD%AE%E7%9A%84%E5%8E%9F%E7%90%86)

[3.2 对vim进行配置](#3.2%20%E5%AF%B9vim%E8%BF%9B%E8%A1%8C%E9%85%8D%E7%BD%AE)

[拓展阅读](#%E6%8B%93%E5%B1%95%E9%98%85%E8%AF%BB%EF%BC%9A)

---

# 一、vim的基本概念

Vim 是一款在 Unix、Linux 及类 Unix 系统中广泛使用的**命令行文本编辑器**，它是 Vi 编辑器的增强版，核心特点是通过**键盘命令**高效操作，无需频繁依赖鼠标，是后端开发的必备。

### 💡 Vim 的核心定位与优势：

- **跨平台与预装性**：几乎所有 Linux、macOS 系统都默认预装 Vim，无需额外安装即可使用，在服务器环境中尤为常用。
- **命令驱动高效性**：通过快捷键和命令组合（如复制、粘贴、查找替换、行操作等），可减少鼠标操作，大幅提升文本编辑速度，尤其适合大文件或代码编辑。
- **可扩展性**：支持通过插件（如代码补全、语法高亮插件）、自定义配置（.vimrc 文件）优化功能，满足不同场景（如编程、写作）的需求。

![](https://i-blog.csdnimg.cn/direct/07f67b895b86400394a1e9f4bb628354.png)![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "点击并拖拽以移动")​编辑

# 二、vim的使用

## 2.1 进入vim

先创建好一个文件。通过vim+ 文件名进入vim编辑器。

**示例：**vim test.c

![](https://i-blog.csdnimg.cn/direct/f02c36d46e4a4c3f951e42b1cd78bac5.png)![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "点击并拖拽以移动")​编辑

回车进入；

![](https://i-blog.csdnimg.cn/direct/9b1f7bc6b0554a24b5a854fe8e80c4b8.png)![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "点击并拖拽以移动")​编辑

进⼊vim之后，是默认处于正常（命令）模式，你要切换到[插⼊模式]才能够输⼊⽂字。

## 2.2 Vim 的核心工作模式（三种）

🌊 **正常/普通/命令模式(Normal mode)：**打开 Vim 后的默认模式，用于执行命令（如移动光标、删除内容、复制粘贴），目的是快速编辑。

🌊 **插入模式**(Insert mode)**：**用于输入文本，从命令模式进入插入模式需按 `i`（在光标前插入）、`a`（在光标后插入）或 `o`（在当前行下方新建行插入）等键；按 `Esc` 键可返回命令模式。

  
🌊 **底行模式**(last line mode)**：**从命令模式按shift+;进入，用于执行更复杂的命令（如保存、退出、查找替换）。按 `Esc` 键可返回命令模式。

## ✨**三种模式之间的切换图：**

![](https://i-blog.csdnimg.cn/direct/fc8785a8a60a468a89e39e5c3e648f0a.png)![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "点击并拖拽以移动")​编辑

### 2.2.1 vim正常模式命令集

#### 🌸 **进入插⼊模式：**

- 按「i」切换进⼊插⼊模式「insert mode」，按“i”进⼊插⼊模式后是从光标当前位置开始输⼊ ⽂件；
    
- 按「a」进⼊插⼊模式后，是从⽬前光标所在位置的下⼀个位置开始输⼊⽂字；
    
- 按「o」进⼊插⼊模式后，是插⼊新的⼀⾏，从⾏⾸开始输⼊⽂字。
    

#### 🌸 **光标移动：**

- 按⼩写英⽂字⺟「h」、「j」、 「k」、「l」，分别控制光标左、下、上、右移⼀格，**记忆：**h在键盘的最左边，表示光标向左移动，l在键盘的最右边，表示光标向右移动，k想成king,国王，表示高高在上，向上移动，j想象成jump，你跳起来后，肯定会往下落，表示光标向下移动。
    
- 按「n+l」：光标向右移动n格，如：5+l,光标向右移动5格，类似的还有「n+h」「n+j」「n+k」；
    
- 按「w」：光标跳到下个单词的开头，前面也能加数字。
    
- 按「e」：光标跳到下个单词的结尾，前面也能加数字。
    
- 按「b」：光标跳到上一个单词的开头，前面也能加数字。
    
- 按「G」：移动到文本的最后
    
- 按「n+G」:定位光标到n行，比如按数字5+G,快速定位到文本的第5行
    
- 按［gg］：进⼊到⽂本最开始
    
- 按「 $ 」：移动到光标所在⾏的“⾏尾”
    
- 按「^」：移动到光标所在⾏的“⾏⾸“
    

#### 🌸 **编辑文本：**

##### ⚡**复制与粘贴：（可批量化操作）**

- 「yy」：复制光标所在⾏到缓冲区。
    
- 「n+yy」：例如，「6yy」表⽰拷⻉从光标所在的该⾏“往下数”6⾏⽂字。
    
- 「p」：将缓冲区内的字符贴到光标所在位置的下一行。
    
- 「n+p」:「6+p」表⽰在光标所在位置的下一行粘贴6行
    

##### ⚡**撤销上⼀次操作：**

- 「u」：如果您误执⾏⼀个命令，可以⻢上按下「u」，回到上⼀个操作。按多次“u”可以执⾏
    
- 多次回复。
    
- 「ctrl + r」: 撤销u操作
    

 **说明：**这两个操作，可以互相撤销彼此。比如你按了「u」后悔了，可以按「ctrl + r」恢复，你按「ctrl + r」后悔了，可以按「u」恢复。

 **注意：**一旦退出文本编辑（：q），再vim进入文本编辑，就无法再进行撤销了。只是（：w）保存可以撤销。

##### ⚡**剪切：**

- 「dd」：剪切光标所在⾏，不粘贴就是删除了

- 「n+dd」：从光标所在

##### ⚡**行内剪切：**

- 「x」：每按⼀次，删除光标所在位置的⼀个字符
    

- 「n+x」：例如，「6x」表⽰删除光标所在位置的“后⾯（包含⾃⼰在内）”6个字符,也就是光标左侧不动，右侧删除
    

- 「X」：⼤写的X，每按⼀次，删除光标所在位置的“前⾯”⼀个字符,也就是光标右侧不动，左侧删除。
    
- 「n+X」：例如，「20X」表⽰删除光标所在位置的“前⾯”20个字符
    

##### ⚡**替换模式：**

- 「r」：替换光标所在处的字符。
    
- 「n+r」:替换光标所在处后的n个字符。
    
- 「R」：替换光标所到之处的字符，直到按下「ESC」键为⽌。（批量化替换）
    

##### ⚡**大小写字母之间的切换：**

- 「~」:把光标所在处的字符进行大小写的切换。

##### ⚡**批量化注释：**

**步骤：**  
**1.**ctrl+v进入视图模式    **2.**利用**移动光标的操作**选择注释区域     **3.**shift+i进入插入模式     **4.**输入//  **5.**Esc退出到命令模式，完成注释。

##### ⚡**批量化删除：**

**步骤：**

1.ctrl+v进入视图模式    2.利用**移动光标的操作**选择需要删除的区域     3.输入d。

##### ⚡**视图模式：**

可以进行批量化的删除，插入操作。

1.ctrl+v进入视图模式    2.利用**移动光标的操作**选择要批量化的区域    3.shift+i 进入插入模式，输入要增加的信息     4.Esc退出后，之前批量化选择的区域就有输入的信息了。 

##### ⚡**查找:**

- 「#」选中当前单词的首字符，按「n」逆向定位到上一个单词的区域

### 2.2.2 vim底行模式命令集

#### 🌸 保存⽂件：

- 「w」: 在冒号后输⼊字⺟「w」就可以将⽂件保存起来

#### 🌸 **离开vim:**

- 「q」：按「q」就是退出，如果⽆法离开vim，可以在「q」后跟⼀个「!」强制离开vim。
    
- 「wq」：⼀般建议离开时，搭配「w」⼀起使⽤，这样在退出的时候还可以保存⽂件。
    
-   快捷方式 「shift+zz」:也可以退出；
    

#### 🌸 **列出⾏号：**

- 「set nu」: 输⼊「set nu」后，会在⽂件中的每⼀⾏前⾯列出⾏号。

- 「set nonu」:去掉行号。

#### 🌸 **查找字符：**

- 「/+关键字」: 先按「/」键，再输⼊您想寻找的字符，如果第⼀次找的关键字不是您想要的，可以 ⼀直按「n」会往后寻找到您要的关键字为⽌。
    

#### 🌸 不退出vim，执行命令**：**

- **：**进入底行，输入！+命令，表示在不退出vim的情况下，执行相应的命令。

#### 🌸 批量化的替换:

- 输入%s/vim编辑器中的信息/要替换的信息/

#### 🌸 **分屏操作：**

- :vs +新文件，开启分屏
    
- ctrl+ww，切换文件
    

![](https://i-blog.csdnimg.cn/direct/f213429c60274f32842236ff57558e36.png)![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "点击并拖拽以移动")​编辑

## ✨**五种常用模式之间的切换图：**

![](https://i-blog.csdnimg.cn/direct/02a945354c17467d882aba9d3da6cd61.png)![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "点击并拖拽以移动")​编辑

## 2.3 使用Vim的一个小技巧：

vim 文件名 +行号，进入vim编辑器，光标会自动跳转到对应的行号。这个比较有用，以后你在写代码时，可能报错信息会显示在哪一行的前面缺少一个；在你进入vim的时候加上出错的行号，进入之后，光标就在那一行了。

# 三、vim的配置

  未经配置的vim，在插入模式下写代码的时候，没有任何语法提醒，也没有自动补全功能，使用起来比较笨重。要实现在写代码的时候给出提示，需要我们自行对vim进行配置。

## 3.1 vim配置的文件

Vim 的配置文件分为两种，一种是全局配置文件（`/etc/vim/vimrc`），一种是用户配置文件（`/home/<Username>/.vimrc`）。全局配置文件会对系统内所有用户生效，包括 root 用户。而用户配置文件只对单个用户有效。

当你vim打开后，会自动到用户的家目录找**.vimrc配置文件，如果没有，vim就会用默认的，默认的就啥都没有。**

**所以配置vim的基本原理：**将自己所需要的配置项，写到配置文件`.vimrc`中，建议在自己的家目录下进行配置。

对普通用户的vim进行简单的配置：

![](https://i-blog.csdnimg.cn/direct/1ab86d0341b4438fb5c2411f8b6069ee.png)![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "点击并拖拽以移动")​编辑

普通用户的主目录下没有`.vimrc``文件`，那么需要自己创建一个**.vimrc的配置文件。紧接着vim打开.vimrc文件，写入配置信息。**

![](https://i-blog.csdnimg.cn/direct/68c1fafad7d04752af44ba439baa3985.png)![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "点击并拖拽以移动")​编辑

简单配置一下**行号**：

![](https://i-blog.csdnimg.cn/direct/146e530cd47b4be39b6f28fb4649d7e7.png)![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "点击并拖拽以移动")​编辑

用vim打开一个文件，行号就有了；

![](https://i-blog.csdnimg.cn/direct/a9ca3cc5f8914123a8700514dc56906f.png)![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "点击并拖拽以移动")​编辑

**说明：**在每一个用户自己家目录下配置的vim不会影响的其他普通用户，只对当前用户有效。而在**`/ect/`**目录下的**`.vimrc`**属于系统公共的vim配置文件，对所有用户都有效，一般不建议对公共的配置文件进行修改，各用户根据自己的喜好，在自己的家目录下进行配置就好。

## 3.2 对vim进行配置

我们只需明白vim的配置原理就可以了，对vim进行配置可以使用别人已经配置好的，这样方便又快捷。当然也可以自己配置，只不过会有点麻烦。

🔍**Ubuntu**配置**Vim**可参考这篇博客：[基于 Ubuntu 系统的 Vim 全面配置](https://blog.csdn.net/qq_42417071/article/details/139027077?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522f561977fd4720572605c7c83fb32eb3f%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fblog.%2522%257D&request_id=f561977fd4720572605c7c83fb32eb3f&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~blog~first_rank_ecpm_v1~hot_rank-1-139027077-null-null.nonecase&utm_term=ubuntu%E9%85%8D%E7%BD%AEvim&spm=1018.2226.3001.4450 "基于 Ubuntu 系统的 Vim 全面配置")

🔍**Centos7**配置**Vim**可参考：[VimForCpp](https://gitee.com/HGtz2222/VimForCpp "VimForCpp")

# 拓展阅读：

[Vim 从入门到精通](https://wsdjeg.net/vim-galore-zh-cn/#%E5%BB%B6%E7%94%9F%E9%98%85%E8%AF%BB "Vim 从入门到精通")

---

完。

![](https://i-blog.csdnimg.cn/direct/6e6af1a92a594c9fb54e3eab3d74761e.jpeg)![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "点击并拖拽以移动")​

​