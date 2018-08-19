# Common Conception

## 闭包\(Closure\)

### 闭包定义

> * [/developer.mozilla.org/.../Closures](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Closures)：理解闭包
> * \[wikipedia\]\([https://zh.wikipedia.org/wiki/闭包\_\(计算机科学\)\)：表述概念化](https://zh.wikipedia.org/wiki/闭包_%28计算机科学%29%29：表述概念化)
>
>   [ruanyifeng: learning\_javascript\_closures](http://www.ruanyifeng.com/blog/2009/08/learning_javascript_closures.html)：思考题部分，理解闭包

* **什么是闭包**：闭包是函数和声明该函数的词法环境的组合，闭包可以用来在**一个函数**与一组**私有变量**之间建立关联关系。
* **闭包变量的生命周期**：在函数多次调用的过程中，这些私有变量能够保持其持久性。
* **闭包变量的作用域**：仅限于包含它们的函数，因此无法从其它程序代码部分进行访问。
* **用途**：[https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Closures](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Closures)
* **与纯函数区别**：闭包不再具有引用透明性，因此也不再是纯函数，不是函数式编程
* **闭包转函数**：通过将自由变量放进参数表、并扩大函数名字的作用域，可以把一个闭包 / 匿名 / 内部函数变成一个普通的函数，这叫做“Lambda 提升”

典型的支持闭包的语言中，通常将函数当作第一类对象

## 编程范式

### 命令式编程\(Imperative\)、声明式编程\(Declarative\)和函数式编程\(Functional\)

命令式编程\(Imperative\)：怎么做How 声明式编程\(Declarative\)：做什么What 函数式编程\(Functional\)：声明式，函数一等公民

## RESTful

> [/w3cnote/restful-architecture.html](http://www.runoob.com/w3cnote/restful-architecture.html)

REST全称是Representational State Transfer，表现层状态转换。一种万维网软件架构风格，目的是便于不同软件/程序在网络中互相传递信息。

### 关键概念

REST架构风格最重要的架构约束有6个：

* 客户-服务器（Client-Server）

  通信只能由客户端单方面发起，表现为请求-响应的形式。

* 无状态（Stateless）

  通信的会话状态（Session State）应该全部由客户端负责维护。

* 缓存（Cache）

  响应内容可以在通信链的某处被缓存，以改善网络效率。

* 统一接口（Uniform Interface）

  通信链的组件之间通过统一的接口相互通信，以提高交互的可见性。

* 分层系统（Layered System）

  通过限制组件的行为（即每个组件只能“看到”与其交互的紧邻层），将架构分解为若干等级的层。

#### 资源与URI

* 任何事物，只要有被引用到的必要，它就是一个资源
* 资源有唯一标识，就是URI\(Uniform Resource Identifier\)。
* URI的设计应该具有自描述性

#### 统一资源接口

统一接口包含了一组受限的预定义的操作。接口应该使用标准的HTTP方法如GET，PUT和POST。 两个概念：

* 安全：无论请求多少次，都不会改变服务器状态
* 幂等：无论对资源操作多少次， 结果总是一样的

基本操作

* GET: 安全且幂等
* POST: 不安全且不幂等
* PUT: 不安全但幂等
* DELETE：不安全但幂等

常见问题：

**POST和PUT用于创建资源时有什么区别？** 所创建的资源的名称\(URI\)是否由客户端决定。

**统一资源接口对URI有什么指导意义** URI只应该来表示资源的名称，而不应该包括资源的操作

如果按照HTTP方法的语义来暴露资源，那么接口将会拥有安全性和幂等性的特性，例如GET和HEAD请求都是安全的， 无论请求多少次，都不会改变服务器状态。而GET、HEAD、PUT和DELETE请求都是幂等的，无论对资源操作多少次， 结果总是一样的，后面的请求并不会产生比第一次更多的影响。

#### 资源的表述

客户端可以通过Accept头请求一种特定格式的表述，服务端则通过Content-Type告诉客户端资源的表述形式。也可以带版本号。

#### 资源的链接

#### 状态的转移

* REST原则中的无状态通信原则，是指服务端不应该保存客户端状态。
* 客户端负责维护应用状态，而服务端维护资源状态。
* "会话"状态不是作为资源状态保存在服务端的，而是被客户端作为应用状态进行跟踪的。

