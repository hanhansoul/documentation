# 容器

容器是镜像的一个运行实例，带有额外的可写文件层。

## 创建容器

- docker create：新建容器
- docker start：启动容器
- docker run：新建并启动容器：
- -d：让容器以守护态运行
- docker stop [-t|--time[=10]]：终止容器
- docker -a -q：查看处于终止状态的容器的ID信息
- docker restart：重新启动容器

## 进入容器

- docker attach：多个窗口使用attach命令进入同一个容器时，所有窗口都会同步显示，当某个窗口因命令阻塞时，其他窗口也无法操作了。
- docker exec：直接在容器内运行命令。
- nsenter工具

## 删除容器

- docker rm [OPTIONS] CONTAINER [CONTAINER...]
	- -f：强行终止并删除一个运行中的容器
	- -l：删除容器连接，保留容器
	- -v：删除容器挂在的数据卷

## 导入和导出容器

- docker export CONTAINER > file
- docker import CONTAINER：
