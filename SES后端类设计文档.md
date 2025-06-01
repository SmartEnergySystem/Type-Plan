# SES后端类设计文档

**更新时间**：2025年6月2日（未完成）
**作者**：Huangyijun

 

------

1.请求体中属性应该使用驼峰命名，这样与控制层参数名一致

2.对于可选参数，前端可以在请求体中仅传入部分参数，而Spring在接收这些请求时会自动将未传入的属性设为`null`

## User类

1. **userController**

| 接口名称       | 方法 | 端点                        | 描述                                                         | 请求体                                                | 响应（data部分）             |
| -------------- | ---- | --------------------------- | ------------------------------------------------------------ | ----------------------------------------------------- | ---------------------------- |
| 用户注册       | POST | `/api/auth`                 | 注册用户账号（普通或管理员）若注册管理员账号，需提供有效管理员id | `username``password`可选：`type：新账号类型``adminId` |                              |
| 用户登录       | POST | `/api/auth/login`           | 用户登录并获取 Token                                         | `username``password`                                  | `token:令牌``type：账户类型` |
| Token 刷新     | POST | `/api/auth/refresh`         | 刷新过期的 Token                                             | `oldToken`                                            | `token`                      |
| 修改密码       | PUT  | `/api/auth/editPassword`    | 修改密码                                                     | `id：用户id``oldPassword``newPassword`                |                              |
| 分页查询       | POST | `/api/auth/page`            | （管理员）分页查询用户                                       | `page``pageSize`                                      | (pageResult)                 |
| 启用或禁用账号 | POST | `/api/auth/status/{status}` | （管理员）启用或禁用账号                                     | `id`                                                  |                              |

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

| 接口名称               | 方法 | 端点                     | 描述                                                         | 请求体                                         | 响应（data部分）                          |
| ---------------------- | ---- | ------------------------ | ------------------------------------------------------------ | ---------------------------------------------- | ----------------------------------------- |
| 新增设备               | POST | `/api/device`            | 新增设备（这里使用后端内置设备类型）                         | `name:设备名称``type:内置设备类型``userId`     |                                           |
| 删除设备               | DEL  | `/api/device`            | 删除设备                                                     | `id:设备id`                                    |                                           |
| 分页查询               | POST | `/api/device/page`       | 根据用户id分页查询设备可以输入设备名称以筛选                 | `userId``page``pageSize`可选：`name：设备名称` | (pageResult)                              |
| 修改设备名称           | PUT  | /api/devices/{Id}/name   | 修改设备名称                                                 | `name`                                         |                                           |
| 修改设备策略           | POST | /api/devices/{Id}/policy | 修改设备应用的策略（isApplyPolicy=0时表示解绑策略，=1时表示应用policyId） | `isApplyPolicy``policyId`                      |                                           |
| 控制设备运行状态       | POST | /api/devices/{Id}/status | 控制设备运行状态                                             | `status`                                       |                                           |
| 控制设备模式           | POST | /api/devices/{Id}/mode   | 控制设备运行模式                                             | `mode`                                         |                                           |
| 控制设备               | POST | /api/devices/{Id}        | 修改设备的策略、状态、模式（isApplyPolicy=null表示不对策略属性做任何操作） | 可选：`isApplyPolicy``policy_id``status``mode` |                                           |
| 根据设备id查询设备状态 | GET  | /api/devices             | 根据设备id查询设备状态，可一次查询多个设备                   | `idList`                                       | (list，包括运行状态、模式名、功率、策略） |

1. **deviceService**

按每个接口开发即可（系统流程详见设备模拟建议）

1. **deviceMapper**

insert

pageQueny

update

getByID

按需编写

## 

------

以下是仅包括Service层的类：

## DeviceMonitor类

设备监测类，使用定时任务 + 异步处理+线程池进行轮询，使用Redis进行缓存+状态变更检测+异步批量落盘减少数据库操作

（系统流程详见设备模拟建议）



1. **deviceMonitorService**

@Scheduled(fixedRate = 5000)

​    	public void pollAllDevices()



@Async

   	public void pollDeviceAsync(Long deviceId)



private void saveDeviceStatusToDatabase(Long deviceId, DeviceStatusDTO)

等方法



## DeviceAPI类

模拟设备api

1. **deviceApiService**

初始化api

控制api

查询api





------

以下是仅包括mapper层的类：（每个表对应一个 Mapper 接口）

## DeviceM**ode**类

1. **deviceM****ode****Mapper**

getByNameAndDeviceID



## SimDeviceM**ode**类

1. **SimDeviceM****ode****Mapper**

getByNameAndDeviceID



## SimDevice类

1. **SimDeviceMapper**

getByDeviceID



## **PolicyItem**类

1. **policyItem****Mapper**

getByPolicyID