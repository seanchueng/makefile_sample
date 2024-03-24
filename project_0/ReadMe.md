# How to run make

### make 退出状态有三种
- 0 make成功执行完成
- 2 有错误
- 1 如果使用了 `-q`参数和 make 确定某些目标尚未更新。



---
### make 有三种默认的名字

如果想执行其他名字的文件，需要指定`-f`或者`--file`选项



---
### make 常见的目标

all 编译所有目标

distclean/realclean目标 进行 clean 的话，它的目标比 clean 本身来得大。所以它一般是依赖 clean本身，然后再执行一些自己独有的操作。 

install 通常把可执行文件放在一些特点目录下供用户执行，如`usr/bin`

### make 后面的可选项

#### 并非真正执行命令recipe，只是打印该命令

```makefile
    -n
    --just-print
    --dry-run
    --recon
```

#### 更新时间，并非真正执行

即 make 假装更新了文件

```makefile
    -t
    --touch
```
#### 查询文件是否是最新的

```makefile
    -q
    --question
```
#### 测试文件的编译

make 出现错误时继续执行下去，来测试 makefile ，找出错误。
```makefile
    -k
```
#### 其他一些选项

```makefile
#   总是重新编译，不管是不是最新的。
    -B
    --always-make
#   指定头文件的目录
    -I dir
    --include-dir = dir
#   查看make中所有的默认规则和内置变量
    -p
    ...
```



---

### 隐式规则使用的变量

- AR    默认ar
- AS    默认as
- CC    默认cc
- CXX   默认g++
- CPP   默认$(CC) -E

### 自动变量

$@ 目标名称
$% 如果目标不是归档文件，则为空；如果目标是归档文件成员，则为对应的成员文件名
$< 第一个依赖性
$? 依赖中修改时间晚于目标文件修改时间的所有文件名，以空格隔开
$^ 所有依赖文件名，文件名不会重复，不包含order-only依赖
$+ 表示所有依赖文件名，包括重复的文件名，不包含order-only依赖
$| 所有order-only依赖文件名