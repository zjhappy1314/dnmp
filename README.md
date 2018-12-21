## 版本
```java
docker-lnmp
└── v1      -- 原始Nginx + PHHP-FPM
├── v2      -- Alpine Nginx + Tinywan/PHP7.2.3 + PHPRedis4.0
└── v3      -- Alpine Nginx + Tinywan/PHP7.2.3 + PHPRedis4.0 + MySQL5.7 + Reids3.2 Private
└── v4      -- Alpine Nginx + Tinywan/PHP7.2.3 + PHPRedis4.0 + MySQL5.7 Official + Reids5.0 Official
```
## 项目结构  
```java
docker-lnmp
└── vn
    ├── conf                    -- Nginx 配置目录
    │   ├── conf.d
    │   │   └── www.conf        -- Nginx 扩展配置文件
    │   ├── fastcgi.conf
    │   ├── fastcgi_params
    │   ├── mime.types
    │   └── nginx.conf          -- Nginx 主配置文件
    ├── docker-compose.yml      -- Docker Compose 配置文件
    ├── etc                     -- 公共配置目录
    │   ├── letsencrypt         -- Nginx 证书目录
    │   │   ├── ssl.crt
    │   │   └── ssl.key
    │   ├── php-fpm.conf        -- PHP-FPM 进程服务的配置文件
    │   ├── php-fpm.d
    │   │   └── www.conf        -- PHP-FPM 扩展配置文件
    │   ├── redis
    │   │   └── redis.conf      -- Redis 配置文件
    │   ├── mysql
    │   │   └── data            -- MySQL 数据存储目录
    │   │   └── my.cnf          -- MySQL 配置文件
    │   └── php.ini             -- PHP 运行核心配置文件
    ├── log                     -- Nginx 日志目录
    │   ├── tp5_access.log
    │   ├── tp5_error.log       -- 项目错误日志
    │   ├── access.log
    │   └── error.log           -- Nginx 系统错误日志
    └── www                     -- 项目代码目录
        └── tp5.1               -- 具体项目目录
            ├── application
            │   └── index
            ├── composer.json
            ├── composer.lock
            ├── config
            │   ├── app.php
            └── public
               └──index.php    -- 项目框架入口文件
```
## 部署
* 项目目录：`docker-lnmp\dev\nginx\v1`
* 启动所有容器 
    ```java
    $ docker-compose up -d
    $ docker-compose up -d
    Restarting lnmp-nginx-v3  ... done
    Restarting lnmp-php7.3-v3 ... done
    Restarting lnmp-mysql-v3  ... done
    Restarting lnmp-redis-v3  ... done
    ```
* 浏览器输入：`https://127.0.0.1:8088/index/index/index`
    > 支持frp反向代理 `http://docker-v1.frp.tinywan.top:8007/`
    > 支持Https `https://lnmp-v2.frps.tinywan.top/`
* 重启所有容器
    ```java
    $ docker-compose restart
    Restarting lnmp-nginx-v3  ... done
    Restarting lnmp-php7.3-v3 ... done
    Restarting lnmp-mysql-v3  ... done
    Restarting lnmp-redis-v3  ... done
    ```
* 停止所有容器
    ```java
    $ docker-compose stop
    Restarting lnmp-nginx-v3  ... done
    Restarting lnmp-php7.3-v3 ... done
    Restarting lnmp-mysql-v3  ... done
    Restarting lnmp-redis-v3  ... done
    ```
* 进入Docker 容器 `$ docker exec -it lnmp-php7.3-v3 bash`
    > Windows 环境`$ winpty docker exec -it lnmp-php7.3-v3 bash`

## 案例
#### 连接Redis
```php
<?php
    $redis = new \Redis();
    var_dump($redis);
    try {
        $redis->connect('lnmp-redis-v3', 63789);
        var_dump($redis->keys("*"));
    } catch (\Exception $e) {
        var_dump($e->getMessage())  ;
    }
```
> 注意：连接主机为`hostname: lnmp-redis-v3`

#### 连接MySQL
```php
<?php
    $servername = "lnmp-mysql-v3";
    $username = "root";
    $password = "123456";
    $dbname = "docker";
    $conn = mysqli_connect($servername, $username, $password, $dbname);
    if (!$conn) {
        die("连接失败: " . mysqli_connect_error());
    }

    $sql = "SELECT id,name FROM test";
    $result = mysqli_query($conn, $sql);

    if (mysqli_num_rows($result) > 0) {
        while ($row = mysqli_fetch_assoc($result)) {
            echo "id: " . $row["id"] . " - Name: " . $row["name"] . "<br>";
        }
    } else {
        echo "0 结果";
    }
    mysqli_close($conn);
```
> 注意：连接主机为`hostname: lnmp-mysql-v3`

## HELP
* [Dockerise your PHP application with Nginx and PHP7-FPM](http://geekyplatypus.com/dockerise-your-php-application-with-nginx-and-php7-fpm/)
* [docker-openresty](https://github.com/openresty/docker-openresty)

* 相比`nginx:latest`，`nginx:alpine`有几点优势：
    * 用的是最新版nginx镜像，功能与`nginx:latest`一模一样
    * alpine 镜像用的是[Alpine Linux](https://alpinelinux.org/)内核，比ubuntu内核要小很多。
    * `nginx:alpine` 默认支持http2。


* 连接Redis报错：`Connection refused`，其他客户端可以正常连接
  > 容器之间相互隔绝，在进行了端口映射之后，宿主机可以通过127.0.0.1:6379访问redis，但php容器不行。在php中可以直接使用`hostname: lnmp-mysql-v3` 来连接redis容器。[原贴地址](https://stackoverflow.com/questions/42360356/docker-redis-connection-refused/42361204)
* Windows 10 启动错误 `Error starting userland proxy: Bind for 127.0.0.1:3306: unexpected error Permission denied `  
  > 检查本地是否有MySQL已经启动或者端口被占用。关闭即可 
