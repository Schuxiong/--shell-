# 分工

- 单：
  
- 林：解析命令的函数，执行命令的函数，实现重定向，管道，后台执行功能
  - 软件系统设计部分：shell执行命令，管道，重定向，后台执行的原理
  - 算法设计部分：4个函数的流程图和解释
  - 遇到的问题

- 万：



还剩的：

- 遇到的问题？解决？
  - 1.如何将用户输入字符读入 解析并执行对应的命令（林）
  - 2.如何判断用户是否使用了IO重定向以及使用了何种类型的IO重定向
- 还存在什么问题：错误处理，安全性，命令补全，性能，界面美化，命令不够全
- 每人心得体会

- 其它需要补充的问题
- 参考资料





Part1:

1. - 

# 执行指令

执行指令：fork() + exec()



# 管道

fork两次，exec两次+两次dup2()更改方向

管道⽤于⽗⼦进程的通信，在 fork 之前创建 pipe，pipe将成为 fork 之后⽗⼦进程之间的纽带。pipe 函数会返回两个描述符（pipe_in, pipe_out），⼀个⽤于从管道读，⼀个⽤于从管道写。  (此时cmd1和cmd2分别有4个fd，标准输入输出+读 写)

- dup2(fd1, fd2) 函数：它的作⽤是**将 fd1 关联到 fd2 所指向的内核对象（二者关联同一个）**，fd1原先指向的内核对象的引⽤计数减⼀，如果减到零就销毁。  

==dup2(stdout,pipe_out)   +    dup2(stdin,pipe_in)==

![image-20240617195255523](C:\Users\lyt12\AppData\Roaming\Typora\typora-user-images\image-20240617195255523.png)







# 重定向

`>`：对输出重定向。左边的fd（没有就默认1 标准输出）；右边的文件存在会先删除重建。`>>`直接追加，不会删除存在的。

`<`：对输入做重定向，从右边的文件获取内容（没有就默认0 标准输入）。







# 后台运行

- 为了屏蔽键盘控制台，子进程的**stdin stdout重定向到/dev/null**，调用**signal(SIGCHLD,SIG_IGN)让Linux接管这个进程**。shell就能避免调用wait/waitpid，直接去执行下一条命令
- dev/null（Linux的垃圾桶）
- 父进程不用wait阻塞等待子进程结束，直接返回







# 代码思路

## 全局思路

![image-20240617201439712](C:\Users\lyt12\AppData\Roaming\Typora\typora-user-images\image-20240617201439712.png)

全局一个while循环，接受用户输入的命令

- cmdline：从终端读入的字符串
- read_command()函数：读取多种命令函数，处理";"
- 处理后得到commads[]数组：存放每一条命令
- parse_command()函数：处理一条命令
- command[]数组：一条命令的字符串数组
- analazy_command()函数：处理一条命令中的多个参数，是否有管道，输入输出重定向
- argv[] []数组：解析一条命令command的参数

- execute_command()函数：处理多管道，多线程，后台执行等

- argv[tt]：标识当前遍历到第tt个命令

- do_command()函数：执行命令，包括内建命令+外部命令





内部命令：存放在BUILTIN_CMD结构体数组builtins[]

cd history echo export exit









## main函数

打印欢迎信息，显示当前目录和用户提示符

在while(1)循环中：

1.init

2.read_command

3.parse_command







## init

初始化函数









## 解析命令的函数

有3中解析命令的函数：

![image-20240617203352907](C:\Users\lyt12\AppData\Roaming\Typora\typora-user-images\image-20240617203352907.png)

![image-20240617203419925](C:\Users\lyt12\AppData\Roaming\Typora\typora-user-images\image-20240617203419925.png)

### read_command

读取多种命令函数，处理";"











### parse_command

处理一条命令





### analazy_command

处理一条命令多个参数，是否有管道，输入输出重定向













## execute_command

多管道，多线程，后台执行



## do_command

执行命令，内建+外部





# 命令的实现

## history

有一个history vector记录所有的历史命令







## 后台执行

一个bool变量back_flag，是否后台执行





