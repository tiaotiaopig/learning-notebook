# 使用 nginx 进行静态资源的代理

## nginx 部署

### 使用docker 安装 nginx

> 1. `docker pull nginx`
>
>    拉取nginx最新的docker镜像
>
> 2. `docker run -d -p 80:80 --name nginx nginx`
>
>    创建nginx的docker容器，-d 后台运行，-p 80:80,端口映射，主机端口：容器端口

现在可以直接访问：http:192.168.50.17(ip,不加端口默认80)

出现欢迎界面，即为部署成功

### nginx 配置

我们需要在宿主机中配置存放nginx日志、静态资源、配置文件的目录，并将其挂载到容器的相应目录

相当于做了一层目录映射，不用每次进容器修改内容了，直接修改宿主机中的相应目录的内容，即可更换新容器内的资源

1. 下面在宿主机创建nginx目录

```bash
# 日志目录
mkdir -p /home/lifeng/Develop/frontend/nginx/log
# nginx配置文件目录
mkdir -p /home/lifeng/Develop/frontend/nginx/conf
# default.conf目录，配置代理
mkdir -p /home/lifeng/Develop/frontend/nginx/conf.d
# html、css、js、image等静态资源
mkdir -p /home/lifeng/Develop/frontend/nginx/static
# https配置
mkdir -p /home/lifeng/Develop/frontend/nginx/ssl
```

2. 从上面创建的容器中，将配置文件拷贝过来

```bash
# 配置文件
docker cp nginx:/etc/nginx/nginx.conf /home/lifeng/Develop/frontend/nginx/conf
# default.conf
docker cp nginx:/etc/nginx/conf.d/default.conf /home/lifeng/Develop/frontend/nginx/conf.d
```

3. 修改配置文件

   **conf/nginx.conf**

```nginx
user  nginx;
worker_processes  1;
error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;
worker_rlimit_nofile 65535;

events {
    use epoll;
    worker_connections 65535;
}

http {
    include /etc/nginx/mime.types;
    default_type application/octet-stream;
    charset utf-8;
    keepalive_timeout 60;
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
    access_log  /var/log/nginx/access.log  main;
    include /etc/nginx/conf.d/*.conf;
}
```

​	**conf.d/default.conf**

```nginx
server {
    listen       80;
    server_name  localhost;
    add_header Access-Control-Allow-Origin *;
    add_header Access-Control-Allow-Methods 'GET, POST, OPTIONS';
    add_header Access-Control-Allow-Headers 'DNT,X-Mx-ReqToken,Keep-Alive,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type,Authorization,lang,access-token,satoken';
    if ($request_method = 'OPTIONS') {
        return 204;
    }

    location / {
        root   /usr/share/nginx/html;
        index  login.html login.htm;
    }
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }
}
```

4. 停止上次容器并删除

   ```dockerfile
   docker stop nginx
   docker rm nginx
   ```

5. 重新启动一个nginx容器，并进行目录映射

   ```bash
   docker run -p 6688:80 --name nginx-csa -v /home/lifeng/Develop/frontend/nginx/static:/usr/share/nginx/html -v /home/lifeng/Develop/frontend/nginx/conf/nginx.conf:/etc/nginx/nginx.conf -v /home/lifeng/Develop/frontend/nginx/logs:/var/log/nginx -v /home/lifeng/Develop/frontend/nginx/conf.d:/etc/nginx/conf.d -d nginx
   ```

6. 附录

   每次修改nginx资源时，我们需要重启nginx

   ```bash
   # 进入容器
   docker exec -it nginx-csa bash
   # 在nginx-csa容器执行
   nginx -s reload
   ```

