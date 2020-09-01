## 常用指令

#### `docker run`

- `-d`：后台运行
- `-p`：`<host port>:<container port>` 端口映射
- `-v`：`<host path>:<container path>` 文件挂载，宿主路径必须为绝对路径
- `--name`：指定容器名称



## Dockerfile编写

### 1.组成部分

执行`build`命令时，指令从上到下依次执行，`Dockerfile`基本组成部分如下：

| 主要部分           | 代表性命令                                                   |
| ------------------ | ------------------------------------------------------------ |
| 基础镜像信息       | FROM                                                         |
| 维护者信息         | MAINTAINER                                                   |
| 镜像操作指令       | RUN, COPY, ADD, EXPOSE, WORKDIR, ONBUILD, USER, VOLUME,  ENV等 |
| 容器启动时执行指令 | CMD, ENTRYPOINT                                              |

### 2.命令详解

#### （1）RUN

构建镜像时执行的



