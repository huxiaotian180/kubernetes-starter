# 综述

  **必读：本项目是专门针对慕课网的在线课程[《Docker + Kubernetes微服务容器化实践》][5]中的kubernetes实战部分使用的，主要讲解了kubernetes在绿色网络环境下的集群搭建及集群的使用、常用命令、应用的部署。首先剥离了认证授权和服务发现模块，从最核心的模块开始构建集群，然后逐步增加认证授权和服务发现部分，在搭建过程中逐步熟悉kubernetes。最后在集群上部署我们前面开发的所有微服务模块。如果没有结合视频教程请忽略第四部分，敬请谅解！**

## [一、预先准备环境][1]
## [二、基础集群部署 - kubernetes-simple][2]
## [三、完整集群部署 - kubernetes-with-ca][3]
## [四、在kubernetes上部署我们的微服务][4]








[1]: https://github.com/liuyi01/kubernetes-starter/tree/master/docs/1-pre.md
[2]: https://github.com/liuyi01/kubernetes-starter/tree/master/docs/2-kubernetes-simple.md
[3]: https://github.com/liuyi01/kubernetes-starter/tree/master/docs/3-kubernetes-with-ca.md
[4]: https://github.com/liuyi01/kubernetes-starter/tree/master/docs/4-microservice-deploy.md
[5]: https://coding.imooc.com/class/198.html





## 五 GitLab安装、使用教程（Docker版）

### 一、下载镜像

官方版本是：gitlab/gitlab-ce:latest，为了提升速度我们这里使用阿里云的仓库

```
$ docker pull registry.cn-hangzhou.aliyuncs.com/imooc/gitlab-ce:latest
```

### 二、运行GitLab容器

使用docker命令运行容器，注意修改hostname为自己喜欢的名字，-v部分挂载目录要修改为自己的目录。
端口映射这里使用的都是安全端口，如果大家的环境没有端口限制或冲突可以使用与容器同端口，如：-p 443:443 -p 80:80 -p 22:22

#### 1. 生成启动文件 - start.sh

```
$ cat <<EOF > start.sh
#!/bin/bash
HOST_NAME=gitlab.mooc.com
GITLAB_DIR=`pwd`
docker stop gitlab
docker rm gitlab
docker run -d \\
    --hostname \${HOST_NAME} \\
    -p 8443:443 -p 8080:80 -p 2222:22 \\
    --name gitlab \\
    -v \${GITLAB_DIR}/config:/etc/gitlab \\
    -v \${GITLAB_DIR}/logs:/var/log/gitlab \\
    -v \${GITLAB_DIR}/data:/var/opt/gitlab \\
    registry.cn-hangzhou.aliyuncs.com/imooc/gitlab-ce:latest
EOF
```

#### 2. 运行start.sh 启动gitlab

```
$ sh start.sh
```

#### 3. 配置环境

- 修改host文件，使域名可以正常解析

> 127.0.0.1 gitlab.xiaothu.com

- 修改ssh端口（如果主机端口使用的不是22端口）

> 修改文件：${GITLAB_DIR}/config/gitlab.rb 找到这一行：# gitlab_rails['gitlab_shell_ssh_port'] = 22 把22修改为你的宿主机端口（这里是2222）。然后将注释去掉。

- 重新启动容器

```
$ sh start.sh
```

### 三、GitLab试用

#### 1. 打开首页

地址：http://gitlab.xiaothu.com:8080/

#### 2. 设置管理员密码

首先根据提示输入管理员密码，这个密码是管理员用户的密码。对应的用户名是root，用于以管理员身份登录Gitlab。