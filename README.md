# springboot-drone-demo
Maven 项目使用 Drone 实现 CI/CD

## docker版本
1. drone:1.6.5
2. drone-runner-docker:1

## OAuth授权
1. 登录 https://github.com/settings/applications/new
2. 根据[drone 官方文档](https://readme.drone.io/server/provider/github/)操作
3. 建立成功获得 DRONE_GITHUB_CLIENT_ID，DRONE_GITHUB_CLIENT_SECRET

## 搭建 drone/drone-runner, docker-compose 集成
替换【】里面的内容
```
version: '3'

services:
  drone-server:
    image: drone/drone:1.6.5
    ports:
      - 10080:80
    volumes:
      - /var/lib/drone:/var/lib/drone/
    restart: always
    environment:
      - DRONE_GITHUB_CLIENT_ID=【注意：替换成上一节中的DRONE_GITHUB_CLIENT_ID】
      - DRONE_GITHUB_CLIENT_SECRET=【注意：替换成上一节中的DRONE_GITHUB_CLIENT_SECRET】
      - DRONE_RPC_SECRET=【openssl rand -hex 16 生成】
      - DRONE_SERVER_HOST=drone.dev.huirongke.com:10080
      - DRONE_SERVER_PROTO=http
      - DRONE_WEBHOOK_SKIP_VERIFY=false
      - DRONE_LOGS_DEBUG=true
      - DRONE_USER_CREATE=username:【Git 仓库当前授权的账号名】,admin:true

  drone-runner:
    image: drone/drone-runner-docker:1
    restart: always
    depends_on:
      - drone-server
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    environment:
      - DRONE_RPC_HOST=drone-server
      - DRONE_RPC_PROTO=http
      - DRONE_RPC_SECRET=【同上值 DRONE_RPC_SECRET】
      - DRONE_RUNNER_CAPACITY=2
      - DRONE_RUNNER_NAME=${HOSTNAME}
      - DRONE_LOGS_DEBUG=true
```

## 挂载 Docker maven  到宿主
1. 启用 Drone Trusted: SETTINGS-Project settings-Trusted, 必须在 Drone 配置`DRONE_USER_CREATE=username:【Git 仓库当前授权的账号名】,admin:true`，才有该菜单。

2. pipeline 的.drone.yml配置
```
steps:
 - name: build
    image: maven
    volumes:
      - name: cache # The Volume's name
        path: /root/.m2 # The path in the container
    commands:
      #- mvn help:effective-settings # check maven
      - mvn clean package -DskipTests
volumes:
  - name: cache # The name use in this pipeline,
    host:
      path: /var/lib/cache # The path be used in the host.
```


## 使用 Aliyun Maven镜像地址
在宿主机进入`/var/lib/cache`,新建settings.xml文件
```
<settings xmlns="http://maven.apache.org/SETTINGS/1.1.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.1.0 http://maven.apache.org/xsd/settings-1.1.0.xsd">
  <localRepository>/Users/zhangdx/.m2/repository</localRepository>
  <mirrors>
    <mirror>
      <id>alimaven</id>
      <name>aliyun</name>
      <mirrorOf>central</mirrorOf>
      <url>http://maven.aliyun.com/nexus/content/groups/public</url>
    </mirror>
  </mirrors>
  <pluginGroups>
    <pluginGroup>org.apache.maven.plugins</pluginGroup>
    <pluginGroup>org.codehaus.mojo</pluginGroup>
  </pluginGroups>
</settings>
```

## 启动检查Drone

## 问题 or 坑
* Bitbucket OAuth 授权会获取所有 Team 的项目，[尴尬]公司项目用的Bitbucket。
* Drone 的文档真实烂，参数都不写清楚，为了项目跑起来，直接Docker XXX inspect 调试配置。。。 


