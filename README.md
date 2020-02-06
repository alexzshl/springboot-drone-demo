# springboot-drone-demo
Maven 项目使用 Drone 实现 CI/CD

## 搭建 drone/drone-runner
todo

## OAuth授权
todo
[drone 官方文档](https://readme.drone.io/server/provider/github/)

## 挂载 Docker maven  到宿主
todo
## 使用 Aliyun Maven镜像地址
todo

```
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0
                      https://maven.apache.org/xsd/settings-1.0.0.xsd">
  <localRepository>/root/.m2/repository</localRepository>
  <mirrors>
    <mirror>
        <!--This sends everything else to /public -->
        <id>aliyun-nexus</id>
        <mirrorOf>*</mirrorOf>
        <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
    </mirror>
    <mirror>
        <!--This is used to direct the public snapshots repo in the
                         profile below over to a different nexus group -->
        <id>aliyun-nexus-public-snapshots</id>
        <mirrorOf>public-snapshots</mirrorOf>
        <url>http://maven.aliyun.com/nexus/content/repositories/snapshots/</url>
      </mirror>
    </mirrors>
</settings>
```

## 问题 or 坑
* Bitbucket OAuth 授权会获取所有 Team 的项目，[尴尬]
* Drone 的文档真实烂，参数都不写清楚，为了项目跑起来，直接Docker XXX inspect 调试配置。。。 


