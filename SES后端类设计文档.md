# SES后端类设计文档

**更新时间**：2025年6月15日
**作者**：Huangyijun

 

------

1.请求体中属性应该使用驼峰命名，这样与控制层参数名一致

2.对于可选参数，前端可以在请求体中仅传入部分参数，而Spring在接收这些请求时会自动将未传入的属性设为`null`

3.除了注册和登录的所有请求，都需要在请求头传入jwt令牌，字段名为token

## User类

1. **userController**

| 接口名称           | 方法 | 端点                             | 描述                             | 请求体                           | 响应（data部分）             |
| ------------------ | ---- | -------------------------------- | -------------------------------- | -------------------------------- | ---------------------------- |
| 用户注册           | POST | `/api/user`                      | 注册用户账号（只能注册普通账号） | `username``password`             |                              |
| 用户登录           | POST | `/api/user/login`                | 用户登录并获取 Token             | `username``password`             | `token:令牌``type：账户类型` |
| Token 刷新         | POST | `/api/user/refresh`              | 刷新过期的 Token                 | `oldToken`                       | `token`                      |
| 修改用户名         | PUT  | `/api/user/editUsername`         | 用户修改自己的用户名             | `newUsername`                    |                              |
| 修改密码           | PUT  | `/api/user/editPassword`         | 用户修改自己的密码               | `oldPassword``newPassword`       |                              |
| 根据账号id修改权限 | PUT  | `/api/user/{id}/type`            | （管理员）修改某个账号权限       | `type`                           |                              |
| 分页查询           | GET  | `/api/user/page`                 | （管理员）分页查询用户           | `page``pageSize`可选：`username` | (pageResult)                 |
| 启用或禁用账号     | POST | `/api/user/{id}/status/{status}` | （管理员）启用或禁用账号         |                                  |                              |

1. **userService**

按每个接口开发即可

1. **userMapper**

getByUsername

insert

pageQueny

update

getByID

按需编写

## Device类

设备管理类

1. **deviceController**

| 接口名称               | 方法 | 端点                    | 描述                                                         | 请求体                                          | 响应（data部分）           |
| ---------------------- | ---- | ----------------------- | ------------------------------------------------------------ | ----------------------------------------------- | -------------------------- |
| 新增设备               | POST | `/api/device`           | 新增设备（这里使用后端内置设备类型）                         | `name:设备名称``type:内置设备类型`              |                            |
| 删除设备               | DEL  | `/api/device/{id}`      | 删除设备                                                     |                                                 |                            |
| 分页查询               | GET  | `/api/device/page`      | 根据用户id分页查询设备（不包括设备模式）可以输入设备名称以筛选 | `page``pageSize`可选：`name：设备名称`          | (pageResult)               |
| 根据设备id查询模式信息 | GET  | `/api/device/{id}/mode` | 根据设备id查询设备的所有模式信息                             |                                                 | (list，包括各模式id与名称) |
| 修改设备名称           | PUT  | /api/device/{id}/name   | 修改设备名称                                                 | `name`                                          |                            |
| 修改设备策略           | POST | /api/device/{id}/policy | 修改设备应用的策略（isApplyPolicy=0时表示解绑策略，=1时表示应用policyId） | `isApplyPolicy``policyId`                       |                            |
| 控制设备运行状态       | POST | /api/device/{id}/status | 控制设备运行状态                                             | `status`                                        |                            |
| 控制设备模式           | POST | /api/device/{id}/mode   | 控制设备运行模式                                             | `modeId:该设备对应模式的id`                     |                            |
| 控制设备               | POST | /api/device/{id}        | 修改设备的策略、状态、模式（isApplyPolicy=null表示不对策略属性做任何操作） | 可选：`isApplyPolicy``policyId``status``modeId` |                            |

新增设备、分页查询等不需要传入用户id，这部分通过jwt提取

1. **deviceService**

按每个接口开发即可（系统流程详见设备模拟建议）



控制请求，要记录操作日志



DTO参考：

