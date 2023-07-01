# 认识微服务
## 单体架构
**单体架构**：将业务的所有功能集中在一个项目中开发，打成一个包部署。
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561681306-389df405-81b2-4852-81da-2f61ef78388b.png#averageHue=%23f9f2f1&clientId=ue42b8c5c-255d-4&from=paste&id=u9fff84d0&originHeight=416&originWidth=1203&originalType=url&ratio=1&rotation=0&showTitle=false&size=53109&status=done&style=none&taskId=u1ddcf18c-60ef-4f29-80f8-69f9a514028&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901083809.png)
**优点：**架构简单，部署成本低
**缺点：**耦合度高（维护困难、升级困难）

## 分布式架构
**分布式架构**：根据业务功能对系统做拆分，每个业务功能模块作为独立项目开发，称为一个服务。
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561685777-fcfc0986-c92a-4e91-af70-d7210ebb0daf.png#averageHue=%23f9f6f5&clientId=ue42b8c5c-255d-4&from=paste&id=ucecb63a7&originHeight=701&originWidth=1370&originalType=url&ratio=1&rotation=0&showTitle=false&size=145442&status=done&style=none&taskId=ue8447fc7-559d-48b9-a269-93d626d832e&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901092921.png)
**优点：**降低服务耦合，有利于服务升级和拓展
**缺点：**服务调用关系错综复杂
分布式架构虽然降低了服务耦合，但是服务拆分时也有很多问题需要思考：

- 服务拆分的粒度如何界定？
- 服务之间如何调用？
- 服务的调用关系如何管理？

**人们需要制定一套行之有效的标准来约束分布式架构。**
## 微服务
微服务的架构特征：

- 单一职责：微服务拆分粒度更小，每一个服务都对应唯一的业务能力，做到单一职责
- 自治：团队独立、技术独立、数据独立，独立部署和交付
- 面向服务：服务提供统一标准的接口，与语言和技术无关
- 隔离性强：服务调用做好隔离、容错、降级，避免出现级联问题

