---
title: Best Practice Of Jenkins 
date: 2017-05-11 22:57:34
tags: jenkins
---


### Jenkins交付流水

DevOps和产品团队需要熟悉的Jenkins技能

1. 持续集成
- 持续集成概念简介
- 项目交付流水线：持续集成的表现形式
- Web和Mobile项目的典型交付流水线
2. Jenkins持续集成系统
- Jenkins的特点
- 理解流水线即代码（Pipeline as Code）
3. Jenkins的使用
- 流水线的目录层级规划
- 在Jenkins中创建流水线
 1. 配置定时检查代码更新
 2. 配置带输入参数的流水线
 3. 添加流水线内容
- Jenkinsfile的使用
 1. Jenkinsfile的管理：项目中的pipeline目录
 2. 标准流水线的常见步骤
 3. 常用的Jenkinsfile语法
 4. Jenkins中的Pipeline Syntax工具

 ----
 
<!-- more -->


### 建议阅读材料

1. Jenkins Pipeline官方教程：
- https://github.com/jenkinsci/pipeline-plugin/blob/master/TUTORIAL.md
2. Jenkins Pipeline的10个最佳实践：
- http://www.cnblogs.com/itech/p/5678643.html
3. Jenkinsfile官方示例：
- https://github.com/jenkinsci/pipeline-examples

### 持续集成基础概念

持续集成，Continuous integration ，简称CI。

随着软件开发复杂度的不断提高，团队开发成员间如何更好地协同工作以确保软件开发的质量已经慢慢成为开发过程中不可回避的问题。尤其是近些年来，敏捷（Agile） 在软件工程领域越来越红火，如何能再不断变化的需求中快速适应和保证软件的质量也显得尤其的重要。
持续集成正是针对这一类问题的一种软件开发实践。它倡导团队开发成员必须经常集成他们的工作，甚至每天都可能发生多次集成。而每次的集成都是通过自动化的构建来验证，包括自动编译、发布和测试，从而尽快地发现集成错误，让团队能够更快的开发内聚的软件。
以一个比较典型的大型IT项目为例。
首先，解释下集成。我们所有项目的代码都是托管在Git服务器上。每个项目都要有若干个单元测试，只有在本地电脑上通过了单元测试的代码才能上传到Git服务器上，保证上传的代码没有问题。代码进入流水线以后会经历一系列的预定过程，比如确认单元测试通过，代码静态检查和测试环境的部署等，然后会进入集成测试阶段。集成测试是指将多个软件组件连接后，通过自动化的手段检查组件直接的通信是否被破坏，因此这里的“集成”指就是指的集成测试。
再说持续。不言而喻，就是指长期的对项目代码进行集成测试。既然是长期，那肯定是自动执行的，否则，人工执行则没有保证，而且耗人力。对此，我们有一台服务器，它会检查Git仓库的代码变更，当代码仓库内容发生变化以后，它需要更新本地代码，重新编译，然后跑集成测试。每次集成测试结果都会记录在案。完成这方面工作的就是Jenkins软件，当然，它的功能远不止这些。

持续集成的特点：

- 它是一个自动化的周期性的集成测试过程，从检出代码、编译构建、运行测试、结果记录、测试统计等都是自动完成的，无需人工干预；
- 需要有专门的集成服务器来执行集成构建；
- 需要有代码托管工具支持；

持续集成的作用：

- 保证团队开发人员提交代码的质量，减轻了软件发布时的压力
- 持续集成中的任何一个环节都是自动完成的，无需太多的人工干预，有利于减少重复过程以节省时间、费用和工作量；

### Jenkins安装与配置

Jenkins是一个开源的实现持续集成的软件工具。它能实时监控集成中存在的错误，提供详细的日志文件和提醒功能，还能用图表的形式形象地展示项目构建的趋势和稳定性。

官方网站：http://jenkins.io 

Jenkins的特点：

