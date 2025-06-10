# SES后端测试文档

更新时间：2025年6月11日
作者：Huangyijun

 

备注：

1.时间可以压缩模拟，例如24h等比例缩放到1h



2.如果需要查看部分日志信息，可以关闭自动轮询和缓存自动刷新，位于AutoSettingConfig。

否则记得开回来



3.可以调整log信息的打印级别。位于application.yml：

```cpp
logging:
  level:
    org:
      springframework:
        boot:
          autoconfigure=DEBUG:
    com:
      SES:
        mapper: debug
        service: info
        controller: info
```

可以改为`debug` 、`info` 或 `warn`



4.如有需要可一键清空数据库：（运行前在群里说）

```cpp
TRUNCATE TABLE device_log;
TRUNCATE TABLE alert_log;
TRUNCATE TABLE operation_log;

TRUNCATE TABLE policy_item;
TRUNCATE TABLE policy;

TRUNCATE TABLE batch_item;
TRUNCATE TABLE batch;

TRUNCATE TABLE device_mode;
TRUNCATE TABLE device;

TRUNCATE TABLE sim_device_mode;
TRUNCATE TABLE sim_device;
```



5.RabbitMQ调试相关

运行：

net start RabbitMQ



查看管理界面：

先启动插件：rabbitmq-plugins enable rabbitmq_management

然后打开：http://localhost:15672/

本地运行时，默认用户名：guest 密码：guest





计划测试的内容有：

**1.基础功能测试**

模拟用户不同使用流程，例如



新建许多设备



直接控制设备状态或模式



为一个设备编写多个策略与策略条目

给设备应用，撤销，切换策略



编写批量操作，应用批量操作





**2.系统内部功能测试**



设计策略并应用

观察能否获取设备实时信息

观察策略是否有效果

观察能否获得设备报表，内容是否符合预期

数据库查询日志，观察各字段是否正确





设计故障策略并应用

观察能否获取故障信息与警告报表





**3.编写测试报告并留档**

有bug反馈，没bug通过阶段验收