# 此文件用于演示 gnu make 的用法
# author ： Sean

# 对于 GNU make 来说，该文件名有三种命名方式: makefile Makefile GNUmakefile
# 如果上述三种文件名的文件都存在，对于GNU make 来说，
# GNUmakefile》makefile》Makefile

# .DEFAULT_GOAL 表示默认目标，没有这一行的话，第一个目标即是默认目标
.DEFAULT_GOAL=demo

# make 可以自动推断，冒号右边和下面可以省略。
# 如果代码中一行内容过多，可以在该行最后加反斜杠“\”来换行。默认情况下，换行会多一个
# 空格，如果想忽略该空格，要在反斜杠前加“$”。

# 可以用变量，用法是 obj = hello.o , $(obj)或者${obj}

# include 指令用于暂停当前makefile的执行，而跳转到include 指定的文件去执行该文件。
include ./subfile_0/makefile.build

.PHONY:print
print : 
	@echo $(subMakefile)


# 头文件查找  .INCLUDE_DIRS 这个宏记录了默认搜索头文件的路径。
.PHONY:print_include_dir
print_include_dir:
	echo $(.INCLUDE_DIRS) 
# 如果源文件包含的头文件不在同一个目录下，会报错。
# 解决方式1 是把头文件的路径写完整，如 #include "include/demo2.h"
# 解决方式2 把头文件拷贝到 /usr/local/include 文件夹中，该文件夹默认是空的
# 解决方式3 编译命名后面 -I 头文件路径 如 g++ -o $(obj) -c demo1.cpp -Iinclude
# 		  通常情况下 -Iinclude 会用变量来命名，如 INCLUDES = -I./include/

# makefile 文件中能使用当前系统的环境变量（在bash里面，环境变量通过 env 命令来查看）
# 可以通过 export 来定义临时环境变量
# 运行的实际命令前加@可以在make+命令的时候不显示该命令，而直接执行该命令。
export HELLO := hi!!!
.PHONY:print_env
print_env:
	@echo $(HELLO)
	@echo $(USER)


# 单引号规则 ，如果目标文件后使用单引号，若目标最新，则不执行相应的命令
# 双引号规则 ，若使用双引号，则无条件执行相应的命令。
# 单个$后加变量，如果想输入$则应该写成$$，表示转义,开启了二阶段展开则需要写成$$$$


# 如果存在多个目标，且目标名相同，则以最后一个目标为准。

# GNU make 分为 立即展开 和 延迟展开
#  1.读取所有makefile和include makefile,
#       内在化所有显示规则和隐式规则的变量，
#       建立一个所有目标和依赖的关系图。
#  2.make 通过这些数据来决定执行更新目标和执行命令。
# 变量和函数的展开发生在第一阶段，称为立即展开，否则就称为延迟展开。


# GNU make 解析makefile的流程如下：
# 1.读取完整的逻辑行，包括反斜杠的转义
# 2.去除注释
# 3.如果该行的命令前缀字符tab开头，并且处于规则的上下文中，将该行添加到当前命令并阅读下一行。
# 4.将立即展开的元素展开
# 5.扫描行中的分隔符（如：，=），以确定该行是宏命令还是规则
# 6.内化生成的操作并阅读下一行。

# 第二阶段展开的宏是 .SECONDEXPANSION ,应用场景不太明确

#.SECONDEXPANSION:
#var0 = demo
#.PHONY:print_var0
#print_var0:$(var0)
#	@echo $^
#var0 = demo1.o


# 有两种依赖形式，一种是normal-prerequisites，一种是order-only-prerequisites。
# 普通依赖如果比目标新，则目标会被更新。
# order-only依赖不管比不比目标新，都不会更新目标。
# 语法是 target:normal-prerequisites|order-only-prerequisites

# make 支持 *通配符，但有类似$转义一样的注意事项，如遇到，请参考官方文档

