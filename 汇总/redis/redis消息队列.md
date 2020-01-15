# redis消息队列

消息队列实现：
- 基于List的 LPUSH+BRPOP 的实现
- PUB/SUB，订阅/发布模式
- 基于Sorted-Set的实现
- 基于Stream类型的实现