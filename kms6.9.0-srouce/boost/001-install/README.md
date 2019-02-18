# ubuntu 编译安装boost 1.69.0

```
  docker cp boost_1_69_0.tar.bz2 boost:/home/yb/  
  docker exec -u=yb -w=/home/yb -t -i boost /usr/bin/zsh

  // build
  ./bootstrap.sh
  ./b2

  头文件在当前目录，库文件在stage/lib
```


