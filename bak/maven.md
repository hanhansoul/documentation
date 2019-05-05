## lifecycle phase

- validate：验证project是否正确，所有必要信息是否可用。
- compile：编译project的源代码。
- test：利用单元测试框架测试编译后的源码。
- package：打包编译后的代码。
- integration-test
- verify：运行检查package是否合法及满足质量标准。
- install：将package安装入本地仓库，作为其他project的本地依赖。
- deploy：将package拷贝如远程仓库。
- clean：清理之前的buil中创建的artifacts。
- site：为project创建site文档。


## 仓库

maven统一存储所有maven项目共享的构件位置就是仓库，maven项目不再各自存储其依赖文件，而只需要声明这些依赖的坐标，由maven自动根据坐标找到仓库中的构件。

### 分类

仓库分为两类：本地仓库与远程仓库。

#### 本地仓库

mvn install将项目的build输出文件安装到本地仓库中。

#### 远程仓库

## 生命周期与插件

- clean清理项目
- default构建项目
- site建立项目站点

每个生命周期包含一些有序的阶段，排列靠后的阶段依赖于排列靠前的阶段。

### clean

- pre-clean执行清理前需要完成的工作。
- clean清理上一次构建生成的文件。
- post-clean执行一些清理后需要完成的工作。

### default

- validate
- initialize
- generate-sources
- process-sources
- generate-resources
- process-resources
- compile
- process-classes
- generate-test-sources
- process-test-sources
- generate-test-sources
- test-compile
- process-test-classes
- test
- prepare-package
- package打包成可发布的格式
- pre-integration-test
- integration-test
- post-instegration
- verify
- install
- deploy

### site

- pre-site
- site
- post-site
- site-deploy