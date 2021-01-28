## 一、GCC的使用

[原教程点这里](https://zhuanlan.zhihu.com/p/105969264)

### 1.编译流程

GCC 编译器在编译一个C语言程序时需要经过以下 4 步：

1. 将C语言源程序预处理，生成`.i`文件。

   预处理：代码在交给编译器之前，会先由预处理器进行一些文本替换方面的操作，例如宏展开、文件包含、删除部分代码等。

   ```shell
   gcc -E test.c -o test.i
   ```

    在正常的情况下，GCC 不会保留预处理阶段的输出文件，也即`.i`文件。然而，可以利用`-E`选项保留预处理器的输出文件，以用于诊断代码。`-E`选项指示 GCC 在预处理完毕之后即可停止。

   因为头文件可能相当大，如果源文件包括了多个头文件，那么它的预处理器输出可能会庞杂难读。使用`-C`选项会很有帮助，这个选项可以阻止预处理器删除源文件和头文件中的注释：

    ```shell
   gcc -E -C test.c -o test.i
    ```

   

2. 预处理后的.i文件编译成为汇编语言，生成`.s`文件。

   编译器的核心任务是把C程序翻译成机器的汇编语言（assembly language）。汇编语言是人类可以阅读的编程语言，也是相当接近实际机器码的语言。由此导致每种 CPU 架构都有不同的汇编语言。

   > 实际上，GCC 是一个适合多种 CPU 架构的编译器，不会把C程序语句直接翻译成目标机器的汇编语言，而是在输入语言和输出汇编语言之间，利用一个中间语言，称为 RegisterTransfer Language（简称 RTL，寄存器传输语言）。借助于这个抽象层，在任何背景下，编译器可以选择最经济的方式对给定的操作编码。
   >
   > 而且，在交互文件中针对目标机器的抽象描述，为编译器重新定向到新架构提供了一个结构化的方式。但是，从 GCC 用户角度来看，我们可以忽略这个中间步骤。

   通常情况下，GCC 把汇编语言输出存储到临时文件中，并且在汇编器执行完后立刻删除它们。但是可以使用`-S`选项，让编译程序在生成汇编语言输出之后立刻停止。

   如果没有指定输出文件名，那么采用`-S`选项的 GCC 编译过程会为每个被编译的输入文件生成以`.s`作为后缀的汇编语言文件。

   ```shell
   gcc -S test.i -o test.s
   ```

   如果想把C语言变量的名称作为汇编语言语句中的注释，可以加上`-fverbose-asm`选项：

   ```shell
   gcc -S -fverbose-asm test.i -o test.s
   ```

   

3. 将汇编语言文件经过汇编，生成目标文件`.o`文件(机器语言)。

   ```shell
   gcc -c test.o -o test.o
   ```

   `-c`选项表示编译、汇编指定的源文件（也就是编译源文件），但是不进行链接。使用`-c`选项可以将每一个源文件编译成对应的目标文件。

4. 将各个模块的`.o`文件链接起来生成一个可执行程序文件。

   ```shell
   gcc main.o test.o -o main.out
   ```

   

![image-20201220081911302](http://qkwfs9p87.hn-bkt.clouddn.com/image-20201220081911302.png)



`.i`文件、`.s`文件、`.o`文件可以认为是中间文件或临时文件，如果使用 GCC 一次性完成C语言程序的编译，那么只能看到最终的可执行文件，这些中间文件都是看不到的，因为 GCC 已经经它们删除了。

### 二、Makefile

[原教程点这里](https://seisman.github.io/how-to-write-makefile/introduction.html)

#### 简介：

> 在软件开发中，make通常被视为一种软件构建工具。该工具主要经由读取一种为“makefile”或“Makefile”的文件来实现软件的自动化建构。它会通过一种被称之为“target”概念来检查相关文件之间的依赖关系，这种依赖关系的检查系统非常简单，主要通过对比文件的修改时间来实现。在大多数情况下，我们主要用它来编译源代码，生成结果代码，然后把结果代码连接起来生成可执行文件或者库文件。

**只要我们的makefile写得够好，所有的这一切，我们只用一个make命令就可以完成，make命令会自动智能地根据当前的文件修改的情况来确定哪些文件需要重编译，从而自动编译所需要的文件和链接目标程序。**

#### Makefile的规则

在讲述这个makefile之前，还是让我们先来粗略地看一看makefile的规则。

```
target ... : prerequisites ...
    command
    ...
    ...
```

- target

  可以是一个object file（目标文件），也可以是一个执行文件，还可以是一个标签（label）。对于标签这种特性，在后续的“伪目标”章节中会有叙述。

- prerequisites

  生成该target所依赖的文件和/或target

- command

  该target要执行的命令（任意的shell命令）

这是一个文件的依赖关系，也就是说，target这一个或多个的目标文件依赖于prerequisites中的文件，其生成规则定义在command中。说白一点就是说:

```
prerequisites中如果有一个以上的文件比target文件要新的话，command所定义的命令就会被执行。
```

#### 实际的例子

**1.初始版本**

如果一个工程有3个头文件和8个c文件，编译要求如下

1. 如果这个工程没有编译过，那么我们的所有c文件都要编译并被链接。
2. 如果这个工程的某几个c文件被修改，那么我们只编译被修改的c文件，并链接目标程序。
3. 如果这个工程的头文件被改变了，那么我们需要编译引用了这几个头文件的c文件，并链接目标程序。

对应的MakeFile文件如下

```shell
edit : main.o kbd.o command.o display.o \
        insert.o search.o files.o utils.o
    cc -o edit main.o kbd.o command.o display.o \
        insert.o search.o files.o utils.o

main.o : main.c defs.h
    cc -c main.c
kbd.o : kbd.c defs.h command.h
    cc -c kbd.c
command.o : command.c defs.h command.h
    cc -c command.c
display.o : display.c defs.h buffer.h
    cc -c display.c
insert.o : insert.c defs.h buffer.h
    cc -c insert.c
search.o : search.c defs.h buffer.h
    cc -c search.c
files.o : files.c defs.h buffer.h command.h
    cc -c files.c
utils.o : utils.c defs.h
    cc -c utils.c
clean :
    rm edit main.o kbd.o command.o display.o \
        insert.o search.o files.o utils.o
```

反斜杠（ `\` ）是换行符的意思。这样比较便于makefile的阅读。我们可以把这个内容保存在名字为“makefile”或“Makefile”的文件中，然后在该目录下直接输入命令 `make` 就可以生成执行文件edit。如果要删除执行文件和所有的中间目标文件，那么，只要简单地执行一下 `make clean` 就可以了。

在这个makefile中，目标文件（target）包含：执行文件edit和中间目标文件（ `*.o` ），依赖文件（prerequisites）就是冒号后面的那些 `.c` 文件和 `.h` 文件。每一个 `.o` 文件都有一组依赖文件，而这些 `.o` 文件又是执行文件 `edit` 的依赖文件。依赖关系的实质就是说明了目标文件是由哪些文件生成的，换言之，目标文件是哪些文件更新的。

在定义好依赖关系后，后续的那一行定义了如何生成目标文件的操作系统命令，一定要以一个 `Tab` 键作为开头。记住，make并不管命令是怎么工作的，他只管执行所定义的命令。make会比较targets文件和prerequisites文件的修改日期，如果prerequisites文件的日期要比targets文件的日期要新，或者target不存在的话，那么，make就会执行后续定义的命令。

这里要说明一点的是， `clean` 不是一个文件，它只不过是一个动作名字，有点像c语言中的label一样，其冒号后什么也没有，那么，make就不会自动去找它的依赖性，也就不会自动执行其后所定义的命令。要执行其后的命令，就要在make命令后明显得指出这个label的名字。这样的方法非常有用，我们可以在一个makefile中定义不用的编译或是和编译无关的命令，比如程序的打包，程序的备份，等等。

##### 2.引入变量简化

于是如果有新的 `.o` 文件加入，我们只需简单地修改一下 `objects` 变量就可以了。

```shell
objects = main.o kbd.o command.o display.o \
    insert.o search.o files.o utils.o

edit : $(objects)
    cc -o edit $(objects)
main.o : main.c defs.h
    cc -c main.c
kbd.o : kbd.c defs.h command.h
    cc -c kbd.c
command.o : command.c defs.h command.h
    cc -c command.c
display.o : display.c defs.h buffer.h
    cc -c display.c
insert.o : insert.c defs.h buffer.h
    cc -c insert.c
search.o : search.c defs.h buffer.h
    cc -c search.c
files.o : files.c defs.h buffer.h command.h
    cc -c files.c
utils.o : utils.c defs.h
    cc -c utils.c
clean :
    rm edit $(objects)
```

##### 3.引入自动推导

GNU的make很强大，它可以自动推导文件以及文件依赖关系后面的命令，于是我们就没必要去在每一个 `.o` 文件后都写上类似的命令，因为，我们的make会自动识别，并自己推导命令。

只要make看到一个 `.o` 文件，它就会自动的把 `.c` 文件加在依赖关系中，如果make找到一个 `whatever.o` ，那么 `whatever.c` 就会是 `whatever.o` 的依赖文件。并且 `cc -c whatever.c` 也会被推导出来，于是，我们的makefile再也不用写得这么复杂。我们的新makefile又出炉了。

```shell
objects = main.o kbd.o command.o display.o \
    insert.o search.o files.o utils.o

edit : $(objects)
    cc -o edit $(objects)

main.o : defs.h
kbd.o : defs.h command.h
command.o : defs.h command.h
display.o : defs.h buffer.h
insert.o : defs.h buffer.h
search.o : defs.h buffer.h
files.o : defs.h buffer.h command.h
utils.o : defs.h

.PHONY : clean
clean :
    rm edit $(objects)
```

这种方法，也就是make的“隐晦规则”。上面文件内容中， `.PHONY` 表示 `clean` 是个伪目标文件。

## 三、gdb调试

> GDB是一个由GNU开源组织发布的、UNIX/LINUX操作系统下的、基于命令行的、功能强大的程序调试工具，对于一名Linux下工作的c++程序员，gdb是必不可少的工具。

```shell
gcc test.c -g -o test.out		#编译时加上-g才能gdb调试
gdb test.out	#进入gdb调试
b main	     #break，设置断点
b n			 #在第n行设置断点
r			 #运行程序到断点处	run
s	         #下一步 有函数则进入函数
n			 #下一步 不进入函数
p xxx		 #打印信息
display a b  #监控a和b的值
p (*(p+4))@6 #p指向的第4个地址中6个字节的数据
bt			 #打印当前堆栈中的所有信息（函数调用时所有数据会压入栈中）
f 1          #切换到堆栈1
q            #退出调试
l            #显示源码
             #回车 重复上一条命令
```

