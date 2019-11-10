因为微服务间的调用，API 网关的请求转发等内容实际上都是通过Ribbon 来实现的，包括后续我们将要介绍的 Feign, 它也是基于 Ribbon 实现的工具。

因为负载均衡是对系统的高可用、 网络压力的缓解和处理能力扩容的重要手段之一。 我们通常所说的负载均衡都指的是服务端负载均衡， 其中分为硬件负载均衡和软件负载均衡。 硬件负载均衡主要通过在服务器节点之间安装专门用于负载均衡的设备，比如 F5 等；而软件负载均衡则是通过在服务器上安装一些具有均衡负载功能或模块的软件来完成请求分发工作， 比如Nginx 等。

硬件负载均衡的设备或是软件负载均衡的软件模块都会维护一个下挂可用的服务端清单，通过心跳检测来剔除故障的服务端节点以保证清单中都是可以正常访问的服务端节点。当客户端发送请求到负载均衡设备的时候， 该设备按某种算法（比如线性轮询、 按权重负载、 按流量负载等）从维护的可用服务端清单中取出一台服务端的地址， 然后进行转发。

负载均衡

- - BestAvailabl ：选择一个最小的并发请求的 Server ，逐个考察 Server ，如果 Server被标记错误了，则跳过。然后在选择其中ActiveRequestCount最小的Server.
  - AvailabilityFilteringRule：过滤掉那些一直连接失败的且被标记为circuit tripped的后端Server，并过滤掉那些高并发的后端Server或者使用一个AvailabilityPredicate来包含过滤Server的逻辑。其实就是Status里记录的各个Server的运行状态。
  - ZoneAvoidanceRule ：使用 ZoneAvoidancePredicate AvailabilityPredicate 来判断是否选择某个 Server ，前一个判断判定一个 Zone 的运行性能是否可用，剔除不可用的Zone （的所有 Server), Availability Predicate 用于过滤掉连接数过多的 Server。
  - RandomRule ：随机选择一个 Server。
  - RoundRobinRule ：轮询选择，轮询 index ，选择 index 对应位置的 Server。
  - RetryRule ：对选定的负载均衡策略机上重试机制，也就是说当选定了某个策略进行请求负载时在一个配置时间段内若选择 Server 不成功，则一直尝试使用 ubRule方式选择一个可用的 Server。
  - Response Time WeightedRule ：作用同 WeightedResponseTimeRule, Response Time WeightedRule 后来改名为 WeightedRespons TimeRule。
  - WeightedResponseTimeRule ：根据响应时间分配一个 Weight （权重），响应时间越长，Weight 越小，被选中的可能性越低。

![img](D:/Youdao/YoudaoNote/findgalaxy@163.com/66eeeea1063c4153bd4e6373373f5ffb/clipboard.png)

均衡算法

![img](D:/Youdao/YoudaoNote/findgalaxy@163.com/c8384400702545e98a14c22d7613c4ee/clipboard.png)

![img](D:/Youdao/YoudaoNote/findgalaxy@163.com/7bf1902bfbb44de496f128ee43fbc743/clipboard.png)

使用自己定义算法

![img](D:/Youdao/YoudaoNote/findgalaxy@163.com/33bc4771b3384540a985fb13d8868fdc/clipboard.png)

——-------------------------------------------------------------------------------

\-------------------------------------------------------------------------------------------

\-------------------------------------------------------------------------------------------------

![img](D:/Youdao/YoudaoNote/findgalaxy@163.com/5579d9b7187040e79a5fc06f0942e55d/clipboard.png)

新建的类不能和主启动类在一块。。。大概就是这意思吧

![img](D:/Youdao/YoudaoNote/findgalaxy@163.com/706591646b1f45a19b634bc1bd7cf58b/clipboard.png)

配置自定义 

![img](D:/Youdao/YoudaoNote/findgalaxy@163.com/a197e4604c344bdc8cb905812a39e011/clipboard.png)

![img](D:/Youdao/YoudaoNote/findgalaxy@163.com/18ae3508e3d44c3fb801322f0da442c0/clipboard.png)

![img](D:/Youdao/YoudaoNote/findgalaxy@163.com/f0d0e0eadd8b41e2bb00aa6239dd6431/clipboard.png)

![img](D:/Youdao/YoudaoNote/findgalaxy@163.com/0558537d4919448b86c8a54bade26726/clipboard.png)

• 服务提供者只需要启动多个服务实例并注册到一个注册中心或是多个相关联的服务注册中心。

• 服务消费者直接通过调用被@LoadBalanced 注解修饰过的 RestTemplate 来实现面向服务的接口调用。