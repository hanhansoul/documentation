# 镜像

- 如何使用pull命令从Docker Hub仓库下载镜像到本地；
- 如何查看本地已有的镜像信息；
- 如何在远端仓库使用search命令进行搜索和过滤；
- 如何删除镜像标签和镜像文件；
- 如何创建用户定制的镜像并且保存为外部文件。

## 获取镜像

	docker pull NAME[:TAG]

	// 在仓库名称前指定完整的仓库注册服务器地址
	docker pull ADDRESS:PORT/NAME[:TAG]

## 查看镜像信息

	// 列出本地主机上已有的镜像
	docker images

	// 添加新的标签
	docker tag

	// 获取镜像的详细信息
	docker inspect

## 搜寻镜像

	// 搜索远端仓库中共享的镜像
	docker search

## 删除镜像

	// 删除镜像，IMAGE可以是标签或ID
	docker rmi IMAGE


	docker rmo -f IMAGE

	// 查看本地存在的所有容器
	docker ps -a

	// 删除容器
	docker rm CONTAINER

## 创建镜像

1. 基于已有镜像的容器创建
2. 基于本地模板导入
3. 基于Dockerfile创建

### 基于已有镜像的容器创建

	// 
	docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]

### 基于本地模板导入

使用OpenVZ模板。

## 存出和载入镜像

### 存出镜像
	
	// 存出镜像到本地文件
	docker save

### 载入镜像

	//从本地文件中导入到本地镜像库
	docker load

	docker load --input ubuntu_14.04.tar

	docker load < ubuntu_14.04.tar

## 上传镜像

	//将镜像上传到仓库
	docker push NAME[:TAG]