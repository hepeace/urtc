# 通话前检测网络质量

通话前，检测网络质量，可以判断或者预知当前的网络好坏。

在网络质量要求比较高的场景中，建议在加入房间之前，先检测通话质量。

## 实现方法

在正式加入房间前，可以在本地创建两个 Client，然后创建两路流，进入一个测试用的房间。    
其中一路流用来测上行网络的连接状况，另一路测下行网络的连接状况。    

 - 1、调用 client.publish 方法发布一路流后，你可以调用 getNetworkStats 方法获取上行网络连接数据 NetworkStats。可以通过 NetworkStats 值大致判断上行网络的质量：
     - [0,100) 上行网络质量好
     - [100,200) 上行网络质量较差
     - ≧ 200 上行网络质量很差

 - 2、调用 client.subscribe 成功订阅第二路流后，你也可以调用 getNetworkStats 方法获取下行网络连接数据 NetworkStats。可以通过 NetworkStats 的值大致判断下行网络的质量：
     - [0,200) 下行网络质量好
     - [200,400) 下行网络质量较差
     - ≧ 400 下行网络质量很差