deviceDTO(name、type）

deviceControlDTO（`isApplyPolicy`、`policyId`、`status`、`modeId`）

1. **deviceMapper**

insert

pageQueny

update

getByID

按需编写

## 

## Policy类

策略管理类

1. **policyController**

| 接口名称           | 方法 | 端点                            | 描述                       | 请求体           | 响应（data部分）     |
| ------------------ | ---- | ------------------------------- | -------------------------- | ---------------- | -------------------- |
| 新增策略           | POST | `/api/policy`                   | 新增策略（不包括策略条目） | `name``deviceID` |                      |
| 删除策略           | DEL  | /api/policy/{id}                | 删除策略                   |                  |                      |
| 根据设备id查询策略 | GET  | `/api/policy/device/{deviceId}` | 根据设备id查询策略         |                  | (list，包括每个策略) |
| 修改策略名称       | PUT  | /api/policy/{id}/name           | 修改策略名称               | `name`           |                      |
| 根据设备id删除策略 | DEL  | `/api/policy/device/{deviceId}` | 删除设备的所有策略         |                  |                      |

1. **policyService**

按每个接口开发即可（系统流程详见设备模拟建议）

1. **policyMapper**

insert

pageQueny

update

getByID

按需编写



## **PolicyItem**类

1. **policyItem****Controller**

| 接口名称               | 方法 | 端点                                | 描述                   | 请求体                                 | 响应（data部分）         |
| ---------------------- | ---- | ----------------------------------- | ---------------------- | -------------------------------------- | ------------------------ |
| 新增策略条目           | POST | `/api/policyItem`                   | 新增策略条目           | `policyId``startTime``endTime``modeId` |                          |
| 删除策略条目           | DEL  | /api/policyItem/{id}                | 删除策略               |                                        |                          |
| 根据策略id查询策略条目 | GET  | `/api/policyItem/policy/{policyId}` | 根据策略id查询策略条目 |                                        | (list，包括每个策略条目) |
| 修改条目内容           | PUT  | /api/policyItem/{id}                | 修改条目内容           | 可选:`startTime``endTime``modeId`      |                          |
| 根据策略id删除策略条目 | DEL  | `/api/policyItem/policy/{policyId}` | 删除策略的所有策略条目 |                                        |                          |

注：策略条目的时间格式请用"00:36:18"（仅时分秒）

1. **policy****Item****Service**

按接口开发

1. **policyItem****Mapper**

insert

update

getByPolicyID

deleteById

按需编写



修正：我们现在的策略实际上是“日策略”，即其中时间点不超过24h，策略按日循环执行



理论上，还可以有周策略，即星期几应用什么日策略，按周循环执行

然后，可以给设备应用日策略或者周策略，策略监测模块自动判断并执行

或者从预订的角度考虑，也可能有“定时到x月x日”的需求

再者，有时设置策略未必是需要强制设备开关机，而只是设置“运行时策略”，即在这个时间段，我如果需要开启这台设备的话，它使用什么模式运行。

考虑到工期与先期设计的不足，以上更详细的策略设计暂时无法实现



## Batch类

批量操作类

1. **batchController**

| 接口名称         | 方法 | 端点                  | 描述                               | 请求体                                  | 响应（data部分） |
| ---------------- | ---- | --------------------- | ---------------------------------- | --------------------------------------- | ---------------- |
| 新增批量操作     | POST | `/api/batch`          | 新增批量操作（不包括条目）         | `name:批量操作名`                       |                  |
| 删除批量操作     | DEL  | /api/batch/{id}       | 删除批量操作                       |                                         |                  |
| 分页查询         | GET  | `/api/batch/page`     | 根据用户分页查询可以输入名称以筛选 | `page``pageSize`可选：`name:批量操作名` | (pageResult)     |
| 修改批量操作名称 | PUT  | /api/batch/{id}/name  | 修改批量操作名称                   | `name`                                  |                  |
| 应用批量操作     | POST | /api/batch/{id}/apply | 应用批量操作                       |                                         |                  |

新增批量操作、分页查询等不需要传入用户id，这部分通过jwt提取



1. **batchService**

按每个接口开发即可（系统流程详见设备模拟建议）

1. **batchMapper**

insert

pageQueny

update

getByID

按需编写



## **BatchItem**类

1. **batch****Item****Controller**

| 接口名称               | 方法 | 端点                             | 描述                   | 请求体                                                       | 响应（data部分）             |
| ---------------------- | ---- | -------------------------------- | ---------------------- | ------------------------------------------------------------ | ---------------------------- |
| 新增批量操作条目       | POST | `/api/batchItem`                 | 新增批量操作条目       | `batchId``deviceId`可选：`isApplyPolicy``policyId``status``modeId` |                              |
| 删除批量操作条目       | DEL  | /api/batchItem/{id}              | 删除批量操作条目       |                                                              |                              |
| 根据批量操作id查询条目 | GET  | `/api/batchItem/batch/{batchId}` | 根据批量操作id查询条目 |                                                              | (list，包括每个批量操作条目) |
| 修改条目内容           | PUT  | /api/batchItem/{id}              | 修改条目内容           | 可选:`deviceId``isApplyPolicy``policyId``status``modeId`     |                              |
| 根据批量操作id删除条目 | DEL  | `/api/batchItem/batch/{batchId}` | 删除批量操作的所有条目 |                                                              |                              |

1. **batch****Item****Service**

按接口开发

1. **batch****Item****Mapper**

insert

update

getByPolicyID

deleteById

按需编写





## DeviceData类

设备数据管理模块，负责整理数据，并以报表的形式发送（以避免发送完整日志）

1. **deviceDataController**

| 接口名称               | 方法 | 端点                             | 描述                                       | 请求体               | 响应（data部分）                                      |
| ---------------------- | ---- | -------------------------------- | ------------------------------------------ | -------------------- | ----------------------------------------------------- |
| 根据设备id查询设备状态 | GET  | /api/device/data                 | 根据设备id查询设备状态，可一次查询多个设备 | `idList`             | (list，每项为一个设备的运行状态、模式名、功率、策略） |
| 根据设备id查询设备报表 | POST | /api/device/{id}/deviceReport    | 根据设备id查询设备报表                     | `startTime``endTime` | (list，每项为简化的日志）                             |
| 根据设备id查询警报报表 | POST | /api/device/{id}/alertReport     | 根据设备id查询警报报表                     | `startTime``endTime` | (list，每项为简化的日志）                             |
| 根据设备id查询操作报表 | POST | /api/device/{id}/operationReport | 根据设备id查询操作报表                     | `startTime``endTime` | (list，每项为简化的日志）                             |

注：查询报表的时间格式请用"2025-06-11 00:36:18"

1. **deviceDataService**

查询设备当前状态：

返回DeviceDataVO列表

getDataByDeviceID(idList)

查询redis缓存，如果redis中没有，就说明没有实时数据

没有实时数据时，从设备日志表查询最新的日志，作为设备最后已知状态



DeviceDataVO：

（deviceId，status，modeName，power，policyName，isRealTime，lastUpdatedTime）

如果是实时数据，则isRealTime=1

如果不是实时数据，则isRealTime=0，然后提供lastUpdatedTime





生成设备报表：

提取日志的重要信息，以用于生成状态曲线、模式曲线、策略曲线、功率曲线、耗电量曲线

同时提供计算后的总耗电量（忽略异常数值）

getDeviceReportByDeviceID(id，startTime， endTime)





生成警报报表：

从警报日志中提取

getAlertReportByDeviceID(id，startTime， endTime)



关于警报日志：

当设备发生故障，只会记录第一次警报，后续不会持续记录

直到设备恢复，再记录level=-1（恢复正常）的警报日志







生成操作报表：

从操作日志中提取

getOperationReportByDeviceID(id，startTime， endTime)



注：耗电量计算忽略异常数值（例如大于3000W的功率）





------

以下是仅包括Service层的类：

## DeviceMonitor类

设备监测类，使用定时任务 + 异步处理+线程池进行轮询，使用Redis进行缓存+状态变更检测+异步批量落盘减少数据库操作

（流程详见设备模拟建议）



1. **deviceMonitorService**

定时调用设备查询api，得到DeviceQueryApiResultDTO（status，modeName，power）存入redis。

redis为每个设备维护一个deviceDataRedisDTO（deviceId，status，modeName，power，lastUpdatedTime）

其中status，modeName，power在每次轮询时修改。



根据前[设备轮询间隔]作为startTime，当前时间作为endtime，然后记录日志。（由日志模块计算用电量）





此外定期监控用电量，进行预警



关于redis储存设备数据：

在redis中，一个设备只能有一条信息（同key自动覆盖），储存时间戳，60秒后删除。

查询时判断时间戳，距今小于10秒则为实时数据



## PolicyMonitor类

负责跟踪设备策略，根据策略调用设备的控制api

为每个设备设置一个“时间队列”缓存，记录其策略中所有时间点

为设备再维护一个“任务线程”的线程池，使用工厂模式发布延时任务

当一个任务完成时，自动计算并刷新下一个任务



此外任务具有自动或强制刷新，每30分钟自动重新计算所有设备的任务。当策略的应用情况或策略内容改变时，使用消息队列通知相应设备刷新。



补充：测试时发现，在为设备应用策略时，应该立即触发一次强转。即如果当前设备状态与当前策略条目不同，也要进行设备控制。而不仅仅是在策略条目开始或结束进行控制。（考虑到时间不再修改了）



1. **policyMonitorService**

重新计算并发布单个设备的延时任务：

如有旧任务，先清除旧任务

refreshTaskByDeviceId（deviceId）



监听来自“策略的应用情况或策略内容改变”的rabbitMQ消息（内容为deviceId）：

调用PolicyTimePointCache的刷新并同步等待

为该设备重新计算并发布任务

onRefreshTaskByDeviceId（）



任务的执行逻辑：

查询该时间点对应的策略条目，得到任务内容（状态与模式名）

根据任务内容调用设备控制api





## Log类

负责所有日志的生成与储存，支持异步批量储存

1. **logService**

saveDeviceLog(DeviceLogSaveDTO)

区分传入字段与不常变字段

传入字段：

SaveDeviceLogDTO（device_id，startTime，endtime,，status，modeName，power）

（用电量由logService计算）



不常变字段：使用本地缓存

对每个设备id有：

LogCommonDTO

（user_id，user_username，device_name，policy_name，policy）

这个缓存同时被3种日志使用



自动更新缓存：5分钟

LOG_COMMON_REFRESH_INTERVAL = 5 * 60 * 1000;



强制更新缓存：使用消息队列

当修改username、device_name，修改策略（及其条目）、给设备挂载策略时，

强制更新对应字段的缓存





saveAlertLog(AlertLogSaveDTO)



操作日志暂不实现

saveOperationLog(OperationLogSaveDTO)



以下暂不实现（前端不应直接请求日志）

LogQueryDTO(deviceId，startTime， endTime)

queryDeviceLog(LogQueryDTO)

queryAlertLog(LogQueryDTO)

queryOperationLog(LogQueryDTO)



未来升级考虑：

1.接收消息队列而不是被直接调用

2.日志先缓存，再定时批量落盘



## **DeviceAPI类**

负责调用设备的api（这里为直接实现模拟api）

1. **deviceApiService**

初始化api：

为deviceId的设备填写sim_device表和sim_device_mode表

返回deviceInitApiResultDTO，包括该设备的默认模式名与模式名列表，用于填写device_mode

deviceInitApi（deviceId，type）





控制api：

为deviceId设备发布控制指令（实际为修改sim_device表）

deviceControlApi（deviceId，status，modeName）



查询api：

查询deviceId设备的当前状态（实际为查询sim_device和sim_device_mode表）

返回deviceQueryApiResultDTO（status，modeName，power）

deviceQueryApi（deviceId）



## AlertMonitor类

（该类暂不实现）

设备预警类，负责识别设备异常状态，并记录不同级别的警告日志

例如：

设备查询api长时间未响应->认为设备故障



为每个设备设置功率预警线和危险线

功率数据过线->认为设备功率过高



为每个设备设置用电量预警线，与检测周期（例如7天内用电量不超过xxkWh）

用电量过线->预警耗电太多





（现有的异常检测，是设备监控类检测到异常模式，直接调用警告日志储存）



------

以下是一些辅助类（位于服务层）：

## DeviceIdCache**类**

负责管理全设备id的缓存。

使用Caffeine进行本地缓存

自动刷新，或监听RabbitMQ消息队列以强制刷新

1. **deviceIdCacheService**

get（）



## LogCommonCache类

负责管理日志常用字段的缓存。

使用Caffeine进行本地缓存

自动刷新，或监听RabbitMQ消息队列以强制刷新

强制刷新包括用户名、设备名、策略3种，其中用户名、设备名可以直接修改缓存，不用查表

对于新建设备的情况，则查表以新建整个缓存项

1. **logCommonCacheService**

get（deviceId）



## PolicyTimePointCache类

负责管理各设备所应用策略的时间点列表。

各项同上

1. **policyTimePointService**

get（deviceId）



## AbstractCacheService类

抽象类，为所有缓存类提供了初始化以及三个级别的get接口

分别为：

1.直接从缓存获取

2.缓存没有则尝试刷新（一定次数）

3.尝试刷新，失败提供兜底数据



## PolicyTaskFactoryService类

工厂类，用于生成策略任务的运行实例





------

以下是仅包括mapper层的类：（每个表对应一个 Mapper 接口）

## DeviceM**ode**类

1. **deviceM****ode****Mapper**

getByNameAndDeviceID



## DeviceLog类

1. **deviceLogMapper**

按需编写



## AlertLog类

1. **alertLogMapper**

按需编写



## OperationLog类

1. **operationLogMapper**

按需编写



## SimDeviceM**ode**类

1. **SimDeviceM****ode****Mapper**

getByNameAndDeviceID



## SimDevice类

1. **SimDeviceMapper**

getByDeviceID



## 