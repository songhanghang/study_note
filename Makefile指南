# MakeFile指南

> 参考: 
> 
> GNU make: [https://www.gnu.org/software/make/manual/make.html](https://www.gnu.org/software/make/manual/make.html)
> 
> Android.mk: [https://developer.android.com/ndk/guides/android_mk.html?hl=zh-cn](https://developer.android.com/ndk/guides/android_mk.html?hl=zh-cn)
> 
> 陈皓: [https://seisman.github.io/how-to-write-makefile/](https://seisman.github.io/how-to-write-makefile/)
> 
> 建议:
> 推荐使用Sublime Text进行makefile开发，Sublime Text支持makefile语法高亮，自动编译，编译错误提示，虽支持功能甚少，但聊胜于无，能让你感觉自己是在写代码，而不是在编辑文本😒

## Makefile介绍

### 起源
斯图亚特·费尔德曼在1977年在贝尔实验室里制作了这个软件。2003年，斯图亚特·费尔德曼因发明了这样一个重要的工具而接受了美国计算机协会（ACM）颁发的软件系统奖。
在make诞生之前，编译工作主要依赖于操作系统里面的类似于“make”、“install”功能的shell脚本。它可以批量执行生成目标的命令，并且可以完成依赖关系的检查。这是向现代编译环境发展的重要一步。
### 概述
> 维基百科：
Most often, the makefile directs make on how to compile and link a program. Using C/C++ as an example, when a C/C++ source file is changed, it must be recompiled. If a header file has changed, each C/C++ source file that includes the header file must be recompiled to be safe. Each compilation produces an object file corresponding to the source file. Finally, if any source file has been recompiled, all the object files, whether newly made or saved from previous compilations, must be linked together to produce the new executable program. These instructions with their dependencies are specified in a makefile. If none of the files that are prerequisites have been changed since the last time the program was compiled, no actions take place. For large software projects, using Makefiles can substantially reduce build times if only a few source files have changed

通常，makefile指定如何编译和链接程序。以C/C++为例，当C/C++源文件被改变时，必须重新编译。如果头文件已经改变，那么包含头文件的每个C/C++源文件都必须重新编译以确保安全。每个编译生成一个对应于源文件的目标文件。最后，如果所有源文件已经被重新编译，所有的目标文件，无论是新建的还是从以前的编译中保存的，都必须链接在一起，以产生新的可执行程序。这些指令及其依赖关系在makefile中指定。如果自上次编译程序以来没有任何先决条件的文件发生更改，则不会执行任何操作。对于大型软件项目，如果只有少数源文件发生更改，则使用Makefile可以显着缩短生成时间。

## 
### 核心规则
``` makefile
target ... : prerequisites ...
    command
    ...
    ...
```
**target**
可以是一个object file*（目标文件）*，也可以是一个执行文件，还可以是一个标签*（label）*
**prerequisites**
生成该target所依赖的文件或target
**command**
该target要执行的命令*（任意的shell命令）*

makefile最核心内容：  ***prerequisites中如果有一个以上的文件比target文件要新的话，command所定义的命令就会被执行。***
### 如何工作

```Shell
# 自动编译当前目录下名字叫“Makefile”或“makefile”的文件（不带后缀）
make
# 或者指定自定义名字makefile文件
make -f custom.mk
```
1. make会查找文件中第一个target文件，如果能够找到并且target所依赖的prerequisites文件的修改时间不比target文件新，则将其作为最终的目标文件。
2. 否则，执行后面定义的command生成新的target文件。
3. 如果prerequisites文件也不存在，那么make会在当前makefile中找到prerequisites作为target的依赖，如果找到再根据其规则生成该prerequisites文件，以此递归，直至找到递归出口收拢结束。

### 包含内容
Makefile里主要包含了五个东西：显式规则、隐晦规则、变量定义、文件指示和注释。

1. 显式规则。显式规则说明了如何生成一个或多个目标文件。这是由Makefile的书写者明显指出要生成的 文件、文件的依赖文件和生成的命令。
2. 隐晦规则。由于我们的make有自动推导的功能，所以隐晦的规则可以让我们比较简略地书写 Makefile，这是由make所支持的。
3. 变量的定义。在Makefile中我们要定义一系列的变量，变量一般都是字符串，这个有点像你C语言中的宏，当Makefile被执行时，其中的变量都会被扩展到相应的引用位置上。
4. 文件指示。其包括了三个部分，一个是在一个Makefile中引用另一个Makefile，就像C语言中的include一样；另一个是指根据某些情况指定Makefile中的有效部分，就像C语言中的预编译#if一 样；还有就是定义一个多行的命令。有关这一部分的内容，我会在后续的部分中讲述。
5. 注释。Makefile中只有行注释，和UNIX的Shell脚本一样，其注释是用 # 字符，这个就 像C/C++中的//一样。如果你要在你的Makefile中使用 # 字符，可以用反斜杠进行 转义，如： \# 。

最后，还值得一提的是，在Makefile中的命令，必须要以 Tab 键开始。

### 引用其它makefile
```makefile
# filename 可包含路径和通配符
include <filname>
```
include 前可有空格，but绝不可以 Tab 键开始，make命令开始时，会找寻 include 所指出的其它Makefile，并把其内容在当前的位置展开。如果文件都没有指定绝对路径或是相对路径的话，make会在当前目录下首先寻找，如果当前目录下没有找到，那么，make还会在下面的几个目录下找。
1. 如果make执行时，有 -I 或 --include-dir 参数，那么make就会在这个参数所指定的目 录下去寻找。
2. 如果目录 <prefix>/include （一般是： /usr/local/bin 或 /usr/include ）存在的话，make也会去找。

如果有文件没有找到的话，make会生成一条警告信息，但不会马上出现致命错误。它会继续载入其它的文件，一旦完成makefile的读取，make会再重试这些没有找到，或是不能读取的文件，如果还是不行，make才会出现一条致命信息。如果你想让make不理那些无法读取的文件，而继续执行，你可以 在include前加一个减号“-”。如：

```
# - 错误跳过
-include <filename>
```

### 执行顺序

1. 读入执行的Makefile。
2. 读入被include的其它Makefile。
3. 初始化文件中的变量。

        Note: target目标变量此时不会展开求值！！！
        Note: 读取Makefile时就计算条件表达式的值，并根据条件表达式的值来选择语句。 
          PS:（个人理解： 1, 2 是包含 3 的，读入的同时初始化变量）
4. 推导隐晦规则，并分析所有规则。
5. 为所有的目标文件创建依赖关系链。
6. 根据依赖关系，决定哪些目标要重新生成。

        Note: 执行命令前，会展开所有$(varname)变量进行求值，如果找不到该变量，则返回对应的string，``（eg：$(songhanghang) 找不到则返回songhanghang），
7. 执行生成命令。 

        Note: target变量已经展开求值！！！
              在command中引用shell变量需使用$$varname， (eg: $$(ls | grep "haha") 执行command时会对其求值)

## Makefile规则

### 语法
*  规则包含两个部分，一个是依赖关系，一个是生成目标的方法
* command 前面必须以tab开头，如果command过长可以使用反斜杠“\”进行换行
* command 会以UNIX的标准Shell进行执行，即`/bin/sh`来执行命令
 
``` makefile
# 依赖关系
targets : prerequisites
# 目标生成方法
    command
    ...
```
或者

``` makefile
# 依赖关系                目标生成方法
targets : prerequisites; command
    command
    ...
```
### 通配符

通配符和UNIX的Shell一致

```
*.mtz     所有后缀mtz的文件  
~/miui     根目录下的miui文件，即$HOME/miui
...
```

### 文件搜索

make在查找文件依赖时可以指定文件路径，（eg：/Users/songhang/miuidev），也可以通过 ***特殊变量 `VPATH`*** 来告诉make路径，让其自动查找。当make在当前目录找不到时，则会到指定的目录进行查找。
 

```
# 由 ：“冒号”分割，按照先后顺序查找
VPATH = theme ： ../miui
```
另可以通过 ***关键字 `vpath`***更灵活的控制搜索目录，注意是关键字不是变量。

```
# Note: vapth使用方法中的<pattern>需要包含 % 字符，% 的意思是匹配零或若干字符

# 为符合模式<pattern>的文件指定搜索目录<directories>
vpath <pattern> <directories>

# eg：从平级目录miui中查找所有后缀mtz文件
vpath %.mtz ../miui

# 清除符合模式<pattern>的文件的搜索目录
vpath <pattern>

# eg：清除之前定义的../miui搜索目录
vpath %.mtz

# 清除所有已被设置好了的文件搜索目录
vpath
```
### 伪目标

```
.PHONY : clean
clean:
    rm -rf miui/out
```
`.PHONY` 即是这个伪目标，因为并不生成 `clean` 文件，所以伪目标并不是一个文件，只是一个标签，make无法生成它的依赖关系和决定他是否执行，只有通过显式的指定这个"目标" `make clean ` 才能使其执行，为避免与文件重名，一般使用 `.PHONY` 来显式指明这个伪目标。
伪目标一般没有依赖的文件。但是，也可以为伪目标指定所依赖的文件。

### 多目标


```
target1 target2 : /miui/icons
zip -rq /miui/icons $@
```
等价于

```
# $@ == target1
target1 : /miui/icons
zip -rq /miui/icons $@

# $@ == target2
target2 : /miui/icons
zip -rq /miui/icons $@
```

### 静态模式

```
# targets定义了一系列的目标文件，可以有通配符。是目标的一个集合
# target-parrtern是指明了targets的模式，也就是的目标集模式
# prereq-parrterns是目标的依赖模式，它对target-parrtern形成的模式再进行一次依赖目标的定义
<targets ...> : <target-pattern> : <prereq-patterns ...>
    <commands>
    ...
   
# 匹配 targets 中以.o为后缀的 target，用对应的miui1.c miui2.c 编译生成
targets = miui1.o miui2.o
$(targets) : %.o : %.c
...
```
等价于

```
miui1.o : miui1.c
...
miui2.o : miui2.c
...
```

### 自动生成依赖

```
# 把变量中所有.c换成.mk
sources = miui1.c miui2.c

include $(sources:.c=.mk)
```
等价于

```
include miui1.mk
include miui2.mk
```

## Makefile命令

### 显示命令

默认执行 command 时会打印该 command，如果不想打印该 command 可以在前加 `@`

```
# command
echo 正在编译miui...
```
输出结果

```
echo 正在编译miui...
正在编译miui...
```

```
# command
@echo 正在编译miui...
```
输出结果

```
正在编译miui...
```

只显示命令，但不执行命令，方便用于调试makefile

```
make -n
make --just-print
```
安静执行，不显示命令

```
make -s
make --silent
make --quiet
```
### 命令执行
* 每行命令处于独立的进程，作用域限于当前行，离开当前行失效。
* 可以用 `;` 分隔写在一行表示一行。
* 可以用 `;\` 分隔写在两行表示一行。


```
# pwd 输出根目录 eg: /songhang/
cd miui/icons
pwd

# pwd 输出 miui/icons
cd miui/icons; pwd

# pwd 输出 miui/icons
cd miui/icons;\
pwd
```
### 命令出错
```
# 执行命令忽略错误
make -i
make --ignore-errors

# 如果某规则中的命令出错了，那么就终止该规则的执行，但继续执行其它规则
make -k
make --keep-going
```
而如果一个规则是以 `.IGNORE` 作为目标的，那么这个规则中的所有命令将会忽略错误。

### 嵌套执行make
嵌套命令执行make，可以不通过`include`执行其他makefile

```
cgoogle:
    cd googledir && $(MAKE)
```
等价于

```
cgoogle:
    $(MAKE) -C googledir
```

### 定义命令包
如果makefile中出现相同命令序列，那么可以为其定义一个变量，类似于定义方法。

```
# $@ == mi6 $^ == Snapdragon835
define makemi
    zip -rq $@ $^
endef

mi6 : Snapdragon835
    $(makemi)
```
## Makefile变量
### 变量基础

变量声明时需要赋予初值，使用时需要前面加 `$` ，然后用 `()` 把变量包起来。

Note:

```
# Note: 声明变量时添加一个或者多个空格以后增加#注释，会对该变量增加一个空格
dir := /miui/icons    # directory to miui icons
all :
# 此处会error，因/miui/icons /res找不到
    cd $(dir)/res/
```

### 变量中引用变量

```
# 用 = 等号赋值，前面的变量可以引用后面的变量
a = $(b)
b = $(c)
c = are you ok ?
all:
    @echo $(a)
```
输出结果

```
are you ok ?
```
```
# 用 := 等号赋值，前面的变量不可以引用后面的变量
a := $(b)
b := $(c)
c := are you ok ?
all:
    @echo $(a)
```
输出结果

``` 
# 无输出
```


```
# 如果buyPhone没有被定义过，则赋值mix2，否则什么也不做
buyPhone ?= mix2
```
等价于

``` makefile
ifeq ($(origin buyPhone), undefined)
    buyPhone = mix2
endif
```
### 变量高级用法

```
# $(var:a=b) 把变量“var”中所有以“a”字串“结尾”的“a”替换成“b”字串
# themeMtz := 默认.mtz 无界.mtz
themeZip := 默认.zip 无界.zip
themeMtz := $(themeZip: .zip=.mtz)
```
### 追加变量


```
# += 追加变量
# iconsPath = /miui/v6/common/theme/icons /miui/v6/common/devices/theme/icons
iconsPath := /miui/v6/common/theme/icons
iconsPath += /miui/v6/common/devices/theme/icons
```
### override 指示符

```
# 如果有变量是通过make的命令行参数设置的，那么Makefile中对这个变量的赋值会被忽略。如果你想在Makefile中设置这类参数的值，那么，你可以使用“override”指示符
override <variable>; = <value>;
override <variable>; := <value>;
# 追加变量
override <variable>; += <more text>;
# 对于多行的变量定义，我们用define指示符，在define指示符前，也同样可以使用override指示符
override define foo
bar
endef
``` 

### 多行变量

还有一种设置变量值的方法是使用define关键字。使用define关键字设置变量的值可以有换行，这有利于定义一系列的命令（前面***Makefile命令/定义命令包***也是使用该关键字）。

`define` 指示符后面跟的是变量的名字，而重起一行定义变量的值，定义是以 `endef` 关键字结束。其工作方式和 `=` 操作符一样。变量的值可以包含函数、命令、文字，或是其它变量。因为命令需要以 `Tab` 键开头， 所以如果你用 `define` 定义的命令变量中没有以 `Tab` 键开头，那么make就不会把其认为是命令。

```
define miphone
echo D1
echo D2
endef
```

### 环境变量
make运行时的系统环境变量可以在make开始运行时被载入到Makefile文件中，但是如果Makefile中已定义了这个变量，或是这个变量由make命令行带入，那么系统的环境变量的值将被覆盖。（如果make指定了“-e”参数，那么，系统环境变量将覆盖Makefile中定义的变量）

因此，如果我们在环境变量中设置了 CFLAGS 环境变量，那么我们就可以在所有的Makefile中使用这个变量了。这对于我们使用统一的编译参数有比较大的好处。如果Makefile中定义了CFLAGS，那么则会使用Makefile中的这个变量，如果没有定义则使用系统环境变量的值，一个共性和个性的统一，很像“全局变量”和“局部变量”的特性。

### 目标变量

```
# 目标变量为目标target设置局部变量，这种变量被称为“Target-specific Variable”
# 它可以和“全局变量”同名，因为它的作用范围只在这条规则以及连带规则中，所以其值也只在作用范围内有效。
# 而不会影响规则链以外的全局变量的值。
 <target ...> : <variable-assignment>;
 <target ...> : overide <variable-assignment>
 
# eg:
$(default_theme): PRIVATE_OUT_THEME_PATH := $(ANDROID_PRODUCT_OUT)/system/media/theme
```

### 模式变量

```
# 模式变量为目标target给定一种“模式”，可以把变量定义在符合这种模式的所有目标上。
# 这种变量被称为(Pattern-specific Variable）。
# make的“模式”一般是至少含有一个 % 的，
# <pattern ...>; : <variable-assignment>;
# <pattern ...>; : override <variable-assignment>;
# 为所有后缀为mtz的目标设置目标变量version = 1
%.mtz : version = 1
```
## Makefile条件判断
### 语法

```
# 等价于 java 
# if () {
#   ...
# }
# 
# endif 结束符最近距离配对
<conditional-directive>
<text-if-true>
endif
```
或者

```
# 等价于 java 
# if () {
#   ...
# } else {
#   ...
# }
#
# endif 结束符最近距离配对
<conditional-directive>
<text-if-true>
else
<text-if-false>
endif
```

\<conditional-directive> 指示条件有 `ifeq`、`ifneq`、`ifdef`、`ifndef` 关键字开头，指示条件不能以 `tab` 开头，指示条件后必须有空格 ` ` 。

Note: make是在读取Makefile时就计算条件表达式的值，并根据条件表达式的值来选择语句， 所以，最好不要把自动化变量（如 `$@` 等）放入条件表达式中，因为自动化变量是在运行时才有的。

### 示例
```
mifans := true
rich = 

ifeq (true, mifans)
ifdef rich 
# buy mix2
else 
# buy red4x
endif # endif 对应 ifdef rich 
else
# can try mi phone
endif # endif 对应 ifeq (true, mifans)
```

## Makefile函数
### 语法

```
# <function> 函数名
# <arguments> 参数
# 函数名与参数之间以 "空格" 分割 ; 参数间以逗号 "," 分割; 函数调用以 "$" 开头。
$(<function> <arguments>)
```
或者

```
${<function> <arguments>}
```
### 字符串处理函数

```
# 字符串替换函数
$(subst <from>,<to>,<text>)

# 模式字符串替换函数
$(patsubst <pattern>,<replacement>,<text>)

# 去空格函数
$(strip <string>)

# 查找字符串函数
$(findstring <find>,<in>)

# 过滤函数
$(filter <pattern...>,<text>)

# 反过滤函数
$(filter-out <pattern...>,<text>)

# 排序函数
$(sort <list>)

# 取单词函数
$(word <n>,<text>)

# 取单词串函数
$(wordlist <ss>,<e>,<text>)

# 单词个数统计函数
$(words <text>)

# 首单词函数——firstword
$(firstword <text>)
```
### 文件处理函数

```
# 取目录函数——dir
$(dir <names...>)

# 取文件函数——notdir
$(notdir <names...>)

# 取后缀函数——suffix。
$(suffix <names...>)

# 取前缀函数——basename
$(basename <names...>)

# 加后缀函数——addsuffix
$(addsuffix <suffix>,<names...>)

# 加前缀函数——addprefix
$(addprefix <prefix>,<names...>)

# 连接函数——join
$(join <list1>,<list2>)
```

### foreach 循环函数


```
# <var> 变量名
# <list> 集合，也可以是表达式
# <text> 使用 <var> 参数依赖枚举 <list> 中单词

# 参数 <list> 中的单词逐一取出放到参数 <var> 所指定的变量中， 然后再执行 <text> 所包含的表达式。
# 每一次 <text> 会返回一个字符串，循环过程中，<text> 的所返回的每个字符串会以空格分隔，最后当整个循环结束时，
# <text> 所返回的每个字符串所组成的整个字符串（以空格分隔）将会是foreach函数的返回值 

$(foreach <var>,<list>,<text>)

```
### if函数

if函数很像make条件语句 `ifeq` 。

```
# <condition> 条件
# <then-part> 条件成立执行
$(if <condition>,<then-part>)
```
或者

```
# <condition> 条件
# <then-part> 条件成立执行
# <else-part> 条件不成立执行
$(if <condition>,<then-part>,<else-part>)
```
### call 函数

#### 语法
```
# call函数是唯一一个可以用来创建新的参数化的函数。
# 你可以写一个非常复杂的表达式，这个表达式中，
# 你可以定义许多参数，然后你可以call函数来向这个表达式传递参数。

$(call <expression>,<parm1>,<parm2>,...,<parmn>)
```
#### 示例

```
# 当make执行这个函数时， <expression> 参数中的变量，如 $(1) 、 $(2) 等，
# 会被参数 <parm1> 、 <parm2> 、 <parm3> 依次取代。
# 而 <expression> 的返回值就是 call 函数的返回值
showPhone := $(1) $(2) $(3)
result := $(call showPhone, mix2, red4x, iphoneX)
```
### origin函数
#### 语法
origin函数不像其它的函数，他并不操作变量的值，他只是告诉你你的这个变量是哪里来的？

Note:  \<variable> 是变量的名字，不应该是引用。所以你最好不要在 \<variable> 中使用
`$ ` 字符。Origin函数会以其返回值来告诉你这个变量的“出生情况”，下面，是origin函数的返回值:
`undefined`
如果 \<variable> 从来没有定义过，origin函数返回这个值 undefined
`default`
如果 \<variable> 是一个默认的定义，比如“CC”这个变量，这种变量我们将在后面讲述。
`environment`
如果 \<variable> 是一个环境变量，并且当Makefile被执行时， -e 参数没有被打开。
`file`
如果 \<variable> 这个变量被定义在Makefile中。
`command line`
如果 \<variable> 这个变量是被命令行定义的。
`override`
如果 \<variable> 是被override指示符重新定义的。
`automatic`
如果 \<variable> 是一个命令运行中的自动化变量。关于自动化变量将在后面讲述。

```
$(origin <variable>)
```
#### 示例

```
# 如果ICONS_PATH是环境变量
ifeq ("environment", $(origin ICONS_PATH))
# do someting
endif
```
### Shell函数
不同于其他函数，它的参数就是操作系统Shell的命令，与反引号  `  是相同的功能。

Note: 这个函数会新生成一个Shell程序来执行命令，所以要注意其运行性能。

Note: 如果执行在 `commond` 中，会在执行前提前展开，需要使用 `$$` 才能正常执行。

```
# 获取当前目录下文件列表
icons := $(shell ls ./)
```
### 控制make的函数
#### 语法
make提供了一些函数来控制make的运行。通常，你需要检测一些运行Makefile时的运行时信息，并且根据这些信息来决定，让make继续执行，还是停止。

```
$(error <text ...>)
```
警告方法，只输出警告信息，然后继续执行。

```
$(warning <text ...>)
```
#### 示例

```
$(error "is time fc !")
$(warning "dir not found!")
```
## Makefile运行

### 退出码
make命令执行后有三个退出码：

0
表示成功执行。
1
如果make运行时出现任何错误，其返回1。
2
如果你使用了make的“-q”选项，并且make使得一些目标不需要更新，那么返回2。
### 指定Makefile

```
make -f Android.mk
make --file Android.mk
make --makefile Android.mk
```
### 指定目标

```
.PHONY: all
all : a b
a :
    echo ---- aaaaa -----
b : 
    echo ---- bbbbb -----
```
执行输出

```
# 输出 ---- aaaaa -----, ---- bbbbb -----
make

# 输出 ---- aaaaa -----, ---- bbbbb -----
make all

# 输出 ---- aaaaa -----
make a

# 输出 ---- bbbbb -----
make b
```
### 检查规则
有时候不希望makefile执行，只想检查一下命令，或是执行的序列。可以使用make命令的下述参数：

`-n` ,  `--just-print` , ` --dry-run` ,  `--recon`
不执行参数，这些参数只是打印命令，不管目标是否更新，把规则和连带规则下的命令打印出来，但不 执行，这些参数对于我们调试makefile很有用处。
`-t` ,  `--touch`
这个参数的意思就是把目标文件的时间更新，但不更改目标文件。也就是说，make假装编译目标，但不 是真正的编译目标，只是把目标变成已编译过的状态。
`-q` ,  `--question`
这个参数的行为是找目标的意思，也就是说，如果目标存在，那么其什么也不会输出，当然也不会执行 编译，如果目标不存在，其会打印出一条出错信息。
`-W <file>` ,  `--what-if=<file>` ,  `--assume-new=<file>` ,  `--new-file=<file>`
这个参数需要指定一个文件。一般是是源文件（或依赖文件），Make会根据规则推导来运行依赖于这个 文件的命令，一般来说，可以和“-n”参数一同使用，来查看这个依赖文件所发生的规则命令

### make的参数

`-b, -m`
这两个参数的作用是忽略和其它版本make的兼容性。

`-B, --always-make`
认为所有的目标都需要更新（重编译）。

`-C <dir>, --directory=<dir>`
指定读取makefile的目录。如果有多个“-C”参数，make的解释是后面的路径以前面的作为相对路径 ，并以最后的目录作为被指定目录。如：`“make -C ~hchen/test -C prog”`等价于 `“make -C ~hchen/test/prog”`。

`-debug[=<options>]`
输出make的调试信息。它有几种不同的级别可供选择，如果没有参数，那就是输出最简单的调试信息。 下面是<options>的取值：

* a: 也就是all，输出所有的调试信息。（会非常的多）
* b: 也就是basic，只输出简单的调试信息。即输出不需要重编译的目标。
* v: 也就是verbose，在b选项的级别之上。输出的信息包括哪个makefile被解析，不需要被重编 译的依赖文件（或是依赖目标）等。
* i: 也就是implicit，输出所以的隐含规则。
* j: 也就是jobs，输出执行规则中命令的详细信息，如命令的PID、返回码等。
* m: 也就是makefile，输出make读取makefile，更新makefile，执行makefile的信息。

`-d`
相当于“–debug=a”。

`-e, --environment-overrides
`指明环境变量的值覆盖makefile中定义的变量的值。

`-f=<file>, --file=<file>, --makefile=<file>`
指定需要执行的makefile。

`-h, --help`
显示帮助信息。

`-i , --ignore-errors`
在执行时忽略所有的错误。

`-I <dir>, --include-dir=<dir>`
指定一个被包含makefile的搜索目标。可以使用多个“-I”参数来指定多个目录。

`-j [<jobsnum>], --jobs[=<jobsnum>]`
指同时运行命令的个数。如果没有这个参数，make运行命令时能运行多少就运行多少。如果有一个以上的“-j”参数，那么仅最后一个“-j”才是有效的。（注意这个参数在MS-DOS中是无用的）

`-k, --keep-going`
出错也不停止运行。如果生成一个目标失败了，那么依赖于其上的目标就不会被执行了。

`-l <load>, --load-average[=<load>], -max-load[=<load>]`
指定make运行命令的负载。

`-n, --just-print, --dry-run, --recon`
仅输出执行过程中的命令序列，但并不执行。

`-o <file>, --old-file=<file>, --assume-old=<file>`
不重新生成的指定的<file>，即使这个目标的依赖文件新于它。

`-p, --print-data-base`
输出makefile中的所有数据，包括所有的规则和变量。这个参数会让一个简单的makefile都会输出一堆信息。如果你只是想输出信息而不想执行makefile，你可以使用`“make -qp”`命令。如果你想查看执行makefile前的预设变量和规则，你可以使用 `“make –p –f /dev/null”`。这个参数输出的 信息会包含着你的makefile文件的文件名和行号，所以，用这个参数来调试你的 makefile会是很有 用的，特别是当你的环境变量很复杂的时候。

`-q, --question`
不运行命令，也不输出。仅仅是检查所指定的目标是否需要更新。如果是0则说明要更新，如果是2则说 明有错误发生。

`-r, --no-builtin-rules`
禁止make使用任何隐含规则。

`-R, --no-builtin-variabes`
禁止make使用任何作用于变量上的隐含规则。

`-s, --silent, --quiet`
在命令运行时不输出命令的输出。

`-S, --no-keep-going, --stop`
取消“-k”选项的作用。因为有些时候，make的选项是从环境变量“MAKEFLAGS”中继承下来的。所以你 可以在命令行中使用这个参数来让环境变量中的“-k”选项失效。

`-t, --touch`
相当于UNIX的touch命令，只是把目标的修改日期变成最新的，也就是阻止生成目标的命令运行。

`-v, --version`
输出make程序的版本、版权等关于make的信息。

`-w, --print-directory`
输出运行makefile之前和之后的信息。这个参数对于跟踪嵌套式调用make时很有用。

`--no-print-directory`
禁止“-w”选项。

`-W <file>, --what-if=<file>, --new-file=<file>, --assume-file=<file>`
假定目标<file>;需要更新，如果和“-n”选项使用，那么这个参数会输出该目标更新时的运行动作。 如果没有“-n”那么就像运行UNIX的“touch”命令一样，使得<file>;的修改时间为当前时间。

`--warn-undefined-variables`
只要make发现有未定义的变量，那么就输出警告信息。

## 隐含规则
隐含规则涵盖很多，在此之阐述模板规则和自动化变量
### 模板规则
#### 规则
即规则的目标定义中必须包含 `% `， `% `表示长度任意的非空字符串。

```
%.o : %.c ; <command ......>;
```
#### 示例

```
# 所有后缀为mtz依赖其名称后缀为zip文件
%.mtz : %.zip 
    # do someting
```
### 自动化变量
`$@` : 表示规则中的目标文件集。在模式规则中，如果有多个目标，那么， `$@` 就是匹配于目标中模式定义的集合。

`$%` : 仅当目标是函数库文件中，表示规则中的目标成员名。例如，如果一个目标是 `foo.a(bar.o)` ， 那么， `$%` 就是 `bar.o` ， `$@` 就是 `foo.a `。如果目标不是函数库文件（Unix下是 `.a` ，Windows下是 `.lib` ），那么，其值为空。

`$<` : 依赖目标中的第一个目标名字。如果依赖目标是以模式（即 `%` ）定义的，那么 `$<` 将是符合模式的一系列的文件集。注意，其是一个一个取出来的。

`$?` : 所有比目标新的依赖目标的集合。以空格分隔。

`$^` : 所有的依赖目标的集合。以空格分隔。如果在依赖目标中有多个重复的，那个这个变量会去除重复的依赖目标，只保留一份。

`$+ `: 这个变量很像 `$^` ，也是所有依赖目标的集合。只是它不去除重复的依赖目标。

`$*` : 这个变量表示目标模式中 `%` 及其之前的部分。如果目标是 `dir/a.foo.b` ，并且目标的模式是 `a.%.b` ，那么， `$*` 的值就是 `dir/a.foo` 。这个变量对于构造有关联的文件名是比较有效。如果目标中没有模式的定义，那么 `$*` 也就不能被推导出，但是，如果目标文件的后缀是make所识别的，那么 `$*` 就是除了后缀的那一部分。例如：如果目标是 `foo.c` ，因为 `.c` 是make所能识别的后缀名，所以， `$*` 的值就是 `foo` 。这个特性是GNU make的， 很有可能不兼容于其它版本的make，所以，你应该尽量避免使用 `$*` ，除非是在隐含规则或是静态模式中。如果目标中的后缀是make所不能识别的，那么 `$*` 就是空值。

针对于以上变量可以增加 `D` `F` 字样。
`D ` dir  取目录部分
`F`  file 取文件部分

`$(@D)`
表示 `$@ `的目录部分（不以斜杠作为结尾），如果 `$@` 值是 `dir/foo.o` ，那么 `$(@D)` 就是 `dir` ，而如果 `$@ `中没有包含斜杠的话，其值就是 `.` （当前目录）。

`$(@F)`
表示 `$@` 的文件部分，如果 `$@ `值是 `dir/foo.o` ，那么 `$(@F)` 就是 `foo.o` ， `$(@F)` 相当于函数 `$(notdir $@)` 。



## 后记
原作者：
陈皓：[https://coolshell.cn/haoel ](https://coolshell.cn/haoel )
《跟我一起写Makefile》2004年 

排版：
miui-songhang：[https://github.com/songhanghang](https://github.com/songhanghang)

膜拜先辈！！！
本文按 `代码 + 注释` 的思路重新整理，剔除一些章节和解释，加入一些自己的理解，如有错误，欢迎指正。

<!-- 排版 MIUI-THEME-宋航 -->

