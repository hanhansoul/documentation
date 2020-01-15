# chapter3

	mvn archetype:create 
		-DgroupId=
		-DartifactId=
		-DpackageName=

	mvn install
## 简单的项目对象模型POM

## 核心概念	

### maven插件与目标

一个目标是一个明确的任务，可以作为单独的目标运行，或者作为一个大的构建的一部分和其他目标一起运行。

一个目标

一个maven插件是一个单个或者多个目标的集合。

- jar插件：组建jar文件的目标；
- Compiler插件：包含一组编译源代码和测试源代码的目标；
- Surefire插件：包含一组运行单元测试和生成测试报告的目标；
- Hibernate3插件：用来集成流行的持久化框架Hibernate插件

## maven生命周期

## maven坐标

## maven仓库

## maven依赖管理

maven支持传递性依赖。

## 站点生成和报告

	mvn site