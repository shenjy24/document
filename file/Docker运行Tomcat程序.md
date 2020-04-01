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

