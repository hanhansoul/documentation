# Spring Cloud Eureka

Eureka主要负责完成微服务架构中的服务治理功能。

## 服务治理

服务治理是微服务架构中最为核心与基础的模块，主要用于实现各个微服务实例的自动化注册与发现。

- 服务注册：构建一个注册中心，每个服务单元向注册中心登记自己提供的服务，将主机与端口号、版本号、通信协议等一些附加信息告知注册中心，注册中心按服务名分类组织服务清单。
- 服务发现：通过向服务名发起请求实现具体服务的调用功能。

Eureka服务端与客户端

- Eureka服务端：服务注册中心。
- Eureka客户端：主要处理服务的注册与发现。客户端通过注解和参数配置的方式，嵌入在客户端应用程序代码中。应用程序运行时，Eureka客户端向注册中心注册自身提供的服务并周期性地发送心跳来更新它的服务租约。

常用注解

1. @EnableEurekaServer：启动一个服务注册中心提供给其他应用进行对话。
2. @EnableDiscoveryClient：激活Eureka中的DiscoveryClient实现。

步骤
1. 搭建服务注册中心
2. 注册服务提供者

## Eureka详解

### 基础架构

Eureka核心角色：
1. 服务注册中心：Eureka提供的服务端，提供服务注册与发现的功能，即eureka-server。
2. 服务提供者：提供服务的应用，如spring boot应用等遵循Eureka通信机制的应用。将自己提供的服务注册到Wureka，以供其他应用发现。
3. 服务消费者：消费者应用从服务注册中心获取服务列表，从而使消费者可以指导去何处调用其所需要的服务。

很多时候，客户端既是服务提供者也是服务消费者。

### 服务治理机制

服务提供者

- 服务注册：服务提供者在启动时会通过发送REST请求的方式将自己注册到Eureka Server上，同时带上自身服务的一些元数据信息。Eureka Server接收到这个REST请求之后，将元素朱信息存储在一个双层结构Map中，其中第一层的key为服务名，第二层的key是具体服务的实例名。
- 服务同步：当不同的服务提供者在不同的服务注册中心上注册时，由于服务注册中心直接互相注册为服务，发送的注册请求会被转发到集群中相连的其他注册中心，从而实现注册中心之间的服务同步。
- 服务续约：在注册完服务后，服务提供者会维护一个心跳用来持续告知Server，以防Server将该服务实例从服务列表中排除出去。

服务消费者

- 获取服务：当启动服务消费者时，会发送一个REST请求给服务注册中心，来获取上面注册的服务清单。确保`eureka.client.fetch-registry=true`。
- 服务调用：服务消费者通过服务名可以获得具体提供服务的实例名和该实例的元数据信息。客户端可以根据这些信息和自己的需要决定具体调用哪个实例，Ribbon中默认采用轮询的方式进行调用，从而实现负载均衡。
- 服务下线：服务关闭时，服务实例触发一个服务下线的REST请求到Eureka Server，告诉服务注册中心其下线了。服务端将其服务状态置为下线。

服务注册中心

- 失效剔除：对于故障的服务，服务注册中心将定时将没有续约的服务剔除出去。
- 自我保护

## 配置详解

Eureka客户端的配置主要分为以下两个方面：
1. 服务注册相关的配置信息，包括服务注册中心的地址、服务获取的间隔时间、可用区域等。
2. 服务实例相关的配置信息，包括服务实例的名称、IP地址、端口号、健康检查路径等。

### 服务注册类配置

#### 指定注册中心

### 服务实例类配置

服务实例元数据：
1. 服务名称
2. 实例名称
3. 实例IP
4. 实例端口

