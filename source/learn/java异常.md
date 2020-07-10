1. java 不能返回多个值，如果要想返回多个值，可以java bean 包装起来，类似于php 数组

java 异常体系

主要分类2大类：

Error 和 Exception 都继承了 throwable

我们需要捕获的异常 主要是 Exception 下边的子类，但其中不包括 runtimeExection 

我不需要捕获的异常 主要是 Error 下边的子类，但还要 包括 runtimeExection  【error 意味着程序对此 无能为力了】



不捕获的Checked Exception必须用throws声明， java throws 是写在方法名 后面。

main()是最后捕获异常的机会


2.

使用multi-catch捕获多种类型异常

多个catch子句从上到下匹配

顺序非常重要，子类必须在前。主要如果不存在继承关系的， 可以用 e1 | e2 或者。

注意 ： finally保证有无错误都会执行。

3. 

打印方法调用栈：e.printStackTrace()

异常转换，需要注意应 在转换的异常处 把原来的异常传进被转换的异常。


4. 断言 Assertion

断言是一种调试方式，断言失败会抛出AssertionError，导致程序退出

只能在开发和测试阶段启用断言
对可恢复的错误不能使用断言，而应该抛出异常
断言很少被使用，更好的方法是编写单元测试


5. jdk  内置了一个 logging, 位置： java.util.logging

JDK Logging的局限：

JVM启动时读取配置文件并完成初始化
JVM启动后无法修改配置
需要在JVM启动时传递参数 -Djava.util.logging.config.file=config-file-name





