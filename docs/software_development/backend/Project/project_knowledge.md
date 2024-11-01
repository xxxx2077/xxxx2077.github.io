# 工程问题

## 假设两台服务器，你突然发现服务器A看不到服务器B的网页，你会如何处理

[https://xiaolincoding.com/interview/network.html#网页非常慢转圈圈的时候-要定位问题需要从哪些角度](https://xiaolincoding.com/interview/network.html#网页非常慢转圈圈的时候-要定位问题需要从哪些角度)

我们按照计网四层模型进行思考

- IP层面：测试网络是否连通。ping一下服务器B的ip地址

- TCP层面：如果能ping通，说明服务器网络连接正常。接下来测试端口，使用telnet + ip地址 + 端口，如果连不上，可能是端口的问题

  - 查看服务器是否有防火墙拦截，如果防火墙拦截，说明需要放行端口

- HTTP层面：使用wireshark或tcpdump抓包分析，主要看的是序号

  > 参考https://blog.csdn.net/qq_38658567/article/details/108786826

## 慢查询定位和优化

https://mp.weixin.qq.com/s/sL64uQP0iHKxkMFx1QGLkg

1. 开启慢查询日志
2. 慢查询日志记录查询时间超过约定时间的sql语句
3. 在慢查询日志sql语句前加上explain查看执行计划
4. 定位sql性能的主要指标：type，possible_keys, key, key_len,extra

## 高并发

### 假设两个线程想要读取同一条记录，如果读取不到就更新记录，如果读取到就不更新，你该如何处理这种情况下的并发

通常我们第一反应是加锁，例如使用select ... for update可以给查询的数据行加上next-key lock进行锁定，

乐观锁：假设最好的情况，认为共享资源被访问时不会出现问题，线程可以不停地执行，无需加锁，也无需等待，只是在提交修改的时候去验证对应的资源是否被其他线程修改了。

- 版本号机制
- CAS算法

悲观锁：认为共享资源每次被访问时都会出现问题，所以每次访问资源都会上锁。即：**共享资源每次只给一个线程使用，其他线程阻塞，用完再把资源转让给其他线程**

高并发的场景下，悲观锁会造成线程阻塞，大量阻塞线程会导致系统的上下文切换，而且还可能存在死锁问题

乐观锁不存在锁竞争造成线程阻塞，不会有死锁问题，性能更好。但是如果写次数较多，冲突频繁发生，同样会影响性能，导致CPU飙升

悲观锁用于写比较多的情况

乐观锁用于写比较少的情况
