---
title: Jmeter 生成 html 性能测试报告
date: 2019-04-26 15:50:21
comments: true
toc: true
categories:
	- 软件测试
---

* 在使用 Jmeter 图形用户界面执行脚本进行压测和负载时，生成的聚和报告不够全面直观，于是，在 Jmeter 3.0 和之后的版本，开始支持通过命令行生成 html 格式的报告... 
   
   <!--more-->

* 正常情况下，执行结果的后缀是 jtl 格式，通过命令行 只需要将 jtl 转为 html 即可。不过，在通过 jtl 转 html 文件时，要求路径指定的文件夹内必须为空。那么，如何操作可以一条命令多次执行，测试结果自动备份，这里整理了一种方案。


## 目录结构

如下

![](/uploads/201904/jmeter_mulu.png)

## bat文件

如下

```
set thread=2
set sustain=60
set filepath=C:\git\daohang-api
set now=%DATE:~0,4%%DATE:~5,2%%DATE:~8,2%%time:~0,2%%time:~3,2%%time:~6,2%
jmeter -n -t %filepath%\performance.jmx -J thread=%thread% -J sustain=%sustain% -l %filepath%\WebReport\jtl\%now%_%thread%_%sustain%.jtl -e -o %filepath%\WebReport\htmlRecord\%now%_%thread%_%sustain%
rd /s /q %filepath%\WebReport\html
jmeter -g %filepath%\WebReport\jtl\%now%_%thread%_%sustain%.jtl -e -o %filepath%\WebReport\html
```

### 命令行传参

设置四个参数，thread 表示在 1 s 内启动 2 个线程数，sustain 表示持续执行 60s，filepath 根据本地 clone 目录手动调整

```
set thread=1
set sustain=30
set filepath=C:\git\daohang-api
set now=%DATE:~0,4%%DATE:~5,2%%DATE:~8,2%%time:~0,2%%time:~3,2%%time:~6,2%
```

### 生成报告，备份

Jmeter 命令行执行 Jmx 文件，生成 jtl 文件并转为 html 报告

```
jmeter -n -t %filepath%\performance.jmx -J thread=%thread% -J sustain=%sustain% -l %filepath%\WebReport\jtl\%now%_%thread%_%sustain%.jtl -e -o %filepath%\WebReport\htmlRecord\%now%_%thread%_%sustain%
```

### 保留最新的 html 报告

在 /WebReport/html 目录生成一个最新的 html 报告，若文件中存在内容，删除它

```
rd /s /q %filepath%\WebReport\html
jmeter -g %filepath%\WebReport\jtl\%now%_%thread%_%sustain%.jtl -e -o %filepath%\WebReport\html
```

## 脚本文件

如下
![](/uploads/201904/jmeter_jiaoben.png)

### 全局变量说明

测试计划中的，${\_\_P(thread)} 和 ${\_\_P(sustain)}，是两个全局变量，其变量的值为批处理文件中定义好的值，在调整并发量和持续时间时，不再需要修改测试计划