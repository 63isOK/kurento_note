# 写kurento模块
    通过编写module来扩展kms,有两种常用方式:
    基于opencv的module,开发一个filter用于支持视频处理
      eg: ar 视觉增强
    基于gstreamer的module,基于gstreamer框架的媒体处理

    要开发filter,就需要先确定filter的结构
      可以使用kurento-module-scaffold工具
      这个工具在kurento-media-server-dev开发包中
      sudo apt-get install kurento-media-server-dev

    等确定结构之后,需要把idl文件生成对应的代码


