# 生命周期与插件

maven的生命周期就是对所有构建过程进行抽象和统一。

生命周期包括：
1. 清理
2. 初始化
3. 编译
4. 测试
5. 打包
6. 集成测试
7. 验证
8. 部署
9. 站点生成

maven中，实际的任务交由插件来完成。每个构建步骤可以绑定一个或多个插件行为，而maven为大多数构建步骤编写并绑定了默认插件。

## 三套生命周期

1. clean：清理项目
2. default：构建项目
3. site：建立站点项目

### clean生命周期

1. pre-clean
2. clean
3. post-clean

### default生命周期

### site生命周期

建立和发布项目站点。

1. pre-site
2. site
3. post-site
4. site-deploy

### 命令行与生命周期


- mvn clean：调用clean生命周期的clean阶段；
- mvn test：调用defualt生命周期的test阶段；
- mvn clean install：调用clean生命周期的clean阶段和default生命周期的install阶段；
- mvn clean deploy site-deploy：调用clean生命周期的clean阶段、default生命周期的deploy阶段、以及site生命周期的site-deploy阶段。


## 命令行调用插件

	mvn [options] [<goal>] [<phase>]