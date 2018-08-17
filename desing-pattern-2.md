# 设计模式（二）

## 设计模式分类

* 创建型模式
* 结构型模式
* 行为型模式

[创建型模式](http://www.cnblogs.com/shuigu/p/6908520.html):

* 工厂模式　
* 抽象工厂模式 
* 单例模式
* 构造者模式
* 原型模式

[结构型模式](http://www.cnblogs.com/shuigu/p/6911822.html)

* 适配器模式（Adapter）　　　　　　　　
* 外观模式（Facade）
* 桥接模式（Bridge）
* 装饰器模式（Decorator）
* 代理模式（Proxy）
* 享元模式（Flyweight）
* 组合模式（Composite）

[行为型模式](https://www.cnblogs.com/shuigu/p/6911827.html)：

* 模板方法模式（Template Method）
* 策略模式（Strategy）
* 命令模式（Command）
* 中介者模式（Mediator）
* 观察者模式（Observer）
* 迭代器模式（Iteratior）
* 访问者模式（Visiter）
* 责任链模式（Chain of Responsibility）
* 备忘录模式（Memento）
* 状态模式（State）
* 解释器模式（Interpreter）

## 中介者模式

中介者模式（Mediator Pattern）是用来降低多个对象和类之间的通信复杂性。这种模式提供了一个中介类，该类通常处理不同类之间的通信。

![](.gitbook/assets/image%20%2810%29.png)

## 责任链模式

对象引用同一类型的另一个对象，形成一条链。链中的每个对象实现了同样的方法，处理对链中第一个对象发起的同一个请求。

适用：

* 有多个对象可处理请求，而处理程序只有在运行时才能确定。
* 向一组对象发出请求，而不想现显式指定处理请求的特定处理程序。

![](.gitbook/assets/image%20%2812%29.png)

