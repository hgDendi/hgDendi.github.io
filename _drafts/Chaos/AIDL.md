# AIDL #
# Android Interface Definitioin Language #
>Android接口定义语言。在Android中，AIDL是跨进程通信的主要实现方式。

- 服务端
	- 创建Service服务监听客户端的请求，实现AIDL接口
- 客户端
	- 绑定服务端，调用AIDL的方法
- AIDL接口
	- 跨进程通信的接口，AIDL的包名需要与项目的包名相同，默认生成即可
	- 支持：基本类型，字符串，List，Map，Parcelable，AIDL接口

流程：
- 客户端注册服务端
- 服务端添加新产品
- 客户端接收，并提供客户端的查询产品数量的接口

http://gold.xitu.io/entry/575d036edf0eea006484dea1