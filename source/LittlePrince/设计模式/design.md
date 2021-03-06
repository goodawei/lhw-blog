设计原则： 体现的是规约，契约等。设计要遵循设计原则。

设计模式：套路和方法论。

多肽，继承，封装 等体现的是语言特性。


里式替换原则：重点了解使用的场景是： 
在设计继承关系时，如何设计规范子类，子类的设计要遵循父类所约定的规则契约。比如父类约定好的抽象方法，没有抛出异常的环节，子类复写时就不能有抛出异常。总之子类的设计就是扩展改变父类的实现逻辑，其他的都要遵循约定，就像接口提供的契约一样。


1. 模板方法： 父类定义一个通用方法template, 这个方法约束了一些框架整体流程，在这些流程中有一些变化性的业务，将这些业务，推迟到子类去实现。（通常可以通过里式替换原则来指导继承关系的设计）

2. 构造者/建造者/适配器： 创建对象之前先创建构造者，构造者规范了，要创建对象的参数，以及注入统一校验方式。构造者的使用类似于链条： set->set-buile(),最终返回对象。

3. 原型模式： 首先要了解2个概念，深拷贝和浅拷贝，一般计算机程序语言设计存储都分为地址和值，浅拷贝只是拷贝地址，不拷贝值，拷贝出来的对象和源对象共享数据，深拷贝则是地址和值都拷贝，原型模式的目的就是将一个经过各种复杂的逻辑过程或者计算过程获得的一个对象，避免重复创建，而使用的一种模式。

4. 代理： 首先要了解几个概念， 1是 如何在不改变一个类的情况下去增强一个类的功能，2 是AOP，动态代理如何实现的。就是代理模式灵魂所在。它的实现方式简单的设计可以将代理类和被代理类同时实现同一个接口，使用的时候先创建代理类，代理类执行完逻辑之后，在通过委托执行被代理类的逻辑。这个过程也装饰过程。第二中是通过代理类继承被代理类，执行完代理流程后通过 parent.method()调用原始流程。进阶的设计是，将代理流程设计成动态代理流程，通常是设计一个总的代理类，被代理的类通过最外层告知总代，然后由总代完成自动创建代理并执行代理逻辑和被代理逻辑。一个经常说的词，在包一层。

5. 适配器: 通常是现有程序不兼容， 通过适配包裹使其能够兼容。常用于程序系统改造升级等。

6. 装饰器： 与代理很相似，只是装饰器常常与业务相关。而代理偏向于工程相关。

7. 代理，适配器，装饰器 概念理解上有很多相似的地方，因为从总的称呼上，他们三个又称为 warmp 类，

8. 工厂 和 单例 