[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561681871-d6b1860e-8138-4c35-b0c6-841d8b41c2ae.png#averageHue=%23f8f3f1&clientId=ue42b8c5c-255d-4&from=paste&id=u08cc97e4&originHeight=355&originWidth=1000&originalType=url&ratio=1&rotation=0&showTitle=false&size=150652&status=done&style=none&taskId=u3d68d3d5-a4fa-4a0f-b0d9-8118ee930e9&title=)](https://cdn.xn2001.com/blog/img20210901083658.png)
微服务的上述特性**其实是在给分布式架构制定一个标准**，进一步降低服务之间的耦合度，提供服务的独立性和灵活性。做到高内聚，低耦合。
**因此，可以认为微服务是一种经过良好架构设计的分布式架构方案 。**
其中在 Java 领域最引人注目的就是 SpringCloud 提供的方案了。
## SpringCloud
SpringCloud 是目前国内使用最广泛的微服务框架。官网地址：[https://spring.io/projects/spring-cloud](https://spring.io/projects/spring-cloud)。
SpringCloud 集成了各种微服务功能组件，并基于 SpringBoot 实现了这些组件的自动装配，从而提供了良好的开箱即用体验。
其中常见的组件包括：
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561681943-8822dfda-da99-4535-ad37-92f63bd24c43.png#averageHue=%23f4f0ef&clientId=ue42b8c5c-255d-4&from=paste&id=ucde9886c&originHeight=573&originWidth=1332&originalType=url&ratio=1&rotation=0&showTitle=false&size=93585&status=done&style=none&taskId=ueff9f50d-c964-40ae-a14e-8c01f06046d&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901083717.png)
另外，SpringCloud 底层是依赖于 SpringBoot 的，并且有版本的兼容关系，如下：
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561681788-5017c43a-8e64-4a3c-835b-cf7669d1ab3a.png#averageHue=%23fefefd&clientId=ue42b8c5c-255d-4&from=paste&id=u8d4eaef5&originHeight=362&originWidth=885&originalType=url&ratio=1&rotation=0&showTitle=false&size=52387&status=done&style=none&taskId=ue9e159c1-2c34-4bf3-b733-1999a93328c&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901084050.png)
## 内容知识
需要学习的微服务知识内容
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561685498-5ea73829-9787-41ed-ab52-8f94250b0cd7.png#averageHue=%23cdcfaa&clientId=ue42b8c5c-255d-4&from=paste&id=u79a74ad8&originHeight=916&originWidth=1462&originalType=url&ratio=1&rotation=0&showTitle=false&size=297039&status=done&style=none&taskId=u5d519c95-73f9-461f-918e-da0290e62c9&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901092925.png)
技术栈
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561688231-4fb44cb9-2103-4510-9066-3e044ee0076a.png#averageHue=%23fbf9f9&clientId=ue42b8c5c-255d-4&from=paste&id=u3d45c96f&originHeight=1080&originWidth=1920&originalType=url&ratio=1&rotation=0&showTitle=false&size=220834&status=done&style=none&taskId=ue5b0ced4-6218-497b-bfe4-292ebc1d443&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901084131.png)
自动化部署
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561683999-786bcdf3-67ec-436a-98f3-626a021555fb.png#averageHue=%23fcf4ed&clientId=ue42b8c5c-255d-4&from=paste&id=u62d2d046&originHeight=468&originWidth=645&originalType=url&ratio=1&rotation=0&showTitle=false&size=38135&status=done&style=none&taskId=u4cc5345f-3b0d-4145-a47b-2b23d367b86&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901090737.png)
## 技术栈对比
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561686183-e0186628-e8fe-47d0-922c-3869be13b8c1.png#averageHue=%23d1bcbb&clientId=ue42b8c5c-255d-4&from=paste&id=u2e053966&originHeight=625&originWidth=1751&originalType=url&ratio=1&rotation=0&showTitle=false&size=92883&status=done&style=none&taskId=ue50e13b6-0231-4719-ba2c-daee0f90d85&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901090726.png)
# 服务拆分
案例源码：[https://gitee.com/xn2001/cloudcode/tree/master/01-cloud-demo](https://gitee.com/xn2001/cloudcode/tree/master/01-cloud-demo)
**服务拆分注意事项**
单一职责：不同微服务，不要重复开发相同业务
数据独立：不要访问其它微服务的数据库
面向服务：将自己的业务暴露为接口，供其它微服务调用
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561701543-bfe65c9c-0aaf-4336-8687-c025044d0ffc.png#averageHue=%23f5f2ee&clientId=ue42b8c5c-255d-4&from=paste&id=ufc2d0ae1&originHeight=384&originWidth=427&originalType=url&ratio=1&rotation=0&showTitle=false&size=29530&status=done&style=none&taskId=u84b103f6-447f-47a0-9bdd-c68ae48f97e&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901090745.png)
cloud-demo：父工程，管理依赖

- order-service：订单微服务，负责订单相关业务
- user-service：用户微服务，负责用户相关业务

要求：

- 订单微服务和用户微服务都必须有**各自的数据库**，相互独立
- 订单服务和用户服务**都对外暴露 Restful 的接口**
- 订单服务如果需要查询用户信息，**只能调用用户服务的 Restful 接口**，不能查询用户数据库

微服务项目下，打开 idea 中的 Service，可以很方便的启动。
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561706213-12f756e7-86bf-416a-8cd0-7f78d9cf149b.png#averageHue=%23eef2eb&clientId=ue42b8c5c-255d-4&from=paste&id=u0c82d417&originHeight=397&originWidth=1031&originalType=url&ratio=1&rotation=0&showTitle=false&size=77098&status=done&style=none&taskId=ua05392f6-68dd-4b69-86a0-1f49d7a7fdc&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901090750.png)
启动完成后，访问 [http://localhost:8080/order/101](http://localhost:8080/order/101)
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561704309-fd6aff7f-096c-4db5-95bd-1abe724e2ae5.png#averageHue=%23fefefd&clientId=ue42b8c5c-255d-4&from=paste&id=u7291e3b6&originHeight=320&originWidth=528&originalType=url&ratio=1&rotation=0&showTitle=false&size=20297&status=done&style=none&taskId=uf0db37f6-405c-4156-87b6-5feffee9f6d&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901090757.png)
# 远程调用
案例源码：[https://gitee.com/xn2001/cloudcode/tree/master/02-cloud-restTemplate](https://gitee.com/xn2001/cloudcode/tree/master/02-cloud-restTemplate)
正如上面的服务拆分要求中所提到，
订单服务如果需要查询用户信息，**只能调用用户服务的 Restful 接口**，不能查询用户数据库
因此我们需要知道 Java 如何去发送 http 请求，Spring 提供了一个 RestTemplate 工具，只需要把它创建出来即可。（即注入 Bean）
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561706196-9816c07f-ecdd-42dc-aa12-810e9d2aae1d.png#averageHue=%23f1f4e7&clientId=ue42b8c5c-255d-4&from=paste&id=u6c546479&originHeight=179&originWidth=626&originalType=url&ratio=1&rotation=0&showTitle=false&size=29237&status=done&style=none&taskId=uf5e3f6f9-4d27-4c74-9eab-aec253f4d9e&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901090814.png)
发送请求，自动序列化为 Java 对象。
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561713667-6a8aa51a-0c69-4459-b54f-e16dcaa73233.png#averageHue=%23f0f4e7&clientId=ue42b8c5c-255d-4&from=paste&id=ue93fffcc&originHeight=578&originWidth=1001&originalType=url&ratio=1&rotation=0&showTitle=false&size=128602&status=done&style=none&taskId=uefaf90ff-bd9f-4d11-905f-f5ffeb58935&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901090846.png)
启动完成后，访问：[http://localhost:8080/order/101](http://localhost:8080/order/101)
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561718270-4f3a8521-842f-46ae-8819-16c7f0584293.png#averageHue=%23fefdfd&clientId=ue42b8c5c-255d-4&from=paste&id=u40f32196&originHeight=434&originWidth=636&originalType=url&ratio=1&rotation=0&showTitle=false&size=34047&status=done&style=none&taskId=ub829add2-defd-4a0c-85e7-39179509aef&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901090909.png)
在上面代码的 url 中，我们可以发现调用服务的地址采用硬编码，这在后续的开发中肯定是不理想的，这就需要**服务注册中心**（Eureka）来帮我们解决这个事情。
# Eureka注册中心
案例源码：[https://gitee.com/xn2001/cloudcode/tree/master/03-cloud-eureka](https://gitee.com/xn2001/cloudcode/tree/master/03-cloud-eureka)
最广为人知的注册中心就是 Eureka，其结构如下：
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561727116-b52842ed-5e1c-4ab5-8ca2-51bc2cd44e78.png#averageHue=%23e3ebd2&clientId=ue42b8c5c-255d-4&from=paste&id=u6112eb44&originHeight=838&originWidth=1454&originalType=url&ratio=1&rotation=0&showTitle=false&size=201015&status=done&style=none&taskId=u59b43d60-8059-4109-85ac-61a00f18bfe&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901090919.png)
**order-service 如何得知 user-service 实例地址？**

- user-service 服务实例启动后，将自己的信息注册到 eureka-server(Eureka服务端)，叫做**服务注册**
- eureka-server 保存服务名称到服务实例地址列表的映射关系
- order-service 根据服务名称，拉取实例地址列表，这个叫**服务发现**或服务拉取

**order-service 如何从多个 user-service 实例中选择具体的实例？**
order-service从实例列表中利用**负载均衡算法**选中一个实例地址，向该实例地址发起远程调用
**order-service 如何得知某个 user-service 实例是否依然健康，是不是已经宕机？**

- user-service 会**每隔一段时间(默认30秒)向 eureka-server 发起请求**，报告自己状态，称为**心跳**
- 当超过一定时间没有发送心跳时，eureka-server 会认为微服务实例故障，将该实例从服务列表中剔除
- order-service 拉取服务时，就能将故障实例排除了

---

接下来我们动手实践的步骤包括：
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561728800-b51abf0b-058f-4d76-8816-7dbebbed1de3.png#averageHue=%23ddbaba&clientId=ue42b8c5c-255d-4&from=paste&id=ua8752638&originHeight=514&originWidth=1180&originalType=url&ratio=1&rotation=0&showTitle=false&size=91654&status=done&style=none&taskId=ufde2111c-9065-4fb7-8821-fc421733b59&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901090932.png)
## 搭建注册中心
**搭建 eureka-server**
引入 SpringCloud 为 eureka 提供的 starter 依赖，注意这里是用 **server**
```xml
    <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
        </dependency>
```
**编写启动类**
注意要添加一个 @EnableEurekaServer **注解**，开启 eureka 的**注册中心**功能
```java
@EnableEurekaServer
@SpringBootApplication
public class EurekaApplication {
    public static void main(String[] args) {
        SpringApplication.run(EurekaApplication.class, args);
    }
}

```
**编写配置文件**
编写一个 application.yml 文件，内容如下：
```
server:
  port: 10086 #服务端口
spring:
  application:
    name: eureka-server  #eureka服务名称
eureka:
  client:
    service-url:   #eureka地址信息
      defaultZone: http://127.0.0.1:10086/eureka
```
其中 default-zone 是因为前面配置类开启了注册中心所需要配置的 eureka 的**地址信息**，因为 eureka 本身也是一个微服务，这里也要将自己注册进来，当后面 eureka **集群**时，这里就可以填写多个，使用 “,” 隔开。
启动完成后，访问 [http://localhost:10086/](http://localhost:10086/)
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561728949-6e286f31-a86f-4a58-8e40-73afc75a5f9d.png#averageHue=%23cdcccb&clientId=ue42b8c5c-255d-4&from=paste&id=ubeb44f50&originHeight=731&originWidth=1009&originalType=url&ratio=1&rotation=0&showTitle=false&size=76883&status=done&style=none&taskId=u4f77e913-ee31-4e8d-a2c9-e187651c2a5&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901090945.png)
## 服务注册
将 user-service、order-service 都注册到 eureka
引入 SpringCloud 为 eureka 提供的 starter 依赖，注意这里是用 **client**
```xml
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-netflix-eureka-client</artifactId>
</dependency>

```
在启动类上添加注解：@EnableEurekaClient
在 application.yml 文件，添加下面的配置：
```xml
spring:
  application:
    name: eureka-order

eureka:
  client:
    service-url:   #eureka地址信息
      defaultZone: http://127.0.0.1:10086/eureka
```
3个项目启动后，访问 [http://localhost:10086/](http://localhost:10086/)
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561729042-376cba98-04aa-4b4b-a916-ab4cadddc0cf.png#averageHue=%23d0cfce&clientId=ue42b8c5c-255d-4&from=paste&id=u61133168&originHeight=649&originWidth=1019&originalType=url&ratio=1&rotation=0&showTitle=false&size=84517&status=done&style=none&taskId=ub8465379-7542-4231-a2ef-c620a8f414e&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901090958.png)
这里另外再补充个小技巧，我们可以通过 idea 的多实例启动，来查看 Eureka 的集群效果。
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561730813-5ca04998-bd2a-4c6c-8aac-03115da725a3.png#averageHue=%23e4ebe1&clientId=ue42b8c5c-255d-4&from=paste&id=u39d078dd&originHeight=472&originWidth=1625&originalType=url&ratio=1&rotation=0&showTitle=false&size=215727&status=done&style=none&taskId=ub0758db9-5398-4a66-9c87-4b69bbee6d8&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901091005.png)
4个项目启动后，访问 [http://localhost:10086/](http://localhost:10086/)
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561730816-6125b0f1-ab82-4a10-997f-e4c9365faf6e.png#averageHue=%23dcd9d8&clientId=ue42b8c5c-255d-4&from=paste&id=udc4f9181&originHeight=327&originWidth=996&originalType=url&ratio=1&rotation=0&showTitle=false&size=46306&status=done&style=none&taskId=u81bc06a1-fb27-4860-be57-4be000225e8&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901091015.png)
## 服务拉取
在 order-service 中完成服务拉取，然后通过负载均衡挑选一个服务，实现远程调用
下面我们让 order-service 向 eureka-server 拉取 user-service 的信息，实现服务发现。
首先给 RestTemplate 这个 Bean 添加一个 @LoadBalanced **注解**，用于开启**负载均衡**。（后面会讲）
**@Bean** **@LoadBalanced** **public** RestTemplate **restTemplate**(){     **return** **new** RestTemplate(); }
修改 OrderService 访问的url路径，用**服务名**代替ip、端口：
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561733499-98627163-f722-4afe-b7d5-094c50801a47.png#averageHue=%23f5f9f2&clientId=ue42b8c5c-255d-4&from=paste&id=u04805848&originHeight=651&originWidth=1046&originalType=url&ratio=1&rotation=0&showTitle=false&size=204779&status=done&style=none&taskId=ue99ea9d0-4de2-4226-abf3-d0202faa1ad&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901091216.png)
spring 会自动帮助我们从 eureka-server 中，根据 userservice 这个服务名称，获取实例列表后去完成负载均衡。
# Ribbon负载均衡
案例源码：[https://gitee.com/xn2001/cloudcode/tree/master/04-cloud-ribbon](https://gitee.com/xn2001/cloudcode/tree/master/04-cloud-ribbon)
我们添加了 @LoadBalanced 注解，即可实现负载均衡功能，这是什么原理呢？
**SpringCloud 底层提供了一个名为 Ribbon 的组件，来实现负载均衡功能。**
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561735915-53623d73-4b8a-4d45-bdb0-6647c84b8e48.png#averageHue=%23fcf8f8&clientId=ue42b8c5c-255d-4&from=paste&id=u65cc347a&originHeight=647&originWidth=1573&originalType=url&ratio=1&rotation=0&showTitle=false&size=203697&status=done&style=none&taskId=u4f50ab56-275f-4590-bb1e-4e8865387a1&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901091242.png)
## 源码跟踪
为什么我们只输入了 service 名称就可以访问了呢？为什么不需要获取ip和端口，这显然有人帮我们根据 service 名称，获取到了服务实例的ip和端口。它就是LoadBalancerInterceptor，这个类会在对 RestTemplate 的请求进行拦截，然后从 Eureka 根据服务 id 获取服务列表，随后利用负载均衡算法得到真实的服务地址信息，替换服务 id。
我们进行源码跟踪：
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561733305-22427235-3ebb-4ebf-ba4f-69b37a50d456.png#averageHue=%233f3d3c&clientId=ue42b8c5c-255d-4&from=paste&id=ue69d06f5&originHeight=435&originWidth=864&originalType=url&ratio=1&rotation=0&showTitle=false&size=58317&status=done&style=none&taskId=u6f43d61c-4c56-4f09-8491-142ba510f9b&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901091323.png)
这里的 intercept() 方法，拦截了用户的 HttpRequest 请求，然后做了几件事：

- request.getURI()：获取请求uri，即 [http://user-service/user/8](http://user-service/user/8)
- originalUri.getHost()：获取uri路径的主机名，其实就是服务id user-service
- this.loadBalancer.execute()：处理服务id，和用户请求

这里的 this.loadBalancer 是 LoadBalancerClient 类型
继续跟入 execute() 方法：
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561736018-28e533d2-9297-4ccd-a091-81b7e2106cdc.png#averageHue=%23444342&clientId=ue42b8c5c-255d-4&from=paste&id=u9d23624b&originHeight=266&originWidth=937&originalType=url&ratio=1&rotation=0&showTitle=false&size=34936&status=done&style=none&taskId=u48c45f49-9856-4a2d-9424-3426762bec0&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901091330.png)

- getLoadBalancer(serviceId)：根据服务id获取 ILoadBalancer，而 ILoadBalancer 会拿着服务 id 去 eureka 中获取服务列表。
- getServer(loadBalancer)：利用内置的负载均衡算法，从服务列表中选择一个。在图中**可以看到获取了8082端口的服务**

可以看到获取服务时，通过一个 getServer() 方法来做负载均衡:
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561737928-87342d32-a48f-45d8-8270-85c7229003ea.png#averageHue=%23595452&clientId=ue42b8c5c-255d-4&from=paste&id=u6f0437ab&originHeight=145&originWidth=724&originalType=url&ratio=1&rotation=0&showTitle=false&size=18592&status=done&style=none&taskId=u7d8ffe09-d479-4858-8b54-86f5b028bd2&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901091345.png)
我们继续跟入：
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561738526-4eaaec6f-5544-45ff-ad60-2feca681264b.png#averageHue=%23f4f3f0&clientId=ue42b8c5c-255d-4&from=paste&id=ue3882444&originHeight=228&originWidth=927&originalType=url&ratio=1&rotation=0&showTitle=false&size=24579&status=done&style=none&taskId=ub1dba155-e2d0-4c49-8ed4-785a29a0cef&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901091355.png)
继续跟踪源码 chooseServer() 方法，发现这么一段代码：
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561738510-a5b95c8f-c67b-441d-acde-90698c4844a4.png#averageHue=%2341403f&clientId=ue42b8c5c-255d-4&from=paste&id=ue0ec629d&originHeight=408&originWidth=600&originalType=url&ratio=1&rotation=0&showTitle=false&size=32303&status=done&style=none&taskId=u42ade7fe-3819-45cc-ab51-ee3c8b04469&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901091414.png)
我们看看这个 rule 是谁：
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561740681-7dfe75fc-ca2b-4254-9d49-4d3d581f4666.png#averageHue=%23454341&clientId=ue42b8c5c-255d-4&from=paste&id=u56c4d16e&originHeight=270&originWidth=724&originalType=url&ratio=1&rotation=0&showTitle=false&size=31575&status=done&style=none&taskId=u79fa1ffd-63ef-4633-8a42-9fb4dc1e9e4&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901091432.png)
这里的 rule 默认值是一个 RoundRobinRule ，看类的介绍：
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561740695-e0d9b7f8-4f6a-4d12-9847-681b37c06afd.png#averageHue=%234a4a49&clientId=ue42b8c5c-255d-4&from=paste&id=u45489dec&originHeight=176&originWidth=705&originalType=url&ratio=1&rotation=0&showTitle=false&size=13807&status=done&style=none&taskId=uc289be93-fd57-4e7f-8c02-e8477e39677&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901091442.png)
负载均衡默认使用了轮训算法，当然我们也可以自定义。
## 流程总结
SpringCloud Ribbon 底层采用了一个拦截器，拦截了 RestTemplate 发出的请求，对地址做了修改。
基本流程如下：

- 拦截我们的 RestTemplate 请求 [http://userservice/user/1](http://userservice/user/1)
- RibbonLoadBalancerClient 会从请求url中获取服务名称，也就是 user-service
- DynamicServerListLoadBalancer 根据 user-service 到 eureka 拉取服务列表
- eureka 返回列表，localhost:8081、localhost:8082
- IRule 利用内置负载均衡规则，从列表中选择一个，例如 localhost:8081
- RibbonLoadBalancerClient 修改请求地址，用 localhost:8081 替代 userservice，得到 [http://localhost:8081/user/1](http://localhost:8081/user/1)，发起真实请求

[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561745688-b5fa11f8-000e-4d6d-8472-223ffa534e90.png#averageHue=%23f9f6f6&clientId=ue42b8c5c-255d-4&from=paste&id=uf5d2f8c6&originHeight=722&originWidth=1601&originalType=url&ratio=1&rotation=0&showTitle=false&size=150439&status=done&style=none&taskId=u1529f337-d489-49bf-88a5-a8a8f645f71&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901091755.png)
## 负载均衡策略
负载均衡的规则都定义在 IRule 接口中，而 IRule 有很多不同的实现类：
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561746326-7582518b-e3d4-494e-9450-55865c5e0315.png#averageHue=%23edf5e5&clientId=ue42b8c5c-255d-4&from=paste&id=u1e738d8a&originHeight=512&originWidth=1386&originalType=url&ratio=1&rotation=0&showTitle=false&size=268215&status=done&style=none&taskId=u6b439a2a-95ea-4685-9fff-6d1cd5c6a7d&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901091811.png)
不同规则的含义如下：

| **内置负载均衡规则类** | **规则描述** |
| --- | --- |
| RoundRobinRule | 简单轮询服务列表来选择服务器。它是Ribbon默认的负载均衡规则。 |
| AvailabilityFilteringRule | 对以下两种服务器进行忽略：（1）在默认情况下，这台服务器如果3次连接失败，这台服务器就会被设置为“短路”状态。短路状态将持续30秒，如果再次连接失败，短路的持续时间就会几何级地增加。 （2）并发数过高的服务器。如果一个服务器的并发连接数过高，配置了AvailabilityFilteringRule 规则的客户端也会将其忽略。并发连接数的上限，可以由客户端设置。 |
| WeightedResponseTimeRule | 为每一个服务器赋予一个权重值。服务器响应时间越长，这个服务器的权重就越小。这个规则会随机选择服务器，这个权重值会影响服务器的选择。 |
| **ZoneAvoidanceRule** | 以区域可用的服务器为基础进行服务器的选择。使用Zone对服务器进行分类，这个Zone可以理解为一个机房、一个机架等。而后再对Zone内的多个服务做轮询。 |
| BestAvailableRule | 忽略那些短路的服务器，并选择并发数较低的服务器。 |
| RandomRule | 随机选择一个可用的服务器。 |
| RetryRule | 重试机制的选择逻辑 |

默认的实现就是 ZoneAvoidanceRule，**是一种轮询方案**。
## 自定义策略
通过定义 IRule 实现可以修改负载均衡规则，有两种方式：
1 代码方式在 order-service 中的 OrderApplication 类中，定义一个新的 IRule：
(全局生效，调用所有微服务都生效)
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561746529-418e0e92-1518-46ec-a9ff-4c3b20c2fab7.png#averageHue=%23f5f3d8&clientId=ue42b8c5c-255d-4&from=paste&id=u967adab0&originHeight=558&originWidth=1210&originalType=url&ratio=1&rotation=0&showTitle=false&size=346157&status=done&style=none&taskId=uf6b9b57f-94c6-4a07-80d3-6ae947f8e79&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901091832.png)
2 配置文件方式：在 order-service 的 application.yml 文件中，添加新的配置也可以修改规则：（当前微服务）
```xml
eureka-service:# 给需要调用的微服务配置负载均衡规则，orderservice服务去调用userservice服务  
  ribbon:
    NFLoadBalancerRuleClassName: com.netflix.loadbalancer.RandomRule # 负载均衡规则
```
**注意**：一般用默认的负载均衡规则，不做修改。
## 饥饿加载
当我们启动 orderservice，第一次访问时，时间消耗会大很多，这是因为 Ribbon 懒加载的机制。
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561747443-49d7322d-c0b5-40e4-9a17-64350b242d2c.png#averageHue=%23f5f1ef&clientId=ue42b8c5c-255d-4&from=paste&id=ue2ae06b3&originHeight=269&originWidth=793&originalType=url&ratio=1&rotation=0&showTitle=false&size=31075&status=done&style=none&taskId=u2456cd8c-ad62-4137-80d0-935fdd0e2b5&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901091850.png)
Ribbon 默认是采用懒加载，即第一次访问时才会去创建 LoadBalanceClient，拉取集群地址，所以请求时间会很长。
而饥饿加载则会在项目启动时创建 LoadBalanceClient，降低第一次访问的耗时，通过下面配置开启饥饿加载：
ribbon:   eager-load:     enabled: true     clients: userservice _# 项目启动时直接去拉取userservice的集群，多个用","隔开_
```xml
ribbon:
  eager-load:
    enabled: true
    clients:
      -eureka-service
```
# Nacos注册中心
源码案例：[https://gitee.com/xn2001/cloudcode/tree/master/05-cloud-nacos](https://gitee.com/xn2001/cloudcode/tree/master/05-cloud-nacos)
SpringCloudAlibaba 推出了一个名为 Nacos 的注册中心，在国外也有大量的使用。
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561749206-b0ab84b2-3da9-4b3c-9b41-260cedfd7fc2.png#averageHue=%23fcfbfb&clientId=ue42b8c5c-255d-4&from=paste&id=u30f26172&originHeight=714&originWidth=1379&originalType=url&ratio=1&rotation=0&showTitle=false&size=129130&status=done&style=none&taskId=u4cbd4c47-115a-4729-a3d3-b74e48e2a91&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901091857.png)
解压启动 Nacos，详细请看 [Nacos安装指南](https://www.xn2001.com/archives/661.html)
startup.cmd -m standalone
访问：[http://localhost:8848/nacos/](http://localhost:8848/nacos/)
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561748661-2dcdf01b-3025-433a-ae46-ffc5332af92b.png#averageHue=%23e9d8ae&clientId=ue42b8c5c-255d-4&from=paste&id=ua8719e4a&originHeight=530&originWidth=1029&originalType=url&ratio=1&rotation=0&showTitle=false&size=36093&status=done&style=none&taskId=u2b9c85c0-3cf8-4760-8f90-ff4ca6ba554&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901091904.png)
## 服务注册
这里上来就直接服务注册，很多东西可能有疑惑，其实 Nacos 本身就是一个 SprintBoot 项目，这点你从启动的控制台打印就可以看出来，所以就不再需要去额外搭建一个像 Eureka 的注册中心。
**引入依赖**
在 cloud-demo 父工程中引入 SpringCloudAlibaba 的依赖：
```xml
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-alibaba-dependencies</artifactId>
      <version>2.2.6.RELEASE</version>
    <type>pom</type>
    <scope>import</scope>
</dependency>
```
然后在 user-service 和 order-service 中的pom文件中引入 nacos-discovery 依赖：
```xml
<dependency>     
    <groupId>com.alibaba.cloud</groupId>     
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId> 
</dependency>
```
**配置nacos地址**
在 user-service 和 order-service 的 application.yml 中添加 nacos 地址：
```xml
spring：
  cloud:
    nacos:
      server-addr: 127.0.0.1:8848
```
项目重新启动后，可以看到三个服务都被注册进了 Nacos
老版本：添加启动类注解
@EnableDiscoveryClient
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561748681-d9468768-ed8f-4ee1-8b7d-ff4f2e3fb2fd.png#averageHue=%23fafaf9&clientId=ue42b8c5c-255d-4&from=paste&id=u744b23ca&originHeight=343&originWidth=829&originalType=url&ratio=1&rotation=0&showTitle=false&size=26538&status=done&style=none&taskId=u50e49fcb-64f7-4159-b3a3-2093346b8f4&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901091918.png)
浏览器访问：[http://localhost:8080/order/101](http://localhost:8080/order/101)，正常访问，同时负载均衡也正常。
## 分级存储模型
一个**服务**可以有多个**实例**，例如我们的 user-service，可以有:

- 127.0.0.1:8081
- 127.0.0.1:8082
- 127.0.0.1:8083

假如这些实例分布于全国各地的不同机房，例如：

- 127.0.0.1:8081，在上海机房
- 127.0.0.1:8082，在上海机房
- 127.0.0.1:8083，在杭州机房

Nacos就将同一机房内的实例，划分为一个**集群**。
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561752579-c5feab6a-5202-4cb5-947b-83bf6a564788.png#averageHue=%23faf9f6&clientId=ue42b8c5c-255d-4&from=paste&id=u4a39b26d&originHeight=770&originWidth=1554&originalType=url&ratio=1&rotation=0&showTitle=false&size=184126&status=done&style=none&taskId=ub3526f13-5531-40ad-91b0-298ae84a875&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901091928.png)
微服务互相访问时，应该尽可能访问同集群实例，因为本地访问速度更快。**当本集群内不可用时，才访问其它集群。**例如：杭州机房内的 order-service 应该优先访问同机房的 user-service。
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561750922-4ce1d7ae-2a33-4c73-826d-389a084725ca.png#averageHue=%239ebb64&clientId=ue42b8c5c-255d-4&from=paste&id=u4e1fc20e&originHeight=450&originWidth=1316&originalType=url&ratio=1&rotation=0&showTitle=false&size=55102&status=done&style=none&taskId=uea0a6c31-aefc-4664-9875-2c45b673050&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901091937.png)
## 配置集群
🙄🙄🙄🙄
调用方：(最新版本需要)
```xml
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-loadbalancer</artifactId>
            <version>3.1.0</version>
        </dependency>
```
接下来我们给 user-service **配置集群**
修改 user-service 的 application.yml 文件，添加集群配置：
```xml
  cloud:
    nacos:
      server-addr: 127.0.0.1:8848
      discovery:
        cluster-name: SH #集群
```
重启两个 user-service 实例后，我们再去启动一个上海集群的实例。
-Dserver.port=8083 -Dspring.cloud.nacos.discovery.cluster-name=SH
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561752993-84fd0bfc-469b-41e6-aba2-986d5da1494c.png#averageHue=%23f1f0f0&clientId=ue42b8c5c-255d-4&from=paste&id=u952a1d1f&originHeight=295&originWidth=722&originalType=url&ratio=1&rotation=0&showTitle=false&size=20326&status=done&style=none&taskId=u6554cc3a-c3f1-4b97-b861-1277ec2cb2d&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901091947.png)
查看 nacos 控制台：
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561753416-de15855e-75fe-4f46-a98a-2ebc7ffcfac1.png#averageHue=%23e3fbd7&clientId=ue42b8c5c-255d-4&from=paste&id=ub27265af&originHeight=587&originWidth=1036&originalType=url&ratio=1&rotation=0&showTitle=false&size=46020&status=done&style=none&taskId=ue7354df2-db1c-4208-931b-aeed0d09404&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901091957.png)
## NacosRule
Ribbon的默认实现 ZoneAvoidanceRule 并不能实现根据同集群优先来实现负载均衡，我们把规则改成 **NacosRule** 即可。我们是用 orderservice 调用 userservice，所以在 orderservice 配置规则。
```java
@Bean
public IRule iRule() {
     //默认为轮询规则，这里自定义为随机规则 
    return new NacosRule();
    }
```
另外，你同样可以使用配置的形式来完成，具体参考上面的 Ribbon 栏目。
```
user-service:
  ribbon:
    NFLoadBalancerRuleClassName: com.alibaba.cloud.nacos.ribbon.NacosRule  # 负载均衡规则
```
然后，再对 orderservice 配置集群。
```
spring:
  cloud:
    nacos:
      discovery:
        cluster-name: BJ
```
现在我启动了四个服务，分别是：

- orderservice - HZ
- userservice - HZ
- userservice1 - HZ
- userservice2 - SH

访问地址：[http://localhost:8080/order/101](http://localhost:8080/order/101)
在访问中我们发现，只有同在一个 HZ 集群下的 userservice、userservice1 会被调用，并且是随机的。
我们试着把 userservice、userservice2 停掉。依旧可以访问。
在 userservice3 控制台可以看到发出了一串的警告，因为 orderservice 本身是在 HZ 集群的，这波 HZ 集群没有了 userservice，就会去别的集群找。
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561754793-3cb7059f-4435-415e-9fc4-dd4467358508.png#averageHue=%23e6efc9&clientId=ue42b8c5c-255d-4&from=paste&id=u74f7344f&originHeight=340&originWidth=1215&originalType=url&ratio=1&rotation=0&showTitle=false&size=298859&status=done&style=none&taskId=uc4753a9a-fceb-472c-92d4-9fdae828821&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901092012.png)
## 权重配置
实际部署中会出现这样的场景：
服务器设备性能有差异，部分实例所在机器性能较好，另一些较差，我们希望性能好的机器承担更多的用户请求。但默认情况下 NacosRule 是同集群内随机挑选，不会考虑机器的性能问题。
因此，Nacos 提供了**权重配置来控制访问频率**，0~1 之间，权重越大则访问频率越高，权重修改为 0，则该实例永远不会被访问。
在 Nacos 控制台，找到 user-service 的实例列表，点击编辑，即可修改权重。
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561755319-ffaf470a-866d-46da-8d1b-44b067c08ce8.png#averageHue=%23e3fbd8&clientId=ue42b8c5c-255d-4&from=paste&id=ucbe16aae&originHeight=357&originWidth=1229&originalType=url&ratio=1&rotation=0&showTitle=false&size=31730&status=done&style=none&taskId=uab876290-ba7a-4209-b562-dfa37704439&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901092020.png)
在弹出的编辑窗口，修改权重
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561756325-7964ae6d-3b73-479f-a5de-eddb3121e6f2.png#averageHue=%23c0c0c0&clientId=ue42b8c5c-255d-4&from=paste&id=u917a978f&originHeight=639&originWidth=749&originalType=url&ratio=1&rotation=0&showTitle=false&size=26163&status=done&style=none&taskId=u8b9a60bc-c782-4d63-89ac-6484c1f8664&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901092026.png)
另外，在服务升级的时候，有一种较好的方案：我们也可以通过调整权重来进行平滑升级，例如：先把 userservice 权重调节为 0，让用户先流向 userservice2、userservice3，升级 userservice后，再把权重从 0 调到 0.1，让一部分用户先体验，用户体验稳定后就可以往上调权重啦。

🧐权重不能修改报错500问题：
删除nacos--->data下面的protocal

## 环境隔离
Nacos 提供了 namespace 来实现环境隔离功能。

- Nacos 中可以有多个 namespace
- namespace 下可以有 group、service 等
- 不同 namespace 之间**相互隔离**，例如不同 namespace 的服务互相不可见

[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561757563-d7442d8d-0e74-4378-bc3e-3633b7746e51.png#averageHue=%2377afbe&clientId=ue42b8c5c-255d-4&from=paste&id=u89ba372c&originHeight=650&originWidth=1067&originalType=url&ratio=1&rotation=0&showTitle=false&size=244289&status=done&style=none&taskId=uec887c4f-57af-4950-88f2-19925b67c2f&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901092032.png)
### 创建namespace
默认情况下，所有 service、data、group 都在同一个 namespace，名为 public(保留空间)：
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561756428-60bfb834-2947-472c-bc65-d86020a3d793.png#averageHue=%23e8ecce&clientId=ue42b8c5c-255d-4&from=paste&id=u95b4851b&originHeight=437&originWidth=556&originalType=url&ratio=1&rotation=0&showTitle=false&size=18324&status=done&style=none&taskId=u0c9a6a34-e34e-4408-bd03-c659a827c3f&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901092038.png)
我们可以点击页面新增按钮，添加一个 namespace：
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561756807-29eaaf2a-b822-4e44-8e44-57e162f55d60.png#averageHue=%23c5b8a4&clientId=ue42b8c5c-255d-4&from=paste&id=u4d03d722&originHeight=439&originWidth=724&originalType=url&ratio=1&rotation=0&showTitle=false&size=66457&status=done&style=none&taskId=u7aa173c8-3f3f-4195-bb5c-2f2fd7ced68&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901092050.png)
然后，填写表单：
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561759094-b81767da-150a-4a20-a31f-227d35c38caf.png#averageHue=%23f7f0f0&clientId=ue42b8c5c-255d-4&from=paste&id=u644b8990&originHeight=433&originWidth=723&originalType=url&ratio=1&rotation=0&showTitle=false&size=22302&status=done&style=none&taskId=ub9fabdb4-de44-4aaf-a335-baa13a36a6c&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901092059.png)
就能在页面看到一个新的 namespace：
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561759145-823badd6-b19d-40d6-b361-ba44dacdd829.png#averageHue=%23e5c38f&clientId=ue42b8c5c-255d-4&from=paste&id=ue8707c34&originHeight=289&originWidth=848&originalType=url&ratio=1&rotation=0&showTitle=false&size=21040&status=done&style=none&taskId=u1ad3afa5-d9c8-4368-8ebd-82af4cd4756&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901092114.png)
### 配置namespace
给微服务配置 namespace 只能通过修改配置来实现。
例如，修改 order-service 的 application.yml 文件：
```
spring:
   cloud:
      nacos:
        server-addr: 127.0.0.1:8848
        discovery:
          cluster-name: BJ
          namespace: 123 #命名空间名
```
重启 order-service 后，访问控制台。
**public**
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561759448-f88452e4-63d1-4df1-95f3-598fa4ec8e57.png#averageHue=%23cac2b5&clientId=ue42b8c5c-255d-4&from=paste&id=uc443393f&originHeight=215&originWidth=572&originalType=url&ratio=1&rotation=0&showTitle=false&size=34144&status=done&style=none&taskId=ud5a6bcb3-c995-4353-9e0d-313895604da&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901092143.png)
**dev**
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561760998-7d2f5229-17b6-4a9b-bd61-62a6872f918d.png#averageHue=%23f4f4f3&clientId=ue42b8c5c-255d-4&from=paste&id=u753c97ee&originHeight=214&originWidth=529&originalType=url&ratio=1&rotation=0&showTitle=false&size=34882&status=done&style=none&taskId=ub0ad4459-09a6-41ba-9e47-4d2ccf47040&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901092130.png)
此时访问 order-service，因为 namespace 不同，会导致找不到 userservice，控制台会报错：
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561766520-a6331b8c-391c-4261-9cb9-43210cdeeca4.png#averageHue=%23eabf0a&clientId=ue42b8c5c-255d-4&from=paste&id=uc590ed6a&originHeight=149&originWidth=995&originalType=url&ratio=1&rotation=0&showTitle=false&size=133637&status=done&style=none&taskId=u76888400-c5b5-4a64-b6b1-5cefa75a0fb&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901092138.png)
## 临时实例
Nacos会主动推送给消费者：服务是否宕机
Nacos 的服务实例分为两种类型：

- **临时实例**：如果实例宕机超过一定时间，会从服务列表剔除，**默认的类型**。
- 非临时实例：如果实例宕机，不会从服务列表剔除，也可以叫永久实例。

配置一个服务实例为永久实例：
spring:   cloud:     nacos:       discovery:         ephemeral: false _# 设置为非临时实例_
另外，Nacos 集群**默认采用AP方式(可用性)**，当集群中存在非临时实例时，**采用CP模式(一致性)**；而 Eureka 采用AP方式，不可切换。（这里说的是 CAP 原理，后面会写到）
# Nacos配置中心
案例地址：[https://gitee.com/xn2001/cloudcode/tree/master/05-cloud-nacos](https://gitee.com/xn2001/cloudcode/tree/master/05-cloud-nacos)
Nacos除了可以做注册中心，同样可以做配置管理来使用。
当微服务部署的实例越来越多，达到数十、数百时，逐个修改微服务配置就会让人抓狂，而且很容易出错。**我们需要一种统一配置管理方案，可以集中管理所有实例的配置。**
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561763006-512f708d-d86b-46f3-beba-bea748d8ce5b.png#averageHue=%23fcfcfc&clientId=ue42b8c5c-255d-4&from=paste&id=ud3ca98ec&originHeight=398&originWidth=948&originalType=url&ratio=1&rotation=0&showTitle=false&size=35504&status=done&style=none&taskId=u8f60c035-210e-445d-99ee-56c535158a1&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901092150.png)
Nacos 一方面可以将配置集中管理，另一方可以在配置变更时，及时通知微服务，**实现配置的热更新。**
## 创建配置
在 Nacos 控制面板中添加配置文件
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561769076-4adfe776-06d7-4a0a-973f-c4f6b1eb0a26.png#averageHue=%23e4cda1&clientId=ue42b8c5c-255d-4&from=paste&id=u014cc982&originHeight=600&originWidth=1466&originalType=url&ratio=1&rotation=0&showTitle=false&size=54389&status=done&style=none&taskId=u15d03145-0bb0-4b3b-b156-e0c205767b0&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901092159.png)
然后在弹出的表单中，填写配置信息：
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561771489-4d8a9336-5454-43a5-bc63-440666cdebeb.png#averageHue=%23979494&clientId=ue42b8c5c-255d-4&from=paste&id=u9a89d835&originHeight=638&originWidth=983&originalType=url&ratio=1&rotation=0&showTitle=false&size=106196&status=done&style=none&taskId=uf7b0dc6e-540e-44f1-85b0-3bb6379b0c6&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901092206.png)
**注意：**项目的核心配置，需要热更新的配置才有放到 nacos 管理的必要。基本不会变更的一些配置(例如数据库连接)还是保存在微服务本地比较好。
## 拉取配置
首先我们需要了解 Nacos 读取配置文件的环节是在哪一步，在没加入 Nacos 配置之前，获取配置是这样：
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561773547-a300255b-0d3d-4b7e-ae85-33129056da71.png#averageHue=%23fcfafa&clientId=ue42b8c5c-255d-4&from=paste&id=u073ecdc2&originHeight=405&originWidth=1620&originalType=url&ratio=1&rotation=0&showTitle=false&size=41925&status=done&style=none&taskId=u447acb87-4ed0-4eb0-ae37-af34439bdc5&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901092215.png)
加入 Nacos 配置，它的读取是在 application.yml 之前的：
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561777767-13adbba0-b5d3-482e-9d34-5bad1b1f31c2.png#averageHue=%23fdfbfb&clientId=ue42b8c5c-255d-4&from=paste&id=u1478569b&originHeight=529&originWidth=1604&originalType=url&ratio=1&rotation=0&showTitle=false&size=53966&status=done&style=none&taskId=ua081e881-3d88-45c8-9120-1e9de92988d&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901092223.png)
这时候如果把 nacos 地址放在 application.yml 中，显然是不合适的，**Nacos 就无法根据地址去获取配置了。**
因此，nacos 地址必须放在优先级最高的 bootstrap.yml 文件。
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561777876-38e65d0b-a13f-48a1-8d4a-f2069b91fb0a.png#averageHue=%23e6e2e2&clientId=ue42b8c5c-255d-4&from=paste&id=u30423a9b&originHeight=500&originWidth=1519&originalType=url&ratio=1&rotation=0&showTitle=false&size=79122&status=done&style=none&taskId=uf5c4808f-dae5-423a-bf3b-ca927ff6552&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901092228.png)
**引入 nacos-config 依赖**
首先，在 user-service 服务中，引入 nacos-config 的客户端依赖：
```xml
<!--nacos配置管理依赖-->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
</dependency>
```
**添加 bootstrap.yml**
然后，在 user-service 中添加一个 bootstrap.yml 文件，内容如下：
```
spring:
  application:
    name: userservice
  profiles:
    active: dev #环境
  cloud:
    nacos:
      server-addr: localhost:8848
      config:
        file-extension: yaml #后缀名
```
根据 spring.cloud.nacos.server-addr 获取 nacos地址，再根据${spring.application.name}-${spring.profiles.active}.${spring.cloud.nacos.config.file-extension}作为文件id，来读取配置。
在这个例子例中，就是去读取 userservice-dev.yaml
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561777772-32a19333-2650-4f07-8986-78eb57201702.png#averageHue=%23f4f3f3&clientId=ue42b8c5c-255d-4&from=paste&id=u481081fe&originHeight=373&originWidth=788&originalType=url&ratio=1&rotation=0&showTitle=false&size=47799&status=done&style=none&taskId=u720727b7-1a85-4c3b-9aab-d9b4f686980&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901092237.png)
使用代码来验证是否拉取成功
在 user-service 中的 UserController 中添加业务逻辑，读取 pattern.dateformat 配置并使用：
```java
  @Value("${pattern.dateformat}")
    private String dateformat;


    @RequestMapping("now")
    public String now() {
        return LocalDateTime.now().format(DateTimeFormatter.ofPattern(dateformat));
    }
```
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561778812-47dca81c-38a6-4f92-a07f-aff92b99fafa.png#averageHue=%23f1eed1&clientId=ue42b8c5c-255d-4&from=paste&id=uc66846e7&originHeight=494&originWidth=1038&originalType=url&ratio=1&rotation=0&showTitle=false&size=282313&status=done&style=none&taskId=u771f9023-2ede-445a-b732-fb58287a59e&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901092243.png)
启动服务后，访问：[http://localhost:8081/user/now](http://localhost:8081/user/now)
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561776998-a5010a8a-9355-455c-9ce5-11fc1b89d4a4.png#averageHue=%23dbbc70&clientId=ue42b8c5c-255d-4&from=paste&id=uc7d738ce&originHeight=189&originWidth=543&originalType=url&ratio=1&rotation=0&showTitle=false&size=14717&status=done&style=none&taskId=uba8d5b01-1be2-443b-ace7-219e36123aa&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901092251.png)
## 配置热更新
我们最终的目的，是修改 nacos 中的配置后，微服务中无需重启即可让配置生效，也就是**配置热更新**。
有两种方式：1. 用 @value 读取配置时，搭配 @RefreshScope；2. 直接用 @ConfigurationProperties 读取配置
### @RefreshScope
方式一：在 @Value 注入的变量所在类上添加注解 @RefreshScope
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561782642-83131042-f121-41ad-85a4-bbf395a1b241.png#averageHue=%23f2efd2&clientId=ue42b8c5c-255d-4&from=paste&id=ud9134184&originHeight=417&originWidth=920&originalType=url&ratio=1&rotation=0&showTitle=false&size=214917&status=done&style=none&taskId=u607a36c8-03b6-4cb2-a89e-d20d64de311&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901092258.png)
### @ConfigurationProperties
方式二：使用 @ConfigurationProperties 注解读取配置文件，就不需要加 @RefreshScope 注解。
在 user-service 服务中，添加一个 PatternProperties 类，读取 patterrn.dateformat 属性
```java
@ConfigurationProperties(prefix = "pattern")
@Component
@Data
public class PatternProperties {

    private  String dateformat;
}
```
## 配置共享
其实在服务启动时，nacos 会读取多个配置文件，例如：

- [spring.application.name]-[spring.profiles.active].yaml，例如：userservice-dev.yaml
- [spring.application.name].yaml，例如：userservice.yaml

这里的 [spring.application.name].yaml 不包含环境，**因此可以被多个环境共享**。
**添加一个环境共享配置**
我们在 nacos 中添加一个 userservice.yaml 文件：
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561780283-fa99c0ab-7a5d-4f83-8ff7-f6efb9d63d40.png#averageHue=%23d4d4d4&clientId=ue42b8c5c-255d-4&from=paste&id=u2c12ca9f&originHeight=443&originWidth=704&originalType=url&ratio=1&rotation=0&showTitle=false&size=23867&status=done&style=none&taskId=u6efc3715-a685-4273-ba7d-7db698d7d14&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901092323.png)
**在 user-service 中读取共享配置**
在 user-service 服务中，修改 PatternProperties 类，读取新添加的属性：
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561781153-aba615d6-af56-4609-8ab8-c90dccdf18fb.png#averageHue=%23f4f9f2&clientId=ue42b8c5c-255d-4&from=paste&id=u945fab2a&originHeight=257&originWidth=629&originalType=url&ratio=1&rotation=0&showTitle=false&size=55815&status=done&style=none&taskId=ue576d315-fe4f-4c84-ae61-3661266d62e&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901092314.png)
在 user-service 服务中，修改 UserController，添加一个方法：
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561782079-93be776d-d4ab-4323-a40f-1b5dc911a254.png#averageHue=%23f5f9f3&clientId=ue42b8c5c-255d-4&from=paste&id=ud2dca222&originHeight=470&originWidth=702&originalType=url&ratio=1&rotation=0&showTitle=false&size=88398&status=done&style=none&taskId=u0a6c9ab0-28f2-4b90-83fb-75bc06b6a9b&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901092331.png)
**运行两个 UserApplication，使用不同的profile**
修改 UserApplication2 这个启动项，改变其profile值：
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561782590-453a2f6e-7f48-4315-b208-352d9cab7763.png#averageHue=%23edf0e8&clientId=ue42b8c5c-255d-4&from=paste&id=u7d4d4116&originHeight=273&originWidth=748&originalType=url&ratio=1&rotation=0&showTitle=false&size=45809&status=done&style=none&taskId=u003c0e25-c13b-47eb-b8d1-83459d740fa&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901092345.png)
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561784781-275dd59c-be80-4e60-a505-2ccee1575a77.png#averageHue=%23f3f2f2&clientId=ue42b8c5c-255d-4&from=paste&id=u64a62c2c&originHeight=877&originWidth=1067&originalType=url&ratio=1&rotation=0&showTitle=false&size=70817&status=done&style=none&taskId=ub5addb58-748e-4511-9c52-5a61c8e15b5&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901092338.png)
这样，UserApplication(8081) 使用的 profile 是 dev，UserApplication2(8082) 使用的 profile 是test
启动 UserApplication 和 UserApplication2
访问地址：[http://localhost:8081/user/prop](http://localhost:8081/user/prop)，结果：
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561784787-06da6cde-ed75-498d-9a64-1d91bfedb6f8.png#averageHue=%23fafaf9&clientId=ue42b8c5c-255d-4&from=paste&id=uaaf30213&originHeight=200&originWidth=667&originalType=url&ratio=1&rotation=0&showTitle=false&size=60000&status=done&style=none&taskId=u0c8eb7d2-63c4-44a6-b0e9-88a41a202fc&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901092400.png)
访问地址：[http://localhost:8082/user/prop](http://localhost:8082/user/prop)，结果：
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561785109-9fbbfff3-35e9-4bab-a08e-ae93c1cd176d.png#averageHue=%23fcfbfb&clientId=ue42b8c5c-255d-4&from=paste&id=u7f3e9146&originHeight=207&originWidth=667&originalType=url&ratio=1&rotation=0&showTitle=false&size=45758&status=done&style=none&taskId=u902a395b-3d93-4d60-bcea-ff67b5b21b3&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901092419.png)
可以看出来，不管是 dev，还是 test 环境，都读取到了 envSharedValue 这个属性的值。
上面的都是同一个微服务下，**那么不同微服务之间可以环境共享吗？**
通过下面的两种方式来指定：

- extension-configs
- shared-configs

spring:    cloud:     nacos:       config:         file-extension: yaml _# 文件后缀名_         extends-configs: _# 多微服务间共享的配置列表_           - dataId: common.yaml _# 要共享的配置文件id_
spring:    cloud:     nacos:       config:         file-extension: yaml _# 文件后缀名_         shared-configs: _# 多微服务间共享的配置列表_           - dataId: common.yaml _# 要共享的配置文件id_
## 配置优先级
当 nacos、服务本地同时**出现相同属性时**，优先级有高低之分。
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561786130-799f5905-9b94-4a94-98ce-885dd6c4b0fb.png#averageHue=%23f1d2b8&clientId=ue42b8c5c-255d-4&from=paste&id=u5b4eae1a&originHeight=267&originWidth=607&originalType=url&ratio=1&rotation=0&showTitle=false&size=26040&status=done&style=none&taskId=u87aed0b5-5656-4a02-9792-f0af5509d02&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901092501.png)
更细致的配置
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561786580-cb7e555b-3cc6-4a32-abae-3c17d918a037.png#averageHue=%23f8e4d4&clientId=ue42b8c5c-255d-4&from=paste&id=u214c1faa&originHeight=715&originWidth=1484&originalType=url&ratio=1&rotation=0&showTitle=false&size=115044&status=done&style=none&taskId=uf3605139-eb18-411b-91f1-f62d8f939be&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901092520.png)
# Feign远程调用
案例地址：[https://gitee.com/xn2001/cloudcode/tree/master/06-cloud-feign](https://gitee.com/xn2001/cloudcode/tree/master/06-cloud-feign)
我们以前利用 RestTemplate 发起远程调用的代码：
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561787969-2f50aad7-241a-4617-92c8-b7547b82eafd.png#averageHue=%23e6f1e1&clientId=ue42b8c5c-255d-4&from=paste&id=u88137148&originHeight=96&originWidth=973&originalType=url&ratio=1&rotation=0&showTitle=false&size=15517&status=done&style=none&taskId=u6deb9dea-d418-4e4c-8247-d73fb1e3de3&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901092616.png)

- 代码可读性差，编程体验不统一
- 参数复杂URL难以维护

Feign 是一个声明式的 http 客户端，官方地址：[https://github.com/OpenFeign/feign](https://github.com/OpenFeign/feign)
其作用就是帮助我们**优雅的实现 http 请求的发送**，解决上面提到的问题。
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561789662-3daaecc4-4c98-4492-8654-1bca918408d4.png#averageHue=%23f5f5f4&clientId=ue42b8c5c-255d-4&from=paste&id=ue2e2171b&originHeight=293&originWidth=1113&originalType=url&ratio=1&rotation=0&showTitle=false&size=120005&status=done&style=none&taskId=ua78a3ec8-f250-4d88-8ba8-132b25704a6&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901092639.png)
## Feign使用
**引入依赖**
我们在 order-service 引入 feign 依赖：
```xml
 <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```
**添加注解**
在 order-service 启动类添加注解开启 Feign
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561790796-c168788e-24e5-4684-bcde-b52de4ba7cb0.png#averageHue=%23c2c57e&clientId=ue42b8c5c-255d-4&from=paste&id=ud3b91238&originHeight=278&originWidth=683&originalType=url&ratio=1&rotation=0&showTitle=false&size=114631&status=done&style=none&taskId=u1d81c4c3-c13d-4c98-96e9-c1de2a6d854&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901092704.png)
**请求接口**
在 order-service 中新建一个接口，内容如下
```java
@FeignClient("userservice")
public interface OrderClient {
    
    @GetMapping("user/now")
    String now();
    
    @GetMapping("user/now2")
    String now2() ;
    
    @RequestMapping("user/{id}")
    User queryById(@PathVariable("id") Long id);
    //带参数时注解@RequestParam
}
```
@FeignClient("userservice")：其中参数填写的是微服务名
@GetMapping("/user/{id}")：其中参数填写的是请求路径
这个客户端主要是基于 SpringMVC 的注解 @GetMapping 来声明远程调用的信息
Feign 可以帮助我们发送 http 请求，无需自己使用 RestTemplate 来发送了。
**测试**
```java
    @Autowired
    private OrderClient orderClient;

    public Order queryOrderById(Long orderId) {
        // 1.查询订单
        Order order = orderMapper.findById(orderId);
       /* String url = "http://userservice/user/" + order.getUserId();

        User user = restTemplate.getForObject(url, User.class);*/
        User user = orderClient.queryById(order.getUserId());

        order.setUser(user);
        // 4.返回
        return order;
    }
```
## 自定义配置
Feign 可以支持很多的自定义配置，如下表所示：

| **类型** | **作用** | **说明** |
| --- | --- | --- |
| **feign.Logger.Level** | 修改日志级别 | 包含四种不同的级别：NONE、BASIC、HEADERS、FULL |
| feign.codec.Decoder | 响应结果的解析器 | http远程调用的结果做解析，例如解析json字符串为java对象 |
| feign.codec.Encoder | 请求参数编码 | 将请求参数编码，便于通过http请求发送 |
| feign.Contract | 支持的注解格式 | 默认是SpringMVC的注解 |
| feign.Retryer | 失败重试机制 | 请求失败的重试机制，默认是没有，不过会使用Ribbon的重试 |

一般情况下，默认值就能满足我们使用，如果要自定义时，只需要创建自定义的 @Bean 覆盖默认 Bean 即可。下面以日志为例来演示如何自定义配置。
基于配置文件修改 feign 的日志级别可以针对单个服务：
```
feign:
  client:
    config:
      default:  # 针对某个微服务的配置  
        loggerLevel: FULL  #  日志级别 
```
**也可以针对所有服务：**
```
feign:
  client:
    config:
      default: # 这里用default就是全局配置，如果是写服务名称，则是针对某个微服务的配置
        loggerLevel: FULL  #  日志级别 
```
而日志的级别分为四种：

- NONE：不记录任何日志信息，这是默认值。
- BASIC：仅记录请求的方法，URL以及响应状态码和执行时间
- HEADERS：在BASIC的基础上，额外记录了请求和响应的头信息
- FULL：记录所有请求和响应的明细，包括头信息、请求体、元数据

也可以基于 **Java 代码**来修改日志级别，先声明一个类，然后声明一个 Logger.Level 的对象
```java
public class DefaultFeignConfiguration {
    @Bean
    public Logger.Level feignLogLevel() {
        return Logger.Level.BASIC; // 日志级别为BASIC 
    }
}
```
如果要**全局生效**，将其放到启动类的 @EnableFeignClients 这个注解中：
**@EnableFeignClients(defaultConfiguration = DefaultFeignConfiguration .class)** 
如果是**局部生效**，则把它放到对应的 @FeignClient 这个注解中：
**@FeignClient(value = "userservice", configuration = DefaultFeignConfiguration .class)** 
## 性能优化
Feign 底层发起 http 请求，依赖于其它的框架。其底层客户端实现有：

- **URLConnection**：默认实现，不支持连接池
- **Apache HttpClient** ：支持连接池
- **OKHttp**：支持连接池

因此提高 Feign 性能的主要手段就是使用**连接池**代替默认的 URLConnection
另外，日志级别应该尽量用 basic/none，可以有效提高性能。
**这里我们用 Apache 的HttpClient来演示连接池。**
在 order-service 的 pom 文件中引入 HttpClient 依赖
```xml
<dependency>
    <groupId>io.github.openfeign</groupId>
    <artifactId>feign-httpclient</artifactId>
 </dependency>
```
**配置连接池**
在 order-service 的 application.yml 中添加配置
```
feign:
  client:
    config:
      default:# default全局的配置  
        loggerLevel: BASIC # 日志级别，BASIC就是基本的请求和响应信息 
  httpclient:
    enabled: true # 开启feign对HttpClient的支持
    max-connections: 200   # 最大的连接数 
    max-connections-per-route: 50  # 每个路径的最大连接数
```
在 FeignClientFactoryBean 中的 loadBalance 方法中打断点
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561790974-b83a046a-7d26-4145-95ef-9b60d9e0dce9.png#averageHue=%23f3f6ee&clientId=ue42b8c5c-255d-4&from=paste&id=uf39b403d&originHeight=386&originWidth=1181&originalType=url&ratio=1&rotation=0&showTitle=false&size=123050&status=done&style=none&taskId=uce27c90f-49e1-46e7-a792-643f6d59cb1&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901092729.png)
Debug 方式启动 order-service 服务，可以看到这里的 client，底层就是 HttpClient
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561792103-2a57eb14-27ba-43bc-8a61-ea37d5956202.png#averageHue=%23f6fbf3&clientId=ue42b8c5c-255d-4&from=paste&id=udd5ba22f&originHeight=285&originWidth=707&originalType=url&ratio=1&rotation=0&showTitle=false&size=67426&status=done&style=none&taskId=uef29d324-a226-45d4-ab38-f0cc81976f6&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901092737.png)
## 最佳实践
### 继承方式
一样的代码可以通过继承来共享：
1）定义一个 API 接口，利用定义方法，并基于 SpringMVC 注解做声明
2）Feign 客户端、Controller 都集成该接口
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561792835-12a18969-5e07-40d2-a558-3f7b06e7c367.png#averageHue=%23e3e9d2&clientId=ue42b8c5c-255d-4&from=paste&id=ue45bf134&originHeight=405&originWidth=1221&originalType=url&ratio=1&rotation=0&showTitle=false&size=48209&status=done&style=none&taskId=ude42e770-5e62-45c1-b7d3-b49fec4f06f&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901092803.png)
优点

- 简单
- 实现了代码共享

缺点

- 服务提供方、服务消费方紧耦合
- 参数列表中的注解映射并不会继承，因此 Controller 中必须再次声明方法、参数列表、注解
### 抽取方式
将 FeignClient 抽取为独立模块，并且把接口有关的 POJO、默认的 Feign 配置都放到这个模块中，提供给所有消费者使用。
例如：将 UserClient、User、Feign 的默认配置都抽取到一个 feign-api 包中，所有微服务引用该依赖包，即可直接使用。
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561793849-b0afcd05-dd66-42b8-9ac1-d47f695f6f6c.png#averageHue=%23cecd99&clientId=ue42b8c5c-255d-4&from=paste&id=u1b1fc96b&originHeight=465&originWidth=1383&originalType=url&ratio=1&rotation=0&showTitle=false&size=80910&status=done&style=none&taskId=ua23e49b7-8294-40f9-ba27-a0e83af9bd8&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901092811.png)
接下来我们就用该方法在代码中实现
**首先创建一个 module，命名为 feign-api**
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561793961-ac00a32d-fd65-408d-b211-b2bdbba84e6d.png#averageHue=%23f1f1f0&clientId=ue42b8c5c-255d-4&from=paste&id=ubccedd1f&originHeight=220&originWidth=717&originalType=url&ratio=1&rotation=0&showTitle=false&size=10692&status=done&style=none&taskId=ufed8d321-06af-4a6d-9e87-0b872414182&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901092835.png)
在 feign-api 中然后引入依赖
```xml
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-openfeign</artifactId>
        </dependency>
```
order-service中的 UserClient、User 都复制到 feign-api 项目中
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561794876-df4787c0-c478-48f6-bac8-3f739a6bc9d4.png#averageHue=%23f9f3e3&clientId=ue42b8c5c-255d-4&from=paste&id=ub264762f&originHeight=242&originWidth=345&originalType=url&ratio=1&rotation=0&showTitle=false&size=50258&status=done&style=none&taskId=u1fa7dd70-1958-48eb-8c59-3ac366e7ae5&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901092848.png)
**在order-service中使用 feign-api**
首先，删除 order-service 中的 UserClient、User
在 order-service 中引入 feign-api
```xml
<dependency>    
  <groupId>com.xn2001.feign</groupId>     
  <artifactId>feign-api</artifactId>     
  <version>1.0</version> 
</dependency>
```
**修改注解**
当定义的 FeignClient 不在 SpringBootApplication 的扫描包范围下时，这些 FeignClient 就不能使用。
修改 order-service 启动类上的 @EnableFeignClients 注解
**@EnableFeignClients(basePackages = "com.xn2001.feign.clients") 或者**
**@EnableFeignClients(clients={XXXClient.Class})**
# Gateway网关
案例地址：[https://gitee.com/xn2001/cloudcode/tree/master/07-cloud-gateway](https://gitee.com/xn2001/cloudcode/tree/master/07-cloud-gateway)
Spring Cloud Gateway 是 Spring Cloud 的一个全新项目，该项目是基于 Spring 5.0，Spring Boot 2.0 和 Project Reactor 等响应式编程和事件流技术开发的网关，它旨在为微服务架构提供一种简单有效的统一的 API 路由管理方式。
Gateway 网关是我们服务的守门神，**所有微服务的统一入口。**
网关的**核心功能特性**：

- 请求路由
- 权限控制
- 限流

[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561797473-c0eb161e-1423-4eb7-800f-9e16612547a5.png#averageHue=%23fbf6f6&clientId=ue42b8c5c-255d-4&from=paste&id=uf4439108&originHeight=669&originWidth=1378&originalType=url&ratio=1&rotation=0&showTitle=false&size=109468&status=done&style=none&taskId=u69706e1d-25f1-44e2-b821-a35bf6a54e7&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210901092857.png)
**权限控制**：网关作为微服务入口，需要校验用户是是否有请求资格，如果没有则进行拦截。
**路由和负载均衡**：一切请求都必须先经过 gateway，但网关不处理业务，而是根据某种规则，把请求转发到某个微服务，这个过程叫做路由。当然路由的目标服务有多个时，还需要做负载均衡。
**限流**：当请求流量过高时，在网关中按照下流的微服务能够接受的速度来放行请求，避免服务压力过大。
在 SpringCloud 中网关的实现包括两种：

- gateway
- zuul

Zuul 是基于 Servlet 实现，属于阻塞式编程。而 Spring Cloud Gateway 则是基于 Spring5 中提供的WebFlux，属于响应式编程的实现，具备更好的性能。
## 入门使用

1. 创建 SpringBoot 工程 gateway，引入网关依赖
2. 编写启动类
3. 编写基础配置和路由规则
4. 启动网关服务进行测试
```xml
<!--网关--> 
<dependency>
     <groupId>org.springframework.cloud</groupId>
     <artifactId>spring-cloud-starter-gateway</artifactId>
</dependency>
 <!--nacos服务发现依赖-->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
```
创建 application.yml 文件，内容如下：
```yaml
server:
  port: 8004
spring:
  application:
    name: gateway
  cloud:
    nacos:
      server-addr: 127.0.0.1:8848
    gateway:
      routes:
        - id: user-service # 路由id，自定义，只要唯一即可
          # uri: http://127.0.0.1:8081 # 路由的目标地址 http就是固定地址
          uri: lb://userservice # 路由的目标地址 lb就是负载均衡，后面跟服务名称
          predicates: # 路由断言，也就是判断请求是否符合路由规则的条件
            - Path=/user/** # 这个是按照路径匹配，只要以/user/开头就符合要求
```
我们将符合Path 规则的一切请求，都代理到 uri参数指定的地址。
上面的例子中，我们将 /user/** 开头的请求，代理到 lb://userservice，其中 lb 是负载均衡(LoadBalance)，根据服务名拉取服务列表，实现负载均衡。
重启网关，访问 [http://localhost:10010/user/1](http://localhost:10010/user/1) 时，符合 /user/** 规则，请求转发到 uri：[http://userservice/user/1](http://userservice/user/1)
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561796086-afc736c3-203b-45d5-a7a3-d4ec82db8f24.png#averageHue=%23fcfcfc&clientId=ue42b8c5c-255d-4&from=paste&id=u41bc4081&originHeight=215&originWidth=445&originalType=url&ratio=1&rotation=0&showTitle=false&size=35340&status=done&style=none&taskId=u93076595-667a-41b0-94f5-1ed23e96408&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/202108220125749.png)
多个 predicates 的话，要同时满足规则，下文有例子。
## 流程图
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561798436-211548cf-e871-4799-a8e1-0427bcd89e4f.png#averageHue=%23f7efef&clientId=ue42b8c5c-255d-4&from=paste&id=u1409b31d&originHeight=635&originWidth=1281&originalType=url&ratio=1&rotation=0&showTitle=false&size=96201&status=done&style=none&taskId=uce730abc-ee59-4c3c-b637-f251c74ed69&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/202108220127419.png)
路由配置包括：

1. 路由id：路由的唯一标示
2. 路由目标（uri）：路由的目标地址，http代表固定地址，lb代表根据服务名负载均衡
3. 路由断言（predicates）：判断路由的规则
4. 路由过滤器（filters）：对请求或响应做处理
## 断言工厂
我们在配置文件中写的断言规则只是字符串，这些字符串会被 Predicate Factory 读取并处理，转变为路由判断的条件。
例如 Path=/user/** 是按照路径匹配，这个规则是由
org.springframework.cloud.gateway.handler.predicate.PathRoutePredicateFactory 类来处理的，像这样的断言工厂在 Spring Cloud Gateway 还有十几个

| **名称** | **说明** | **示例** |
| --- | --- | --- |
| After | 是某个时间点后的请求 | - After=2037-01-20T17:42:47.789-07:00[America/Denver] |
| Before | 是某个时间点之前的请求 | - Before=2031-04-13T15:14:47.433+08:00[Asia/Shanghai] |
| Between | 是某两个时间点之前的请求 | - Between=2037-01-20T17:42:47.789-07:00[America/Denver], 2037-01-21T17:42:47.789-07:00[America/Denver] |
| Cookie | 请求必须包含某些cookie | - Cookie=chocolate, ch.p |
| Header | 请求必须包含某些header | - Header=X-Request-Id, \\d+ |
| Host | 请求必须是访问某个host（域名） | - Host=**.somehost.org, **.anotherhost.org |
| Method | 请求方式必须是指定方式 | - Method=GET,POST |
| Path | 请求路径必须符合指定规则 | - Path=/red/{segment},/blue/** |
| Query | 请求参数必须包含指定参数 | - Query=name, Jack或者- Query=name |
| RemoteAddr | 请求者的ip必须是指定范围 | - RemoteAddr=192.168.1.1/24 |
| Weight | 权重处理 |  |

官方文档：[https://docs.spring.io/spring-cloud-gateway/docs/current/reference/html/#gateway-request-predicates-factories](https://docs.spring.io/spring-cloud-gateway/docs/current/reference/html/#gateway-request-predicates-factories)
一般的，我们只需要掌握 Path，加上官方文档的例子，就可以应对各种工作场景了。
predicates:   - Path=/order/**   - After=2031-04-13T15:14:47.433+08:00[Asia/Shanghai]
像这样的规则，现在是 2021年8月22日01:32:42，很明显 After 条件不满足，可以不会转发，路由不起作用。
## 过滤器工厂
GatewayFilter 是网关中提供的一种过滤器，可以对进入网关的请求和微服务返回的响应做处理。
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561798466-d6e7c908-cc3f-49ad-b6ce-7558e22e39ae.png#averageHue=%23fdecec&clientId=ue42b8c5c-255d-4&from=paste&id=u9acd8938&originHeight=415&originWidth=1129&originalType=url&ratio=1&rotation=0&showTitle=false&size=69486&status=done&style=none&taskId=u67476529-7d28-4b1b-89ce-e120f7a1bd6&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/202108220133487.png)
Spring提供了31种不同的路由过滤器工厂。
官方文档：[https://docs.spring.io/spring-cloud-gateway/docs/current/reference/html/#gatewayfilter-factories](https://docs.spring.io/spring-cloud-gateway/docs/current/reference/html/#gatewayfilter-factories)

| **名称** | **说明** |
| --- | --- |
| AddRequestHeader | 给当前请求添加一个请求头 |
| RemoveRequestHeader | 移除请求中的一个请求头 |
| AddResponseHeader | 给响应结果中添加一个响应头 |
| RemoveResponseHeader | 从响应结果中移除有一个响应头 |
| RequestRateLimiter | 限制请求的流量 |

下面我们以 AddRequestHeader 为例：
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561798353-3866a209-ce8e-424a-bdd0-c8a22cf8e105.png#averageHue=%23fbfafa&clientId=ue42b8c5c-255d-4&from=paste&id=u4f92a9ac&originHeight=392&originWidth=991&originalType=url&ratio=1&rotation=0&showTitle=false&size=26423&status=done&style=none&taskId=uc559e54f-e9e5-4082-ba74-66313fdeab5&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/202108220139913.png)
**需求**：给所有进入 userservice 的请求添加一个请求头：sign=xn2001.com is eternal
只需要修改 gateway 服务的 application.yml文件，添加路由过滤即可。
```yaml
server:
  port: 8004
spring:
  application:
    name: gateway
  cloud:
    nacos:
      server-addr: 127.0.0.1:8848
    gateway:
      routes:
        - id: user-service # 路由id，自定义，只要唯一即可
          # uri: http://127.0.0.1:8081 # 路由的目标地址 http就是固定地址
          uri: lb://userservice # 路由的目标地址 lb就是负载均衡，后面跟服务名称
          predicates: # 路由断言，也就是判断请求是否符合路由规则的条件
            - Path=/user/** # 这个是按照路径匹配，只要以/user/开头就符合要求
            - Before=2031-04-13T15:14:47.433+08:00[Asia/Shanghai]
          filters:
            - AddRequestHeader=sign, xn2001.com is eternal
```
如何验证，我们修改 userservice 中的一个接口
**@GetMapping("/{id}")** **public** User **queryById**(**@PathVariable("id")** Long id, **@RequestHeader(value = "sign", required = false)** String sign) {     log.warn(sign);     **return** userService.queryById(id); }
重启两个服务，访问：[http://localhost:10010/user/1](http://localhost:10010/user/1)
可以看到控制台打印出了这个请求头
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561802016-434a7347-9fc8-4945-9983-cd06261e5234.png#averageHue=%23e6e96b&clientId=ue42b8c5c-255d-4&from=paste&id=u3466c7cc&originHeight=230&originWidth=1300&originalType=url&ratio=1&rotation=0&showTitle=false&size=229489&status=done&style=none&taskId=u5fa6b0cf-afdb-4517-b2e7-f3fbbcd36df&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/202108220145565.png)
当然，Gateway 也是有**全局过滤器**的，如果要**对所有的路由都生效**，则可以将过滤器工厂写到 default-filters 下：

```yaml
spring:    
  cloud:     
    gateway:       
      default-filters:         
        - AddRequestHeader=sign, xn2001.com is eternal # 添加请求头
```
## 全局过滤器
上面介绍的过滤器工厂，网关提供了 31 种，但每一种过滤器的作用都是固定的。**如果我们希望拦截请求，做自己的业务逻辑则没办法实现**。
全局过滤器的作用也是处理一切进入网关的请求和微服务响应，**与 GatewayFilter 的作用一样**。区别在于 GlobalFilter 的逻辑可以**写代码来自定义规则**；而 GatewayFilter 通过配置定义，处理逻辑是固定的。
**需求：**定义全局过滤器，拦截请求，判断请求的参数是否满足下面条件

- 参数中是否有 authorization
- authorization 参数值是否为 admin

如果同时满足则放行，否则拦截。
```java
@Component
@Order(-1)
// 设置过滤器优先级，值越低优先级越高  
public class AuthorizeFilter implements GlobalFilter {
    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
        ServerHttpRequest request = exchange.getRequest();
        // 获取第一个 authorization 参数  
        String authorization = request.getQueryParams().getFirst("authorization");
        if ("authorization".equals(authorization)){
            
            return chain.filter(exchange);
        }
        
        exchange.getResponse().setStatusCode(HttpStatus.UNAUTHORIZED);
        return exchange.getResponse().setComplete();
    }
}

```
> 继承order
> **public** **int** **getOrder**() {         **return** 0;     }

## 过滤器顺序
请求进入网关会碰到三类过滤器：DefaultFilter、当前路由的过滤器、GlobalFilter；
请求路由后，会将三者合并到一个过滤器链（集合）中，排序后依次执行每个过滤器.
[![image.png](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561800919-1940a018-7a4a-4197-8329-3e26d93480c2.png#averageHue=%23cfc4c3&clientId=ue42b8c5c-255d-4&from=paste&id=ub1a5ec30&originHeight=475&originWidth=1368&originalType=url&ratio=1&rotation=0&showTitle=false&size=100531&status=done&style=none&taskId=u39c65025-1d21-482a-9d17-46741f03198&title=)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/202108230002747.png)
排序的规则是什么呢？

- 每一个过滤器都必须指定一个 int 类型的 order 值，**order 值越小，优先级越高，执行顺序越靠前**。
- GlobalFilter 通过实现 Ordered 接口，或者使用 @Order 注解来指定 order 值，由我们自己指定。
- 路由过滤器和 defaultFilter 的 order 由 Spring 指定，默认是按照声明顺序从1递增。
- 当过滤器的 order 值一样时，**会按照 defaultFilter > 路由过滤器 > GlobalFilter 的顺序执行。**
## 跨域问题
不了解跨域问题的同学可以百度了解一下；在 Gateway 网关中解决跨域问题还是比较方便的。
```yaml
spring:
  cloud:
    gateway:
      globalcors: # 全局的跨域处理
        add-to-simple-url-handler-mapping: true # 解决options请求被拦截问题
        corsConfigurations:
          '[/**]':
            allowedOrigins: # 允许哪些网站的跨域请求 allowedOrigins: “*” 允许所有网站
              - "http://localhost:8090"
            allowedMethods: # 允许的跨域ajax的请求方式
              - "GET"
              - "POST"
              - "DELETE"
              - "PUT"
              - "OPTIONS"
            allowedHeaders: "*" # 允许在请求中携带的头信息
            allowCredentials: true # 是否允许携带cookie
            maxAge: 360000 # 这次跨域检测的有效期
```