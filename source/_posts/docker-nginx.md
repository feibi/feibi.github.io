---
title: 利用 docker 启动 nginx
date: 2019-12-26
tags: 
- docker 
- nginx
categories: 
- docker
---


练习使用docker，记录下具体流程。

---

##### 拉取 nginx 镜像

```bash
# 选择更小体积的基础镜像
docker pull nginx:alpine
```

##### 第一次启动 nginx 容器

```
docker run -d --rm --name nginx nginx
```

`--rm`会在容器停止时删除，`--name nginx` 命名容器名为 nginx, `nginx`表示使用 nginx 镜像

##### 拷贝容器的 nginx 目录到宿主机

```bash
# $PWD 表示当前绝对路径
docker cp nginx:/etc/nginx/ $PWD/conf
```


> windows 下需要在管理员模式下进行，否则报`A required privilege is not held by the client.`权限错误

##### 停止 nginx 容器（自动删除）

```bash
docker stop nginx
```

##### 第二次启动 nginx 容器, 挂载配置文件，容器会使用宿主机的 nginx 配置

```bash
docker run -d  -p 80:80 --name nginx -v $PWD/conf:/etc/nginx -v D:/project/:/usr/local/web nginx
```

`-p 80:80`表示把本机的 80 端口映射到容器的 80 端口（local port:docker port），此时可以打开 localhost:80(或 localhost)看到 nginx 欢迎界面；

`-v $PWD/conf:/etc/nginx` 表示把容器的配置目录`/etc/nginx/nginx.conf` 映射到宿主机的 `$PWD/conf`目录

`-v D:/project/:/usr/local/web`表示把宿主机的`D:/project/`目录挂载到容器的`/usr/local/web`目录

##### 修改宿主机`conf/nginx.conf`配置

- 作为静态服务器（以单页面应用为例），将`D:/project/project1/dist`作为根目录

```nginx
http {
    ...
    server {
        # 对应-p 80:80 的冒号后面的'80'
        listen 80;
        # 访问域名
        server_name t.test.com;

        # 容器启动时根据-v D:/project/:/usr/local/web 映射
        root /usr/local/web/project1/dist;
        index index.html index.htm;
        location / {
            try_files $uri $uri/ index.html;
            index index.html;
        }
    }
    ...
}
```

- 若需要反向代理，可以增加下列配置

```nginx
# 代理容器的8000端口到宿主机的80端口
http {
    ...
    server {
        listen 80;
        server_name t1.test.com;
        location / {
            # 此ip为docker网关IP(可换成宿主机具体IP), 不能为localhost或127.0.0.1或0.0.0.0（会报502 Bad Gateway）, 因为映射的是docker里的ip,
            proxy_pass http://10.0.75.1:8000;
        }
    }
    ...
}

```

查看容器在宿主机的 ip:
windows: `ipconfig` 配合 `route print`
linux: `ip addr show docker0`

##### 重启 `conf/nginx.conf`

```bash
docker exec -it nginx service nginx reload
```
