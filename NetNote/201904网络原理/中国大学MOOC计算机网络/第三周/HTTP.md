# HTTP连接的两种类型


## 非持久性连接(Nonpersistent HTTP)

- 每个TCP连接最多允许传输`一个`对象
- HTTP 1.0 版本使用非持久性连接



## 持久性连接(Persistent HTTP)

- 每个TCP连接允许传输`多个`对象
- HTTP 1.1 版本默认使用持久性连接

###  无流水(pipelining)的持久性连接

- 客户端只有收到前一个响应后才发送新的请求
- 每个被引用的对象耗时1个RTT


### 带有流水机制的持久性连接

- HTTP 1.1的默认选项
- 客户端只要遇到一个引用对象就尽快发出请求
- 理想情况下，收到所有的引用对象只需耗时约1个RTT


