# Sun-Proxy
一个简单的反向代理程序, 为 [sun-panel](https://github.com/hslr-s/sun-panel) 插件打造

DockerHub地址：https://hub.docker.com/r/hslr/sun-proxy

## TODO

- [x] 自动填入目标站的basic信息
- [x] 给代理站增加basic验证
- [x] 代理目录
- [x] 启动的命令行可以指定配置文件
- [ ] 在不停止运行（或者停止服务的情况下）的时候自动更新配置文件
- [ ] 加入（非basic）网页认证
- [ ] 在没有cert证书文件的时候启动https服务或者不启动https服务单独启动http服务
- [ ] 缓存服务
- [ ] 网址内容替换


## 目前已经实现4种模式：

域名 => 域名
calendar.cn:8080 =>      http://192.168.3.100

路径 => 域名
127.0.0.1:8080/istore => http://127.0.0.1:8080

域名 => 路径
127.0.0.1:8080 => http://127.0.0.1:8080/abc

路径 => 路径
127.0.0.1:8080/sun-panel-tool-page => http://localhost:1003/sun-panel-tool-page


## 命令说明

```
  -c string
        config file path (default "./sun-proxy.yml") 运行时指定配置路径
  -ec
        generate sample configuration file 生成示例配置文件
```

执行示例：

```sh
./sun-proxy -c ./sun-proxy.yml
```

## 配置文件

一个完整的示例 `./sun-proxy.yml`
```yml
name: Sun-Proxy
port:
  http: 8080
  https: 8081
rules:
  - domain: "example.com" # 域名（不可以带端口）
    path: "/" # 地址 /example/other/path
    target_url: "http://192.168.1.100/" # 目标地址 eg: http://example.sun.sun | http://example.sun.sun/path
    cert: # 证书地址
      key:
        ./cert/privkey.key
      pem:
        ./cert/fullchain.pem
    auth: # 需要验证信息 (Basic Auth)
      username:
        admin
      password:
        123456
    target_basic_auth: # 目标验证信息（自动输入）
      username:
        admin
      password:
        123456
```

## docker 运行

|参数|示例|说明|
|---|---|---|
|-v|-v /root/sun-proxy.yml:/app/sun-proxy.yml|[必须]挂载配置文件（要使用绝对路径）|
|-v|-v ~/docker_data/sun-proxy/cert:/app/cert | SSL证书目录
|-p|-p 8080:8080|http端口|
|-p|-p 8081:8081|https端口|


运行：
```
docker run -d \
-p 8080:8080 \
-p 8081:8081 \
-v /root/sun-proxy.yml:/app/sun-proxy.yml \
-v ~/docker_data/sun-proxy/cert:/app/cert \
--name sun-proxy \
hslr/sun-proxy
```


构建：
```
docker build -t hslr/sun-proxy .
```

## docker compose运行
查看[docker-compose.yml](docker-compose.yml)

```sh
# start
./docker-compose up -d

# close
./docker-compose down
```