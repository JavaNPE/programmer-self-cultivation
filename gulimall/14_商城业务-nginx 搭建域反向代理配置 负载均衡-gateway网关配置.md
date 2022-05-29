# 14_商城业务-nginx 搭建域|反向代理配置 |负载均衡-gateway网关配置

# P139、商城业务-nginx 搭建域名访问环境一(反向代理配置 )

## nginx+Windows搭建域名访问环境

让nginx帮我们进行反向代理，所有来自原gulimall.com的请求， 都转到商品服务。**nginx是基于服务端的负载均衡，gateway是基于客户端的负载均衡。**

![image-20220529150432517](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/image-20220529150432517.png)

# nginx正向代理和反向代理

![image-20220529150448896](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/image-20220529150448896.png)





### 修改hosts文件

Windows中hosts文件地址C:\Windows\System32\drivers\etc

通过软件SwitchHost添加一个本地Hosts类型的gulimall

![image-20220529150513538](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/image-20220529150513538.png)

内容：

```java
# gulimall
192.168.56.10 gulimall.com
```

启动虚拟机，并确保docker中的nginx和kibana容器已经启动

![image-20220529150525718](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/image-20220529150525718.png)

原来通过：[192.168.56.10:9200](http://192.168.56.10:9200/)访问kibana

![image-20220529150537262](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/image-20220529150537262.png)

现在通过：[gulimall.com:9200](http://gulimall.com:9200/)访问kibana

![image-20220529150548853](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/image-20220529150548853.png)

[http://gulimall.com:5601/app/kibana](http://gulimall.com:5601/app/kibana)

![image-20220529150559227](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/image-20220529150559227.png)

让nginx帮我们进行反向代理，所有来自原gulimall.com的请求， 都转到商品服务

## Nginx配置文件

![image-20220529150613539](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/image-20220529150613539.png)

![image-20220529150623372](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/image-20220529150623372.png)

cmd窗口中输入ipconf命令，然后回车，查看到我们的虚拟机跟Windows中间的网卡“以太网适配器 VirtualBox Host-Only Network”：IPv4 地址 . . . . . . . . . . . . : **192.168.56.103，或者通过nacos启动窗口查看**

![image-20220529150635066](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/image-20220529150635066.png)

### docker中重启nginx服务

修改nginx的配置文件，然后重启docker中的nginx服务

```bash
[root@10 /]# docker restart nginx 
nginx
[root@10 /]# docker ps
```

![image-20220529150649455](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/image-20220529150649455.png)

### dockers 中查看nginx容器的日志信息

```bash
[root@10 /]# docker logs nginx
```

修改好之后，[若是通过gulimall.com](http://xn--gulimall-yi5q101utv3ap2a.com/)显示404, 且没有其他异常的情况下，可以试着关闭Windows的防火墙试一试

![image-20220529150703614](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/image-20220529150703614.png)

# P140、商城业务-nginx-搭建域名访问环境二C (负 载均衡到网关)

## nginx官网文档地址

[nginx documentation](http://nginx.org/en/docs/)

## 修改nginx.conf上游服务器配置：

```bash
user  nginx;
worker_processes  1;

error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;

events {
    worker_connections  1024;
}

http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    #gzip  on;
    **upstream gulimall{
         server 192.168.56.103:88;
    }**
    include /etc/nginx/conf.d/*.conf;
}
```



网关的端口是88-》GulimallGatewayApplication :88

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/20220529150833.png)

## 修改gulimall.com配置文件

```bash
server {
    listen       80;
    server_name  gulimall.com;

    #charset koi8-r;
    #access_log  /var/log/nginx/log/host.access.log  main;

    **location / {
        proxy_pass  http://gulimall;
    }**

    #error_page  404              /404.html;

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

    # proxy the PHP scripts to Apache listening on 127.0.0.1:80
    #
 ...................................
    #location ~ /\.ht {
    #    deny  all;
    #}
}
```

![image-20220529150922485](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/image-20220529150922485.png)

## docker中重启nginx服务

```bash
[root@10 /]# docker restart nginx 
nginx
[root@10 /]# docker ps[root@10 /]# docker restart nginx 
nginx
[root@10 /]# docker ps
```

## 配置gulimall-gateway网关服务

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/20220529150943.png)

### SpringCloud Gateway官方文档

[Spring Cloud Gateway](https://docs.spring.io/spring-cloud-gateway/docs/2.2.9.RELEASE/reference/html/#the-host-route-predicate-factory)

**[5.6. The Host Route Predicate Factory](https://docs.spring.io/spring-cloud-gateway/docs/2.2.9.RELEASE/reference/html/#the-host-route-predicate-factory) 配置路由断言规则**

```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: host_route
        uri: https://example.org
        predicates:
        - Host=**.somehost.org,**.anotherhost.org
```

我们需要在gulimall-gateway微服务中的配置文件中，配置一下内容,，然后重启网关服务。

```yaml
# 只要是gulimall.com域名下的所有请求，我们都转给gulimall-product微服务，一定要放在最后面
- id: gulimall_host_route
  uri: lb://gulimall-product
  predicates:
    - Host=**.gulimall.com
```

流程：首先代理给nginx→gateway→gulimall-product(商品服务)

**【nginx坑】nginx代理给网关的时候，会丢失请求的host信息，需要我们在配置文件中手动配置host头信息 然后`docker restart nginx` 重启nginx服务。**

```yaml
server {
    listen       80;
    server_name  gulimall.com;

    #charset koi8-r;
    #access_log  /var/log/nginx/log/host.access.log  main;

    location / {
        **proxy_set_header Host  $host;**
        proxy_pass  http://gulimall;
    }

    #error_page  404              /404.html;

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

    # proxy the PHP scripts to Apache listening on 127.0.0.1:80
    #
    ...........................................
    #location ~ /\.ht {
    #    deny  all;
    #}
}
```

![image-20220529151017827](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/image-20220529151017827.png)

浏览器测试：[http://gulimall.com/](http://gulimall.com/) 成功实现反向代理

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/20220529151046.png)

### 域名映射效果

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/20220529151212.png)

### 汇总：nginx+windows搭建域名访问环境

![](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/20220529151321.png)