# VPATH 指定make寻找的一系列路径来找到依赖,搜索的路径可以用冒号:隔开
# VPATH 只作用于依赖，不针对命令（recipe），命令中仍然需要写出完整的地址。

# $^ 表示当前的依赖项目
# $@ 表示目标
# $< 表示第一个依赖，在目录中使用这个宏会自动加上路径如：@g++ -o $(obj) -c $<
VPATH = src

# vpath 小写，同样也只针对依赖，不针对命令	语法：vpath %.c foo:bar 先在foo中查找后缀为.c的文件，然后在bar里面找

# 常见的用法
#vpath %.c src
#vpath %.h headers


obj = demo1.o

demo : $(obj)
	@g++ -o demo $(obj)

$(obj) : demo1.cpp
	@g++ -o $(obj) -c $<

# 伪目标仅仅是为了执行相关的命令，而没有实际的目标文件。
# .PHONY + name 表示伪目标，用于 name 和文件名重名的情况下。
.PHONY: clean
clean:
	@rm -f demo $(obj)

# 伪目标第二种用法用于递归调用发挥make的多线程：
# 	原始写法
#	SUBDIRS = foo bar baz
#	subdirs:
#	for dir in $(SUBDIRS);do \
#		$(MAKE) -C $$dir; \
#	done
#
#	使用伪目标，发挥多线程能力：
#	SUBDIRS = foo bar baz
#	.PHONY:subdirs $(SUBDIRS)
#	subdirs:$(SUBDIRS)
#	$(SUBDIRS):
#		$(MAKE) -C $@
#	foo:baz
#	暂时没有遇到这方面需求

# 伪目标第三种用法是同时构建多个目标,多个目标之间没有依赖关系。
# 这种方式较为常用 ： make all
#	all:prog1 prog2 prog3
#	.PHONY:all
#	prog1:prog1.o utils.o
#		g++ -o prog1 prog1.o utils.o
#	prog2:prog2.o
#		...
#	prog3:prog3.o sort.o utils.o
#		...


# 如果一条规则只有target，没有依赖也没有执行的命令。
# 那么make会认为依赖这个规则的规则总是需要被更新的。

# 多目标可以同时放在冒号前面。

# 可以分组目标，当一个目标更新，所有目标更新。&符号可以理解为all
# 分组目标的语法	add.o sub.o mul.o &: common.h
#                  		echo some_msg
#					add.o:common.h
# 需要注意的是，每个独立的目标不能有命令。如最下面的一条规则不能加recipe。


# 如果有相同的目标，不同的依赖。则make根据已有依赖来判断应该生成哪个目标

# 静态模式规则 Static Pattern Rules
# 例如	target1.o target2.o target3.o : %.o :%.cpp
# 其中 % 的作用类似与通配符。

# 相同目标单冒号的规则会被最后一条规则覆盖，有warning。
# 相同目标双冒号的规则则同时无条件执行，没有warning。

# -M 参数， 列出所有的依赖文件包含系统头文件 用法 g++ -M xxx
# -MM 参数，列出所有的依赖文件不包含系统头文件 用法 g++ -MM xxx


# make 默认使用 /bin/shell 执行命令。
# 空号如果有tab，不会认为是空行，会被认为是一个空命令。
# 命令行不要加注释，否则会被认为是shell的参数
# 参数定义不要在前面加tab，不然会被认为是命令的一部分。

# 在shell中执行make时加 -n 或者 --just-print,会打印要执行的命令，而不会真正执行
# 如 make print_var1 --just-print
# 在shell中执行make时加 -s 或者 --silent,会阻止打印。 .SILENT 也具有相同的作用。
# 在makefile 最前面使用宏 .SILENT 也能起到一样的效果。
# 	等价于使用@，不过是全局的。

# 每行命令前加 - ，不管此条命令是否执行成功都会继续执行，不中断make。

.PHONY : print_var1
print_var1 :
	-echo 1

