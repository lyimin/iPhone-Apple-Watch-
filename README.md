
## iPhone 与 Watch 通讯

### 简介
iOS和WatchOS都要创建会话对象**WCSession**。当iOS和WatchOS都是在激活状态时，手机应用和手表应用就能马上进入通讯。

### 配置和激活会话
让默认的会话对象设置**delegate**和调用对象 **activateSession**
方法，iOS和WatchOS一定要设置其各自的会话对象，活跃的状态才能给手表应用和手机应用建立连接。

```Objective-c
if ([WCSession isSupported])
{
   WCSession* session = [WCSession defaultSession];  
   session.delegate = self;
   [session activateSession];
}
```
iOS和WatchOS必须要实现会话代理方法，请实现 ```session:activationDidCompleteWithState:error:```方法，
让会话知道app支持多个手表通讯。在手机应用中，实现 ```sessionDidBecomeInactive:```和```sessionDidDeactivate:```
会话代理方法，去管理不同的手表应用状态。

![](/Users/yiming/Desktop/1.jpg)


![](/Users/yiming/Desktop/2.jpg)

当iPhone换一个新的Apple Watch配对且自动配对成功时，一个iPhone只能和一个Apple Watch进行通讯，Apple Watch会话是处于活跃状态，但是iPhone将从不太活跃转换成不活跃状态，在这两个状态的短暂时间里，可以传递已经收到的数据，当数据被传递，会话状态是不活跃的，在这个时间时，iPhone一定要再次调用**activateSession**
一次去和新的Apple Watch进行连接

![](/Users/yiming/Desktop/3.png)

### 与相应的应用通讯
当Apple Watch和iPhone的**WCSession**的**activationState**属性被置成
**WCSessionActivationStateActivated**时，两个应用可以进行相互通讯。
在传数据前，iPhone和Apple Watch要确保app被安装上。

- ```sendMessage:replyHandler:errorHandler:```
- ```sendMessageData:replyHandler:errorHandler```
- ```updateApplicationContext:error:```
- ```transferUserInfo:```
- ```transferFile:metadata:```

当发送信息给对应的应用，后台的信息是通过队列顺序传送的。接收到的信息也是在会话代理里收到。发送数据用```sendMessage:replyHandler:errorHandler:```
, ```sendMessageData:replyHandler:errorHandler:```
和```transferCurrentComplicationUserInfo:```方法，这些方法有高的优先级和马上传送数据，所有的信息都是异步线程按照顺序接收信息的。

### 注意
- 后台传送数据不是马上的，系统是尽快传送数据的，当电量不足时，可以延迟传送数据。因此，当发送大量的数据需要花大量时间，接受端也要花相同的时间去接收数据。

- 当发送应用需要的数据。传送是数据是无线的需要消耗电的，数据改变时才发送数据，而不是每次都要发送。

- 如果设备内存不足时，会导致传送数据失败，如果数据的全面或者通讯出现错误。检查错误并提供合理的解决办法。

![](/Users/yiming/Desktop/4.jpg)
