# api
    通过api可以操控kms的行为

## 媒体元素
* WebRtcEndpoint
    接受发送webrtc流
* RtpEndpoint
    收发rtp流，支持rtp协议，用sdp协议协商
* HttpPostEndpoint
    利用http post请求接收媒体，像是http的文件上传
* PlayerEndpoint
    接收端，数据源可选择多种：本地文件、http url、rtsp
* RecorderEndpoint
    将数据保存起来

除了这些endpoint，还有一些处理媒体的媒体元素，称为filter
* ZBarFilter
    检测视频流中的QR，并用事件通知给client
* FaceOverlayFilter
    人脸检测并覆盖一张图
* GStreamerFilter
    通过这个可以使用gst的element
    目前只能有一个gst插件可以被加到kurento中 - 未来会做扩展
    如果要使用gst插件，就用多个GStreamerFilter

除了这些endpoint和filter，还要一些用于管理媒体流的元素，称为hub
* Composite
    混流 - 会将音视频按固定规则混合 - 只是很耗cpu
* DispatcherOneToMany
    将收到的流推给其他连接到hub上的元素
* Dispatcher
    将收到的流推给所有连接到hub上的元素 - 和上面的区别是：自己也会收到

# client
    kms现在提供了两种语言的client api：js java
    未来会提供python、c、c++、php语言的client api

# kurento protocol
基于json-rpc2.0和websocket
    除了正常的一些接口，还提供了一个connect用于断线重连

    下面是错误返回格式
```json
{
  "jsonrpc": "2.0",
  "id": 1, // id有可能是null
  "error": {
    "code": "33",
    "message": "Invalid parameter format",
    "data": "" // 这是附加信息
  }
}
```

# 自定义api
    kms提供了idl，json格式的，用其他语言实现api可以参考这些idl
    kms提供了一个kurento-module-creator，用于生成其他语言的api
    代码的生成使用freemarker工具作为模板引擎
    kurento-module-creator -c <CODEGEN_DIR> -r <ROM_FILE> -r <TEMPLATES_DIR>
    CODEGEN_DIR：输出目录
    ROM_FILE：kmd文件，或kmd文件的目录
    TEMPLATES_DIR：模板文件目录
    具体可参考 https://doc-kurento.readthedocs.io/en/6.8.1/features/kurento_protocol.html

# kurento utils js
    js的工具包，封装了RTCPeerConnection对象
## data channels
    通过webrtc的data channel可以发送二进制和文本
    工具包可以捕获到流断开消息
    工具包提供了很多强大的功能：
        getPeerConnection
        showLocalVideo
        getLocalStream
        getRemoteStream
        getCurrentFrame
        processAnswer
        processOffer
        dispose
        addIceCandidate
        getLocalSessionDescriptor
        getRemoteSessionDescriptor
        generateOffer
    屏幕共享

# 安全
    chrome47开始，webrtc支持https和localhost
    一个是app server的配置
    一个是kms的配置

