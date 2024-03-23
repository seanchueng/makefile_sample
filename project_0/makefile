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
# $^ 表示当前的依赖项目
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

VPATH = src


obj = demo1.o

demo : $(obj)
	@mkdir release
	@g++ -o /release/demo $(obj)

$(obj) : demo1.cpp
	@g++ -o $(obj) -c src/demo1.cpp

# .PHONY + name 表示伪目标，用于 name 和文件名重名的情况下。
.PHONY: clean
clean:
	@rm -rm ./release
	@rm -f demo $(obj)