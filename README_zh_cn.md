# libuv_cpp11
<br>对libuv的C++11风格的跨平台封装库，用于线上项目。跑过压测，很稳定，正确使用接口情况下，未发现carsh或内存泄漏。</br>

** **
* TCP相关类封装：`TcpServer`、`TcpClient`、`TcpConnection`、`TcpAccept`。及把C风格回调改为C++11风格的回调（支持非静态类成员函数及lambda）。
* `Timer`及`TimerWheel`：定时器及时间复杂度为O(1)的心跳超时踢出机制。
* `Async`：异步机制封装。相对于原生libuv async接口，优化了调用多次可能只运行一次的问题。由于libuv几乎所有api都非线程安全，建议使用writeInLoop接口代替直接write（writeInLoop会检查当前调用的线程，如果在loop线程中调用则直接write，否则把write加到loop线程中执行）。
* libuv信号封装。   
* `Packet`与`PacketBuffer`：包与缓存，发送/接受包，用于解决TCP残包/粘包问题，由ListBuffer和CycleBuffer两种实现，可通过宏配置（前者空间友好，后者时间友好）。
* Log日志输出接口，可绑定至自定义Log库。
** **
简单性能测试：单线程1k字节ping-pong。
<br>环境：Intel Core i5 6402 + ubuntu14.04.5 + gcc5.5.0 + libuv1.22.0 + O2优化</br>

   libuv_cpp | no use PacketBuffer|CycleBuffer|ListBuffer|
:---------:|:--------:|:--------:|:--------:|
次/秒     | 192857 |141487|12594|
** **
<br>**！对于诸如`uv::Timer`,`uv::TcpClient`等对象的释放需要调用close接口并在回调函数中释放对象,否则可能会出错。**</br>
<br>**！切勿在Loop线程外创建注册该Loop下的事件相关对象（`uv::TcpClient`，`uv::TcpServer`，`uv::Timer`……），建议每个Loop都绑定独立线程运行。**</br>
<br>一点微小的工作。</br>
