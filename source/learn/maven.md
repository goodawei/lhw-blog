Maven介绍
Maven是一个Java项目管理和构建工具

Maven使用pom.xml定义项目内容，并使用预设的目录结构

在Maven中声明一个依赖项可以自动下载并导入classpath

Maven使用groupId，artifactId和version唯一定位一个jar包


学习创建一个meven 项目：并更新 junit 版本和jdk版本


依赖管理
Maven通过解析依赖关系确定项目所需的jar包

常用的4种scope：

compile(默认)
test     如 junit
runtime  如 logfj
provided

Maven从中央仓库下载所需jar包并缓存在本地

可以通过镜像加速下载

aliyun镜像仓库配置： 在 ~/.m2/settings.xml中配置


```java
<settings>
  <mirrors>
    <mirror>
      <id>aliyun</id>
      <name>aliyun</name>
      <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
      <mirrorOf>central</mirrorOf>
    </mirror>
  </mirrors>
</settings>
```



构建流程
理解Maven的概念：Lifecycle，Phase和Goal：

使用Maven构建项目就是执行Lifecycle

执行Lifecycle就是按顺序执行一系列Phase

每执行一个Phase，都会执行该Phase绑定的若干Goal

Goal是最小执行任务单元

常用的命令：mvn clean package（不会打包依赖的jar）



使用插件
Maven通过自定义插件可以执行项目构建时需要的额外功能

在pom.xml中声明插件及配置

插件会在某个Phase被执行

插件的配置和用法需参考插件官方文档

常用插件：

maven-shade-plugin
cobertura-maven-plugin
findbugs-maven-plugin


