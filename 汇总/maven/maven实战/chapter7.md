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

## 生命周期详解

### 三套生命周期

1. clean：清理项目
2. default：构建项目
3. site：建立站点项目

### clean生命周期

目的是清理项目。

1. pre-clean
2. clean
3. post-clean

### default生命周期

定义了真正构建时所需要执行的所有步骤，是所有生命周期中最核心的部分。

- validate
- initialize
- compile
- test
- package
- install
- deploy

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

## 插件目标

maven核心仅仅定义了抽象的生命周期，具体任务交由插件完成，插件以独立的构件形式存在。

多个功能聚集在一个插件里，每个功能就是一个插件目标。

## 插件绑定

maven的生命周期和插件是互相绑定的，用以完成实际的构建任务。

### 内置绑定

略。

### 自定义绑定

用户能够自己选择将某个插件目标绑定到生命周期的某个阶段上。

	<build>
		<plugins>
			<plugin>
				<groupId></groupId>
				<artifactId></artifactId>
				<version></version>
				<executions>
					<execution>
						<id></id>
						<phase></phase>
						<goals>
							<goal></goal>
						</goals>
					</execution>
				</executions>
			</plugin>
		</plugins>
	</build>

多个插件目标绑定到同一个阶段时，这些插件的声明的先后顺序决定了目标的执行顺序。

## 插件配置

配置目标的参数，进一步调整插件目标所执行的任务。

### 命令行插件配置

使用-D参数来配置插件目标的参数。

	mvn install -Dmaven.test.skip=true

### POM中插件全局配置

略。

### POM中插件任务配置

略。

## 插件解析机制

### 插件仓库

略。