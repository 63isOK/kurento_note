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
























