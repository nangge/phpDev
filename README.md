#### phpDev 通过docker compose编排的php7.2开发环境；使用简便，部署快捷，并且可以有效的统一团队开发环境。
比如当有新的扩展加入，或者其他工具加入时，只需要将新扩展变更到源文件，其他开发者拉取最新代码重新编译就可以保持环境一致。

## 启动：

```
> docker-compose up --build
#docker-compose up -d //守护进程
#docker-compose up -d --build //重新编译启动
```
首次编译可能需要较长时间，编译启动后通过浏览器访问localhost 或者 宿主机ip就可以看到php环境信息。
## 环境支持
- php-fpm7.2
- mysql5.6
- nginx
- composer

php已安装扩展
```
zmq、swoole、redis、pdo_mysql、mysqli、gd、zip
```

## 环境目录结构



```
│  .env     //docker环境配置文件
│  docker-compose.yml       //compose环境编排文件
├─app   //项目目录
│      50x.html
│      index.html
│      index.php //测试文件
├─data      //数据文件 主要用来数据卷挂载
│  ├─log    //nginx 日志文件;网站访问日志文件
│  │      nginx_error.log   //nginx error日志文件
│  ├─mysql  //mysql 数据文件
│  │  │  auto.cnf
│  │  │  ibdata1
│  │  │  ib_logfile0
│  │  │  ib_logfile1
│  │  ├─mysql //mysql 数据库
│  │  └─performance_schema 
│  └─redis //redis数据
│          dump.rdb
├─mysql  //mysql Dockerfile文件 包含基础镜像
│      Dockerfile
├─nginx     //nginx Dockerfile文件
│  │  Dockerfile
│  │  enable-php.conf
│  │  nginx.conf //nginx配置文件
│  └─conf.d //虚拟主机配置目录，如果需要监听其他端口，
            //则需要在docker-compose.yml 中的nginx下开放映射端口，比如 10000:10000
│          default.conf //默认配置文件
│          managez.com.conf //虚拟主机实例文件
├─phpfpm //nginx Dockerfile文件
│  │  Dockerfile //包含基础镜像 以及一些自定义指令 （php扩展）
│  └─conf
│          php.ini
└─redis //nginx Dockerfile文件
    │  Dockerfile
    └─conf
            redis.conf 
```

## 常用命令
- 查看本地镜像

```
> docker image ls
```

- 查看container

```
> docker container ls

CONTAINER ID        IMAGE                COMMAND                  CREATED              STATUS              PORTS                    NAMES
2b8d75cfc874        nginx_server    "nginx -g 'daemon of…"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp       nginx_server
```

- 进入指定container

```
> docker exec -it < container id/names > bash

#docker exec -it 2b8d75cfc874 bash
#docker exec -it nginx_server bash

#退出 Ctrl+D 或 exit
```

- 查看指定container信息,返回json数据格式

```
> docker inspect < container id/names >

#docker inspect 2b8d75cfc874
#docker inspect nginx_server
```

- 查看所有的 数据卷
```
> docker volume ls
DRIVER              VOLUME NAME
local               340445d47584c0795bc2133ef879afb1d19efaf8f1da31231df69834b24b04a3
local               3591dfe064a5a813ce23007814fa8fbdb7af881f8fd10fa280f294380cb6beaf
local               9813907674b919eb51fc560ff5e605ca7f3dce4092d58abfda5efa7e664b1f55
```

- 查看指定 数据卷 的信息

```
> docker volume inspect [my-vol]    
# docker volume inspect 340445d47584c0795bc2133ef879afb1d19efaf8f1da31231df69834b24b04a3
```


- 删除指定数据卷

```
> docker volume rm [VOLUME NAME]
```
- 新建并启动一个container

```
> docker run [OPTIONS]  [image]
#docker run -p 8080:8080 -p 50000:50000 nginx_server
```

- 启动/重启已存在的容器

```
> docker container start/restart [container name\id]
```

-  终止运行中的容器

```
> docker stop [container name\id]
> docker stop $(docker ps -a -q) //  stop停止所有容器
```

-  删除已停止的容器

```
> docker rm [container name\id]
> docker rm $(docker ps -a -q) //  删除停止所有容器
```

# 常见问题


- Docker Toolbox Windows - Invalid volume specification

```
原因：绑定数据卷时，无法识别windows 路径
解决方法：
cat >.env
COMPOSE_CONVERT_WINDOWS_PATHS=1
```

- Cannot create container for service XXX: b'Drive has not been shared'

```
原因：windows for docker 下没有向docker容器共享磁盘
解决方法：
windows for docker -> setting -> Shared Drives
选择盘符确定时会需要账户密码，如果登陆用户没有， 先设置密码
```