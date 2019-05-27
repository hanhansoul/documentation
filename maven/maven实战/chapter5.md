# 坐标和依赖

## 坐标

坐标包含的元素：
1. groupId：maven项目隶属的实际项目；
2. artifactId：实际项目中的一个maven项目，推荐使用实际项目名称作为artifactId的前缀；
3. version：maven项目当前所属的版本；
4. packaging：定义maven项目的打包方式，如jar或war等；
5. classifier：帮助定义构建输出的一些附属构件。

## 依赖范围

每个依赖包含的元素：
1. 依赖的基本坐标
2. type：依赖的类型，对应于项目坐标定义的packaging；
3. scope：依赖的范围；
4. optional：标记依赖是否可选；
5. exclusions：用来排除传递性依赖

依赖范围是用来控制依赖与三种classpath（编译classpath、测试classpath、运行classpath）的关系的。

maven包含的依赖范围：
1. compile：编译依赖范围
2. test：测试依赖范围，只对测试classpath有效。
3. provided：已提供依赖范围，只对编译和测试classpath有效，在运行时无效。
4. runtime：运行时依赖范围，对于测试和运行classpath有效，在编译主代码时无效。
5. system：系统依赖范围，与provided类似。
6. import：导入依赖范围。

## 传递性依赖

maven会解析各个直接依赖的POM，将那些必要的间接依赖，以传递性依赖的形式引入到当前的项目中。

### 传递性依赖与依赖范围

// TODO

## 依赖调解

对于依赖传递中不同版本的相同依赖，maven采用两个原则：
1. 路径最近者优先原则；
2. 第一声明者优化原则。

## 可选依赖

	<optional>

## 实践

### 排除依赖

`<exclusions>`元素声明可以包含一个或多个`<exclusion>`子元素，用于排除一个或多个传递性依赖。声明exclusion时只需要groupId和artifactId，不需要verison元素。

### 归类依赖

`<properties>`可以定义POM文件中属性值，同时用${}符号引用，如依赖的版本值等。

### 优化依赖

查看当前项目的已解析依赖：

	mvn dependency:list

查看当前项目的依赖树：

	mvn dependency:tree

分析当前项目的依赖关系：

	mvn dependency:analyze