# ONESHELL 宏的作用是把每条命令都在一个shell中执行，
# 默认每行命令在单独的一个shell执行。


# 并行执行 可以通过指定 -j 或者 --jobs 来并行执行。后面跟数字或者不跟，表示线程数。
# 如果其中任一规则失败，则会终止。不想终止可以添加参数 -k 或者 --keep-going

# define 语句的演示
# 注意等于号右侧的内容要写在下面一行，不能遗漏掉endef。

define print_to_terminal =
echo 2
endef

print_var2 :
	@$(print_to_terminal)


# 使用空的Recipes， 语法	target: ;
# 功能1 阻止目标使用隐式规则。 功能2 make会认为它是过期的。




# make中使用变量
# makefile中变量严格区分大小写
# .开头的变量一般有特殊含义。
# 自定义变量建议使用小写字母
# 为控制隐式规则的参数或用户应该使用命令选项重写的参数保留大写，如$(MAKE)
# $后不加小括号或者花括号，则只识别紧跟在后面的一个字符为变量。

# 递归展开变量直接使用等号=赋值。等号右侧也可以是一个变量名。
# 等号右侧的变量不一定要写在前面。
# 等号右侧变量的实际值是他最后一次定义的值。
# 递归展开的等号右侧不能出现他直接，因为上一条特性。

# 简单展开变量定义为 := 或者 ::=

# 应该避免注释写在变量定义的最后面。可能会多出空格（#前的）

# ?= 如果变量未定义，则定义。如 foo ?= bar

# 变量可以用 += 符号来追加内容

# 在定义变量最前面加关键字 override 表示重写该变量。


# 变量引用的一些情况
# foo := a.o b.o l.a c.o
# bar := $(foo:.o=.c)
# bar 等价于下面
# bar := $(foo:%.o=%.c)


# 使用define 和endef关键字。在之间可以定义多行命令，如
# define two-lines
# echo foo
# echo $(bar)
# endef
# 等价于  two-lines = echo foo;echo $(bar)

# undefine foo 用来使foo变量失效。

# CFLAGS 宏是一些和编译器相关的环境变量 
# 如使用下面代码
# CC=g++
# CFLAGS += -g
# export CFLAGS


# 针对目标的变量，如：
# prog:CFLAGS = -g
# prog:prog.o foo.o bar.o
# 表示只对prog生效

# 针对通配符变量的定义，如
# %.o : CFLAGS = -O
# %.o : %.c
#	$(CC) -c $(CFLAGS) $(CPPFLAGS) $< -o $@

# 条件判断
# CC = gcc
# foo: $(prerequisites)
# 	ifeq ($(CC),gcc)
#		echo gcc
# 	else
#		echo not gcc
#	endif
# 通常 ifeq （多个，n>=0）else endif 联合使用，不能忘记最后的endif。
# ifeq和后面的左括号之间有空格。



# 字符串函数

# $(subst from,to,text)#将from的字符串替换为to，如
# 	$(subst ee,EE,feet on the street)#ee替换为EE

# $(patsubst pattern,replacement,text)#将通配符pattern，替换为replacement
#	$(patsubst %.c,%.o,x.c.c bar.c)#将.c替换为.o->x.c.o bar.o

# $(strip string)#删除string前后空格，并将中间多个空格变为一个。

# $(findstring find,in)#在in中查找find，找到返回find，找不到返回空

# $(filter pattern...,text)#返回text中空格隔开，匹配pattern的字符串
# source := foo.c bar.c bza.s ugh.h
# $(filter %.c %.s,$(source))#变量只剩下 foo.c bar.c bza.s

# $(filter-out pattern...,text)#和上面的函数相反。

# $(sort list)#将list的字符串排序，并且去除重复的值。
# $(sort foo bar lose)#bar foo lose

# $(word n,text)#返回text中空格分开的，第n个字符串。n从1开始。
# $(word 2,foo bar lose)#foo