- 易安装：仅仅一个 java -jar jenkins.war，从官网下载该文件后，直接运行，无需额外的安装，更无需安装数据库；
- 易配置：提供友好的GUI配置界面；
- 变更支持：Jenkins能从代码仓库（Svn/Git）中获取并产生代码更新列表并显示到流水线信息中；
- 支持永久链接：用户是通过web来访问Jenkins的，而这些web页面的链接地址都是永久链接地址，因此，你可以在各种文档中直接使用该链接；
- 集成E-Mail/RSS/IM：当完成一次集成完成时，可通过这些工具实时告诉你集成结果（集成测试通常需要花费一定时间，有了这个功能，就可以在等待结果过程中，干别的事情）；
- JUnit/TestNG测试报告：也就是用以图表等形式提供详细的测试报表功能；
- 支持分布式构建：Jenkins可以把集成构建等工作分发到多台计算机中完成；
- 文件指纹信息：Jenkins会保存哪次集成构建产生了哪些产物文件，哪一次集成测试的环境是哪个版本部署的等记录
- 支持第三方插件：使得Jenkins 变得越来越强大；

### 部署Jenkins服务

1. 安装环境介绍
- 系统：CentOS6.5_x64
- Jenkins：2.19.3，路径为 /opt/soft/jenkins
- JDK：jdk1.8.0_51，路径为 /opt/jdk1.8.0_51
- 机器 IP：10.70.71.235
2. 安装 Jenkins
- 新建 /opt/soft/jenkins 目录
- jenkins 官网下载 war 包
- 将 jenkins.war 拷贝到此目录下
- 初始化运行，执行以下命令，默认以管理员身份启动运行

    ```php
    cd /opt/soft/jenkins;
    export JAVA_HOME=/opt/jdk1.8.0_51;
    export PATH=${JAVA_HOME}/bin:$PATH;
    export JENKINS_HOME=/opt/soft/jenkins/workdir/;
    java -jar /opt/soft/jenkins/jenkins.war --httpPort=80
    //在浏览器里输入 10.70.71.235，可以进入 Jenkins
    ```
3. 配置 Jenkins 
- 从左侧面板的“系统管理”按钮进入配置管理页面
- 首先点击“管理插件”页面，在“高级”Tab页上传必要的插件，包括Git、Gitlab Auth、Role-Based Authorization等
- 然后进入“Configure Global Security”页面，勾选“启用安全”，安全域里选择Gitlab登录，并添加相关信息
- 在“Configure Global Security”页面的安全域“权限”中选择“Role-Based Strategy”，然后到“Manage and Assign Roles”页面配置项目角色和权限
- 域名映射
- 启动时，设置 --httpPort=80，再由网管指定域名 http://jenkins.haihangyun.com 映射即可
- 集成Gitlab登录后，用户登录时会跳转到Gitlab页面，此时可用海航域账号登录，完成后会跳转回到Jenkins首页。

### Jenkins发布流水线

部署流水线：
Jenkins提供很好的持续集成/交付的支持。
通常流水线的设计是从Gitlab获取代码更新开始的，对于单开发分支+单发布分支的项目，应该对应有两条甚至多条流水线。
一个比较完整的部署流程如下图：

![pipeline][1]

[1]: http://o8979n2hu.bkt.clouddn.com/pipeline.png

创建流水线:


1. 进入jenkins界面后，点击左上角新建按钮，出现如下界面
![jenkins-manager][2]

[2]: http://o8979n2hu.bkt.clouddn.com/jenkins-manager.png

2. 依据需要创建子目录或流水线
- 如果需要创建的是子目录，在界面中填写目录名称，并且选择下方的Folder选项
为同一项目搭建的流水线应该放置于同一目录下。
![jenkins-manager][3]

[3]: http://o8979n2hu.bkt.clouddn.com/jenkins-manager-project-name.png

点击保存后，在界面会出现新建的目录
![jenkins-manager][4]

[4]: http://o8979n2hu.bkt.clouddn.com/jenkins-manager-project-name-over.png

点击目录名称进入目录内，在左侧的按钮可以继续创建子目录和流水线。

如果创建的是流水线，填写流水线名称，并选择Pipeline选项,流水线的创建完成


### Jenkins配置流水线内容

打开新建流水线，点击左侧configure按钮。进入配置界面。具体有两种方式：

1. 直接填写在界面中,选择Pipeline script，如下图所示：
![jenkins-manager][5]

[5]: http://o8979n2hu.bkt.clouddn.com/Pipeline-scrip.png

2. 使用SCM,在Defintion部分选择  Pipeline script from SCM。在SCM类型中选择Git，填写Pipeline script所在代码仓库和分支以及脚本文件路径。
![jenkins-manager][6]

[6]: http://o8979n2hu.bkt.clouddn.com/Pipeline-script-from-SCM.png

