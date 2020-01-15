## TestContext框架

框架包包含的类和接口：
1. TestContext：为测试提供执行的上下文
2. TestContextManager：TestContext的主入口点
3. TestExecutionListener
4. ContextLoader
5. SmartContextLoader

## Reflection TestUtils

TestUtils是一个基于反射机制的工具方法，用于设置非公共域和调用私有或继承setter方法。