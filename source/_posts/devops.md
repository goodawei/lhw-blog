---
title: devops
date: 2016-12-04 10:49:07
tags: devops
categories: devops
---

![iterm2][1]

[1]: http://o8979n2hu.bkt.clouddn.com/test-pyramid.png

<blockquote class="blockquote-center">devops</blockquote>

----------

> 虽然不喜欢devops，但是还是要总结一下在devops中遇到一些所使用的工具。
https://github.com/integrations

----------

### git  

	http://blog.ffanfood.com/2016/07/09/git

### 代码托管和代码审查

	gitlab / github ／ Code Review

### CI 自动化构件，测试，部署

	CircleCI ／ Travis CI

### 日志聚集

	Slack  https://slack.com/

### 项目与事务跟踪

	JIRA https://github.com/integrations/jira-software

### 前端后端解藕（契约测试）

<!-- more -->

http://www.infoq.com/cn/articles/power-of-raml
	
工具与安装：

	1. 需要Node环境，下载nodejs：https://nodejs.org/en/ ，选择LTS版本
	2. RAML API设计器：npm install -g api-designer 网页版ide
	3. 对于前端（服务调用者）：npm install -g osprey-mock-service
	4. 对于后端（服务提供者）：npm install -g abao
	5. ide工具: Atom https://atom.io/，然后按照扩展api-workbench，类似phpstorm
	6. PostMan 需要安装cookie扩展

相关命令：

api-designer 网页版ide:

	1. 命令行输入api-designer启动，浏览器自动打开http://localhost:3000/

osprey-mock-service 前端：

	1. 命令行输入 osprey-mock-service --cors -f wallet-list.raml -p 6060
	2. 返回：Mock service running at http://localhost:6060 成功状态
	3. PostMan中输入地址调试，Header需添加Content-Type：application/x-www-form-urlencoded
	4. --cors 解决js跨域问题

abao 后端：

	1. 命令行输入abao raml-specs/wallet-list.raml -t 15000 -f login.js --server http://www.abc.com
	2. --server 就是baseUri， -t 是响应时间，
 

文档与规范：

	RAML 规范官网： raml.org
	RAML 详细规范： fhttps://github.com/raml-org/raml-spec/blob/master/versions/raml-08/raml-08.md
	RAML 示例：  http://static-anypoint-mulesoft-com.s3.amazonaws.com/API_examples_notebooks/raml-design3.html
	API 返回值规范：	http://json-schema.org/  


![rmal][2]

[2]: http://o8979n2hu.bkt.clouddn.com/raml.jpg


作用：

	1. 文档与规范 
	2. 自动化测试
	3. Mock服务模拟

工程目录参考 https://github.com/raml-apis