# $(wordlist s,e,text)#返回从s开始到e结束的字符串 s 和 e 表示第n个子串。
# $(wordlist 2,3,foo bar baz)#bar baz

# $(words text)#返回text中字符串的个数（不是字符char的个数）

# $(firstword names...)#返回以空格分开的names的第一个字符串

# $(lastword names...)#返回以空格分开的names的最后一个字符串




# 文件名相关的函数

# $(dir names)#提取names中每个文档名的目录部分。如果是当前目录会显示./
# $(dir src/foo.c hacks)#src/ ./

# $(notdir names)#提取names中每个文档名的非目录部分。
# $(notdir src/foo.c hacks)#foo.c hacks

# $(suffix names)#提取后缀
# $(suffix src/foo.c src-1.0/bar.c hacks)#.c .c（最后一个没有后缀为空）

# $(basename names)#提取除了后缀部分的名字，包含目录。
# $(basename src/foo.c src-1.0/bar.c hacks)#src/foo src-1.0/bar hacks

# $(addsuffix suffix,names...)#给names...添加后缀
# $(addsuffix .c,foo bar)#foo.c bar.c

# $(addprefix prefix,names...)#给names...添加前缀。
# $(addprefix src/,foo bar)#src/foo src/bar

# $(join list1,list2)#交叉匹配
# $(join a b,.c .o)#a.c b.o

# $(wildcard pattern)# 通配符匹配。

# $(realpath names...)#返回当前工作目录真实相对路径，如果不存在返回空。

# $(abspath names...)#返回当前工作目录真实绝对路径，不管是否路径真实存在



# 条件函数 
# 为空才是false，0是true

# $(if condition,then-part,else-part)#if...else
# $(if $(var),true,false)

# $(or condition1,condition2,condition3,...)#返回值是首个不为空的condition,不是其他语言中的返回真假。
# $(or $(var1),$(var2)...)

# $(and condition1,condition2,condition3)#若condition都不为空，就返回最后一个condition。否则返回第一个为0的condition
# $(and $(var1),$(var2),...)


# let 函数将list的值分别展开到var的列表中。
# 如果var列表值的数量多于list值的数量，则后面的都置为空。
# 如果var的数量少于list值的数量，则用list最后的值填充不够的var值。
# 展开完成后替换text中的变量。
# $(let var [var ...],[list],text)

# foreach 函数
# var 每次匹配一个list中空格隔开的子串。替换到text中。
# $(foreach var list,text)

# 文件函数
# file函数允许makefile 写入文档或者从文档读取。
# 两种方式 override 和 append 分布为覆盖和追加
# op 有三种 : > , >> ,< 分别是覆盖，追加和写入
# $(file op filenames,[text])

# call函数 （经常使用）
# $(call variables,param1,param2,...) # 用于自定义函数的调用
# reverse =$(2) $(1)
# foo = $(call reverse,a,b)
# .PHONY:print
#		@echo $(foo)#打印 b a

# value 函数 提供了一种使用变量的值而不让变量展开的方式，通常和eval函数配合使用
# F00 = $PATH
# all：
#	@echo $(FOO)#P未定义，所有打印ATH
#	@echo $(value FOO)#打印了完整的$PATH,在shell中显示了一些系统环境变量

# eval 函数
# 会对它的参数进行二次展开。第一次展开是函数本身完成的，
# 第二次是函数展开后的结果被作为Makefile内容时，由make解析时展开的。即make二次解析了第一次展开后的值
# eval函数没有返回值，或者返回值为空。
# $(eval ...)
# $(eval $(call,var,param...))

# info 函数 用于向终端打印输出，通常用于调试
# $(info text)

# origin 函数
# 返回变量值的来源，比如命令行或者环境变量等，返回值有：
# undefined   default   environment   environment override   file 
# command line   override   automatic
# $(origin variable)

# flavor 函数
# 返回变量的风格 :undefined   recursive   simple
# $(flavor variables)