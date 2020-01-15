#  仓库

## Docker Hub

- docker login：登录
- docker search：查找官方仓库中的镜像
- docker pull：从仓库下载镜像到本地

### 自动创建

用户通过Docker Hub指定跟踪一个目标网站上的项目，一旦项目发现新的提交，则自动执行创建。

## 创建和使用私有仓库

### 使用registry镜像创建私有仓库

	// 自动下载并启动一个eregistry容器，创建本地的私有仓库服务。
	docker run -d -p 5000:5000 registry

