---
title: Docker学习
link: https://blog.csdn.net/AZ_Rise/article/details/121605902
---

# Docker学习

## 初识Docker

### 什么是Docker?

项目部署的问题：
大型项目组件较多，运行环境也较为复杂，部署时会碰到一些问题:
这些服务会依赖于各种各样的应用，Node.js、Redis、RabbitMQ、MySQL等等，每一个服务的应用它们需要的依赖和函数库有差异，也有可能版本不同，所以**依赖关系复杂，容易出现兼容性问题**。

- 依赖关系复杂，容易出现兼容性问题
- 开发、测试、生产环境有差异

![](https://img-blog.csdnimg.cn/809292f45cd94c9aa3cd61c62b169ac0.png)

**Docker如何解决依赖的兼容问题的?**

- 将应用的Libs(函数库)、Deps(依赖)、配置与应用一起打包
- 将每个应用放到一个隔离**容器**去运行，避免互相干扰

![](https://img-blog.csdnimg.cn/bb347c6b26764d568c8fb6b0ee77d09f.png)

不同环境的操作系统不同，Docker如何解决?我们先来了解下操作系统结构
比如Ubuntu系统上的MySQL应用放到CentOS系统上尝试运行，当MySQL去调用函数库时，代码是写死的，这个函数库在CentOS系统上根本不存在，肯定就程序报错，这就是应用不能跨系统运行的原因。

![](https://img-blog.csdnimg.cn/cfc59c3712564336ab44057de6c32965.png)

**Docker如何解决不同系统环境的问题?**

- Docker将用户程序与所需要调用的系统(比如Ubuntu)函数库一起打包；
- Docker运行到不同操作系统时，直接基于打包的库函数，借助于操作系统的Linux内核来运行。

**Docker如何解决开发、测试、生产环境有差异的问题**

- Docker镜像中包含完整运行环境，包括系统函数库，仅依赖系统的Linux内核，因此可以在任意Linux操作系统上运行

### Docker和虚拟机的差别

虚拟机(virtual machine)是在操作系统中模拟硬件设备，然后运行另一个操作系统，比如在Windows 系统里面运行Ubuntu系统，这样就可以运行任意的Ubuntu应用了。