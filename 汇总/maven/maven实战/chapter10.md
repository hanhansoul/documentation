# 使用maven进行测试

## 动态指定要测试的用例

maven-surefire-plugin提供了一个test参数让用户能够在命令行指定要运行的测试用例。

test参数的值时测试用例的类名。

## 包含与排除测试用例

- <includes>
- <excludes>

## 测试报告

### 测试覆盖率报告

测试覆盖率是衡量项目代码质量的一个重要参考指标。

maven通过cobertura-maven-plugin集成Cobertura测试覆盖统计工具。

	mvn cobertura:cobertura

## 重用测试代码

maven默认的打包行为不会包含测试代码。

通过配置maven-jar-plugin可以将测试类打包。