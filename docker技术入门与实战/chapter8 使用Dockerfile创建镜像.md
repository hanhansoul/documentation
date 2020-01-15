# 使用Dockerfile创建镜像

## 基本结构

Dockerfile分为四个部分：
1. 基础镜像信息
2. 维护者信息
3. 镜像操作指令
4. 容器启动时执行指令

	FROM ubuntu

	MAINTAINER docker_user docker_user@email.com

	RUN echo "deb http://archive.ubuntu.com/ubuntu/ raring main universe" >> /etc/apt/sources.list

	RUN apt-get update && apt-get install -y nginx

	RUN echo "\ndaemon off;" >> /etc/nginx/nginx.conf

	CMD /usr/sbin/nginx

	