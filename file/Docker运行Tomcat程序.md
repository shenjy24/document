## Docker运行Tomcat程序

#### 1. 安装tomcat镜像并启动

（1）搜索tomcat镜像

```
docker search tomcat
```

（2）下载tomcat镜像

```
docker pull tomcat
```

（3）启动tomcat

```
docker run -d -p 8088:8080 tomcat
```

（4）浏览器打开`127.0.0.1:8088`，查看tomcat是否启动成功。



#### 2. 使用挂载目录启动war包

挂载就是在本地单独创建一个目录，然后映射到tomcat下的webapps目录，这样修改本地目录的配置文件，容器中的tomcat重启后配置就会生效。、

（1）在某个目录下创建YWHttpService目录。

（2）将YWHttpService.war放置到YWHttpService目录下。

（3）运行如下指令：

```
docker run -d -p 8080:8080 -p 15005:15005 -v E:/workspace/yw/hyt/YWHttpService/:/usr/local/tomcat/webapps --restart=always tomcat
```

- tomcat默认端口为8080

- 15005为远程调试端口的映射，需要在tomcat的catalina.sh添加如下配置：

  ```
  CATALINA_OPTS="-Xdebug -Xrunjdwp:transport=dt_socket,address=15005,suspend=n,server=y"
  ```

（4）通过postman等工具测试

```
http://127.0.0.1:8080/YWHttpService/yw/add_plugin_credit
```



#### 3. 问题积累

（1）Docker连接不上宿主机Mysql和Redis

- 在Docker内部执行 `ping host.docker.internal`可以获取到宿主机IP，程序内部的Mysql和Redis的IP配置需要配置为宿主机IP。



（2）Docker内部程序需要添加白名单

- 在Docker内部执行`ifconfig`，可以获取到Docker内部的IP地址，该地址格式一般为`*.*.*.2`，那么宿主机访问时的IP则为`*.*.*.1`，将改IP配置到白名单列表中即可。



