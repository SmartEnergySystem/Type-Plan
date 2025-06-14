# SES后端部署文档

**更新时间**：2025年6月14日
**作者**：Huangyijun



以下为SES项目的参考部署流程。因为课程作业时间与环境的缘故，redis、rabbitMQ、项目本体没有租用服务器，而是采用本地运行。

如有需要，也可以修改配置文件，使得连接到外部redis、rabbitMQ服务器。



数据库租用的阿里云数据库，并已配置自动连接。



**运行流程：**

1. 安装redis、rabbitMQ
2. 确保数据库连接
3. 本地运行redis、rabbitMQ
4. IDEA运行SESApplication
5. （可选）使用http://localhost:8080/doc.html#/访问Swagger调试



这里使用的是本地运行+花生壳进行内网穿透的方式。并采用nginx进行反向代理

**远程部署流程：**

1. 安装nginx
2. 配置nginx，将/SES转发到http://localhost:8080/
3. 注册花生壳并配置映射，获得域名
4. IDEA运行SESApplication
5. 获得访问链接（参考）：https://109eo7ty57340.vicp.fun/SES/api/，后面与接口文档相同







nginx配置文件参考：

```cpp
server {
    listen       80;
    server_name  localhost;  

    location /SES/api/ {
        proxy_pass http://localhost:8080/api/;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }


}
```