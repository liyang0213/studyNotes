## 负载均衡--Ribbon

#### 负载均衡

负载均衡是一种基础的网络服务，它的核心原理是按照指定的负载均衡算法，将请求分配到后端服务集群上，从而为系统提供并行处理和高可用的能力。

目前主流的负载方案分为两种：

​	(1) 集中式负载均衡：在消费者和服务提供方中间使用独立的代理方式进行负载，有硬件负载均衡器，如 F5，也有软件，如 Nginx；

​	(2) 客户端负载均衡：客户端根据自己的请求情况做负载，Ribbon 就是属于客户端自己做的的框架。

### Ribbon

Ribbon 是由 Netflix 发布的负载均衡器，有助于控制 Http 和 TCP 的客户端的行为。Ribbon 属于客户端负载均衡器。

为 Ribbon 配置服务提供地址后，Ribbon 就可基于某种负载均衡算法，自动的帮助服务消费者进行请求。同时 Ribbon 默认为我们提供了很多负载均衡算法，如：轮询、随机算法等。

### Ribbon主要组件

Ribbon 主要用于负载均衡场景，实现一个通用的负载均衡框架，则需要狠毒组件的支持，Ribbon 就是提供了这些主键，有了这些组件，整个框架的扩展性便会更好，更灵活，我们可以根据业务需求，选择是否自定义对应的组件来满足特定场景下的需求。

![image-20200822101930465](C:\Users\L1822\AppData\Roaming\Typora\typora-user-images\image-20200822101930465.png)

### 服务实例调用流程图

![image-20200822102652158](C:\Users\L1822\AppData\Roaming\Typora\typora-user-images\image-20200822102652158.png)

### 服务实例信息来源

需要一个服务实例的存储组件来支持，ServerList 就是这个组件。存储分为静态和动态两种方式。静态存储需要事先配置好固定的服务实例信息，动态存储需要从注册中心获取对应的服务实例信息。

ServerListFilter 组件可以实现过滤操作；

ServerListUpdate 组件可以实现服务实例的更新

IPing 组件可以检测服务实例信息是否可用

IRule 组件提供很多算法策略来选择实例信息

ILoadBalancer 组件中定义了富案件负载均衡操作的接口，如动态更新一组服务列表，根据指定算法从现有服务器列表中选择一个可用的服务等操作。

### 各组件作用

![image-20200822103735647](C:\Users\L1822\AppData\Roaming\Typora\typora-user-images\image-20200822103735647.png)



## 使用方法

Ribbon 的使用方式主要分为下面三种

​	(1) 原生API，Ribbon 是 Netflix 开源的，如果没有使用 Spring Cloud，也可以在项目中单独使用 Ribbon，在这种场景下就需要使用 Ribbon 的原生 API；

​	(2) Ribbon + RestTemplaate，当项目整合了 Spring Cloud 时，就可以用 Ribbon 为 RestTemplate 提供负载均衡的服务；

​	(3) Ribbon + Feign

### 	原生 API 代码示例

![image-20200822112203440](C:\Users\L1822\AppData\Roaming\Typora\typora-user-images\image-20200822112203440.png)

### 	输出结果

（1）

![image-20200822112255952](C:\Users\L1822\AppData\Roaming\Typora\typora-user-images\image-20200822112255952.png)

（2）

![image-20200822112343601](C:\Users\L1822\AppData\Roaming\Typora\typora-user-images\image-20200822112343601.png)

​	使用 RestTemplate 调用

![image-20200822113642730](C:\Users\L1822\AppData\Roaming\Typora\typora-user-images\image-20200822113642730.png)



## 负载均衡策略

### 	内置负载均衡策略

​		![image-20200822134739475](C:\Users\L1822\AppData\Roaming\Typora\typora-user-images\image-20200822134739475.png)

IRule：接口算法。AbstractLoadBalancerRule 是实现了 IRule 接口的抽象类，所有内置的算法都继承 AbstractLoadBalancerRule 来实现的。

RoundRobinRule：轮询算法。如有 A，B 两个示例，那么该算法的逻辑是选择 A，在选择 B，在选择 A，轮询下去。

RandomRule：随机算法。在服务列表中随机选取。

BestAvailableRule：选择一个最小的并发请求 sever，如果有 A，B 两个示例，当 A 有 4 个请求正在处理中，B 有2个请求正在处理中，下次选择的时候会选择 B，因为 B 处理的数量是最少的，认为它的压力最小，这种场景适合于服务所在的配置都相同的情况下，否则不太适用。

WeightedResponseTimeRule：根据请求的响应时间计算权重，如果响应时间越长，那么对应的权重越低，权重越低的服务器，被选择的可能性就越低。

### 自定义负载均衡算法

​	![image-20200822140702923](C:\Users\L1822\AppData\Roaming\Typora\typora-user-images\image-20200822140702923.png)

#### 	调用自定义的负载均衡算法

​		(1) 配置文件

​		(2) 配置注解