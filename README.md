# php-nginx-mysql-compose
docker-compose php + nginx + mysql
安装nginx+php, 并部署自己的blog，一个基于php的MiniBlog, BlogMi

## 安装docker

```
$ curl -sSL http://acs-public-mirror.oss-cn-hangzhou.aliyuncs.com/docker-engine/internet | sh -
$ service docker restart
```

参考[《docker入门——安装(CentOS)、镜像、容器》](https://www.jianshu.com/p/edba6551d256)

## 安装docker-compose

```
# 下载文件
sudo curl -L https://github.com/docker/compose/releases/download/1.19.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose

# 设置权限
sudo chmod +x /usr/local/bin/docker-compose

# 检测是否安装成功
$ docker-compose --version
docker-compose version 1.19.0, build 1719ceb
```

参考[《docker-compose搭建nginx+php+mysql》](https://www.jianshu.com/p/0561d3cfccda)

## git clone本工程

```
git clone https://github.com/lyj289/docker-compose-php-nginx-mysql.git demo
```
`demo`为用户自定义目录，可以是任意名称

## 启动容器

```
cd demo
docker-compose up -d
```
## 进入容器

```
docker exec -it compose-php /bin/bash
```

## PHP配置
php文件的目录，在nginx和php的volume需保持一致.
如果要增加一个解析php的目录，需要同时在nginx和php的volumes下增加相同项.
如：
```
volumes:
    - "$PWD/php:/usr/local/etc/php"
```
## Restart

> If there are existing containers for a service, and the service's configuration
> or image was changed after the container's creation, `docker-compose up` picks
> up the changes by stopping and recreating the containers (preserving mounted
> volumes).

After php.ini changed, run `docker-compose up -d`, it will not take effect,
I think the volume file change will not be picked up, while it is mean the `yml` configuration file.

So we can run below cmd, to reforce start the service

```
# Stop and remove containers, networks, images, and volumes
docker-compose down
# Create and start containers
docker-compose up -d

# Or
docker-compose restart php

# Or
docker-compose up -d --force-recreate

```

### re-build

更新php-mysqli下的dockerfile，移除下面语句，不然会有报错
```
apt-get install iputils-ping
```
最新为
```
RUN apt-get update \
	&& docker-php-ext-install mysqli && docker-php-ext-enable mysqli
```
此时，需要移除容器，重新build，否则不会生效
```
docker container rm
docker-compose build php
docker-compose up -d
```

### ini配置文件
目录为`/usr/local/etc/php`, 默认容器内包含两个ini文件，development和production版本，复制想使用的版本，更名为php.ini, 更改其中的配置项，重启容器生效

### fpm配置文件
/usr/local/etc/php-fpm.d

## 部署blog(php-BlogMi)
```
cp blog /var/www/html
```

## 查看Log
```
docker-compose logs -tf
```
