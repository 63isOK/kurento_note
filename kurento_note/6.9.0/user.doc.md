# kurento和webrtc介绍

## kurento提供的特征
* webrtc媒体管道的动态配置
* c/s架构，kms提供能力，客户端负责拓扑构建
* 支持java和js客户端
* 第三方支持，插件模式，方便集成

## openVidu
    openVidu是kurento团队开源的另一个项目
    基于kurento项目

## kurento的安装
* 部署在amazon云
* docker方式部署 - 需要自己测试
* 本地安装

    部署要求： ubuntu14或16，x64
    现在kms的开发是在ubuntu16，所以推荐使用ubuntu16

### kurento的本地安装
* 安装kurento
    DISTRO="xenial"
    sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 5AFA7A83
    sudo tee "/etc/apt/sources.list.d/kurento.list" >/dev/null <<EOF
    deb [arch=amd64] http://ubuntu.openvidu.io/6.9.0 $DISTRO kms6
    EOF
    sudo apt-get update
    sudo apt-get install kurento-media-server
* 安装coturn
    sudo apt install -y coturn
    sudo apt install -y sqlite3
* 配置corutn
    配置relay-ip和external-ip
    启用 fingerprint lt-cred-mech realm=kurento.org
    给coturn添加一个用户 sudo turnadmin -k -u coturn -p 1 -r kurento.org
      得到一个md5： 0x716ac31761883de947fbf4a2353c3f6b
    在配置文件中添加 user=coturn:0x716ac31761883de947fbf4a2353c3f6b
    启动no-stdout-log
    配置/etc/default/coturn 让coturn开机启动
* 配合kurento，让kurento知道使用哪个turn服务
    /etc/kurento/modules/kurento/WebRtcEndpoint.conf.ini
    turnURL=coturn:0x716ac31761883de947fbf4a2353c3f6b@172.168.10.112:3478
* 检查防火墙端口
    sudo ufw status 如果开了防火墙 就需要打开相关的端口：
    3478 tcp/udp
    49152-65535 udp

# demo
## java 环境配置
    安装jdk和mvn 
        sudo apt-get install maven
        sudo apt-get install openjdk-8-jdk openjdk-8-jre
    配置环境变量
        export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
        export JRE_HOME=${JAVA_HOME}/jre
        export CLASSPATH=.:$CLASSPATH:$JAVA_HOME/lib:$JRE_HOME/lib  
        export PATH=${PATH}:${JAVA_HOME}/bin:$JRE_HOME/bin
        将这些丢到/etc/profile中  再在.zshrc中加入 source /etc/profile
        或是直接在.zshrc中加入上面几条
        如果是其他sh,eg:bash, 就在.bashrc中处理  或者每开一次终端手动输入source 命令

## hello world
### java版本
    git clone https://github.com/Kurento/kurento-tutorial-java.git
    cd kurento-tutorial-java/kurento-hello-world
    git checkout 6.9.0
    mvn -U clean spring-boot:run -Dkms.url=ws://localhost:8888/kurento

## 特征
    kms通过rpc api暴露功能，这些api称为kurento api
    目前提供了js nodejs java版本的api，其他语言可以通过kurento protocol调用api

    如果配置了turn就不需要配置stun，因为turn是stun的超集
    官方推荐一个会议一个管道

# 调试
## kms
    kms崩溃之后，需要安装调试版本的kms，这样才有符号信息
    kms隔一段事件就断开连接：
      检查stun/turn服务, 最好用coturn
      用webrtc提供的测试网页去测试coturn是否配置ok
      在kurento检查stun/turn的配置
      检查kurento配置stun/trun的语法是否正确
      检查coturn的日志
      检查kms的调试日志
      kms的服务init是否有正确启动，用命令server查看，或检查/var/log/syslog
    cpu使用增长太快
      当前版本的kurento对720p及以上的视频，性能不是很理想，不推荐使用1080p
      源和目的的编码格式不一致，也是webrtc性能问题的根源
          也包括录制转码
      是否有其他占cpu的进程，中继太多，会导致coturn资源消耗增加
      总之：
          转码是cpu消耗大户(因为编码的计算太占资源)
          分辨率也是其中一个考虑点
    openh264找不到
      安装正确的package或是配置号链接
    丢失音频流或视频流
      一般是因为coturn没有配置正确引起的
## app server
    kms未启动
    kms无响应 - 重启kms即可
    sdp解析错误 - 是因为browser不支持h264 eg：safari
        OperationError (DOM Exception 34): Expects at least 4 fields
        因为kms不支持h264，而浏览器客户端只支持h264
    指定媒体元素的信息
        player endpoint的rtsp信息
        record endpoint的录制文件大小为0kb
## browser
    safari不工作 - 因为safari的约束比chrome/firefox严格很多

