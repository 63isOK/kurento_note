# 说明
```c++
  Debug::DeathHandler dh;
  dh.set_thread_safe (true);
```

## db

这个db是一个封装类，用于处理：sigsegv/sigabrt,
主要是打印堆栈，其中也是利用sigaction函数来处理
