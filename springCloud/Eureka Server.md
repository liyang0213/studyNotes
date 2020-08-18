## Eureka Server

1、加入pom依赖

```xml
<!-- 基于 springboot 2.3.2.RELEASE-->
<!-- 基于 springcloud Hoxton.SR7-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
</dependency>
```

2、配置application.yml

```yml
	eureka:
​		click:
​			register-with-eureka: true  #是否将自己注册到Eureka服务中，本身就是所有无需注册
​			fetch-register: true #是否从Eureka中获取注册信息
​			service-url: #Eureka客户端和服务器端交互的地址
​				defaulZone: http://127.0.0.1:8080/eureka/  # or  ${eureka.server}
​		instance:
​			prefer-ip-address: true #将自己注册到Eureka服务中
​			ip-address: 127.0.0.1
​			instance-id: ${spring.application.name}:${server.port} #指定实例id
​		server:
​			enable-self-preservation: false #禁用自我保护模式
​			eviction-interval-timer-in-ms: 60000 #清理间隔 （单位毫，默认 60*1000）
```

3、在springboot启动类中加上开启Eureka服务的注解

```java
@EnableEurekaServer //开启Eureka服务
@SpringBootApplication
public class SpringCloudEurekaServerDemoApplication {
    public static void main(String[] args) {
        SpringApplication.run(SpringCloudEurekaServerDemoApplication.class, args);
    }
}
```