3. 至此一条流水线搭建完成。

### 典型的流水线模板

对于不同类型的应用，所需的流水线会有差异。例如Web应用通常需要设计自动化部署的流程，而Mobile的应用则通常只能到达打包的步骤。
以聚宝滙产品为例。一个比较规范的Web项目流水线如下：

```php
withEnv(['PATH=/usr/local/bin:$PATH']) {
    node {
        stage('代码更新') {
            checkout scm: [$class: 'GitSCM', branches: [[name: '*/dev']],
                         userRemoteConfigs: [[url: 'git@gitbj.haihangyun.com:JBH/web.git']]]
        }
        stage('单元测试') {
            sh "phpunit"
        }
        stage('代码检查') {
            sh "sonar-scanner -Dsonar.login='jenkins' -Dsonar.password='jenkins' -Dsonar.host.url=http://sonar.haihangyun.com"
            sh "bash ./pipeline/checkSonarStatus.sh"
        }
    }
    stage('部署确认') {
        timeout(time:1, unit:'HOURS') {
            milestone()
            input "现在执行部署？"
        }
    }
    node {
        lock(resource: 'WEB_PAAS', inversePrecedence: true) {
            stage('部署PaaS云主站服务') {
                sh 'bash ./pipeline/buildImage.sh dev'
                sh 'bash ./pipeline/makesureImageReady.sh dev'
                sh "bash ./pipeline/deployImage.sh dev"
                sh "bash ./pipeline/waitDeployment.sh dev"
            }
            stage('部署PaaS云消息队列服务') {
                sh 'bash ./pipeline/buildImage.sh dev -queue'
                sh 'bash ./pipeline/makesureImageReady.sh dev -queue'
                sh "bash ./pipeline/deployImage.sh dev -queue"
                sh "bash ./pipeline/waitDeployment.sh dev -queue"
            }
            stage('接口测试') {
                withEnv(['SERVICE_NAME=api-app','BACKEND_ADDR="http://devopsgroup-jbh-api-dev.haihangyun.com"']) {
                    dir('api-test') {
                        git url: 'git@gitbj.haihangyun.com:JBH/api-doc.git'
                        sh 'npm run api-test'
                    }
                }
            }
            stage('界面测试') {
                withEnv(['PATH=/opt/jdk1.7.0_67/bin:/usr/local/bin:$PATH','JAVA_HOME=/opt/jdk1.7.0_67']) {
                    dir('e2e-test') {
                        git url: 'git@gitbj.haihangyun.com:JBH/e2e-test.git'
                        sh 'mvn clean test'
                    }
                }
            }
            milestone()
        }
    }
}
```

而Mobile的流水线内容看起来是这样的：

```php
withEnv(['PATH=/usr/local/bin:$PATH']) {
    node {
        stage('代码更新') {
            checkout scm: [$class: 'GitSCM', branches: [[name: '*/dev']],
                         userRemoteConfigs: [[url: 'git@gitbj.haihangyun.com:JBH/app-android.git']]]
        }
        dir('jbh') {
            stage('单元测试') {
                sh 'gradle clean test'
            }
            stage('代码检查') {
                sh "sonar-scanner -Dsonar.login='jenkins' -Dsonar.password='jenkins' -Dsonar.host.url=http://sonar.haihangyun.com"
                sh "bash ./pipeline/checkSonarStatus.sh"
            }
        }
    }
    stage('打包确认') {
        timeout(time:6, unit:'HOURS') {
            milestone()
            input "生成此版本的Apk包？"
        }
    }
    node {
        dir('jbh') {
            stage('生成Apk') {
                sh "gradle build"
                archiveArtifacts 'build/app.apk'
            }
        }
    }
}
```

### Jenkinsfile文件管理

依据Jenkins的惯例，流水线脚本文件通常命名为Jenkinsfile，与项目一起存放到版本仓库中。
在Jenkinsfile中可能还需要调用其他辅助脚本，我们可以将所有相关的文件放到一个统一的pipeline目录中。形成类似这样的结构：

```php
pipeline
├── Jenkinsfile.dev
├── Jenkinsfile.release
├── Jenkinsfile.prod
├── checkSonarStatus.sh
├── buildImage.sh
├── deployImage.sh
├── ... ...
```

### help

- Jenkins Step参考：https://jenkins.io/doc/pipeline/steps/
- 使用Jenkins Syntax
