# 信号
    linux系统中的信号

```c++
int
main (int argc, char **argv)
{
  struct sigaction signalAction {};

  /* Install our signal handler */
  signalAction.sa_handler = signal_handler;

  // ...

  sigaction(SIGINT, &signalAction, nullptr);
  sigaction(SIGTERM, &signalAction, nullptr);
  sigaction(SIGPIPE, &signalAction, nullptr);

  // ...

  return 0;
}

static void
signal_handler (int signo)
{
  static unsigned int __terminated = 0;

  switch (signo) {
  case SIGINT:
  case SIGTERM:
    if (__terminated == 0) {
      GST_DEBUG ("Terminating.");
      loop->quit ();
    }

    __terminated = 1;
    break;

  case SIGPIPE:
    GST_DEBUG ("Ignore sigpipe signal");
    break;

  default:
    break;
  }
}
```

## 信号的介绍
    linux中的信号，是`软中断`

*apue 3rd p312*  
大多数应用程序都会使用信号

信号可用于处理异步事件，eg：
在终端按下中断键去结束一个程序

信号的历史很长，归功于posix，现在使用的信号都是可靠的，兼容的

## 信号的概念
信号都有名字，SIG开头，eg：  
SIGABRT，程序调用abort(),会产生abort中断信号；
SIGALRM，alarm()设置的定时器到点之后，会产生alarm报警信号

除了系统支持的信号，应用程序也可以自己定义信号，<signal.h>

signal中没有0，只有kill()会使用到，posix称这个0为null signal，
下列情况会产生一个signal：
- 终端生成的signal，一般是用户指定键生成(ctrl-c)
- 硬件异常生成signal，eg：除零操作，无效内存操作,
一般都是硬件捕获，告知给kernel，kernel会产生响应的signal
- 通过kill()可以向任意进程/进程组发送任意signal，
前提是成为这些进程/进程组的owner或是超级用户
- 可以通过kill命令向其他进程发送signal，这个kill命令是kill()的一个封装，
只做kill操作，通常用于结束正在运行的后台程序
- 进程需要监听一些事件时，也可产生中断，软中断

信号典型的就是异步事件，信号的出现是随机的，
要看信号是否出现，轮训的方式是不可取的，一般都是通过kernel进行监听

kernel遇到信号，有3种处理方式：
- 忽略，kernel遇到大部分信号都执行这个操作，SIGKILL和SIGSTOP两个
永远不会被忽略。部分信号如果被忽略了，会导致程序行为未知，eg：内存越界
- 捕获，程序需要告诉kernel处理函数。SIGKILL和SIGSTOP不会被捕获
- 默认操作，每个signal都有一个默认操作

有些信号的默认处理就是结束+产生dump文件

dump文件在以下情况下并不会产生：
- 程序的owner并不是当前用户
- 当前用户并不在程序的用户组中
- 当前用户并没有权限在程序运行目录进行写操作
- core已经存在，且当前用户并没有core文件的写权限
- core文件太大，超出限制

具体的signal信息，可以查看*apue 3rd p317*  

## siganl()
```c++
    #include <signal.h>
    void (*signal(int signo, void (*fun)(int))(int);
```
    先来理解一下这个写法：
    void func(int);  // 函数
    void (*p)(int);  // 函数指针
    void (*a(int))(int); // 
























