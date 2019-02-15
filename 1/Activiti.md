## 工作流基础

### BPM 业务流程管理

**工作流生命周期**

- 定义：手机业务需求并转化为流程定义。
- 发布：打包各种资源，然后在系统管理中发布流程定义。
- 执行：具体的流程引擎按照事先定义的流程处理路线以任务驱动的方式执行业务流程。
- 监控：收集每个任务的结果，根据结果做出相应处理。
- 优化：优化流程。

## Activiti特点

- 数据持久化：使用MyBatis。
- 引擎Service接口：提供七大Service接口，通过ProcessEngine获取，支持链式API风格。
- 流程设计器：Eclipse Designer / Activiti Modeler。
- 原生支持Spring：Spring集成，方便管理事务和解析表达式。
- 分离运行时与历史数据。

Activiti引擎的七大Service接口

- RepositoryService：流程仓库Service，用于管理流程仓库，例如部署、删除、读取流程资源等；
- IdentifyService：身份Service，用于管理和查询用户、组之间的关系；
- RuntimeService：运行时Service，可以处理所有正在运行状态的流程实例、任务等；
- TaskService：任务Service，用于管理、查询任务，例如签收、办理、指派等；
- FormService：表单Service，可以查询所有历史数据，例如流程实例、任务、活动、变量、附件等；
- ManagementService：引擎管理Service，和具体业务无关，可以查询引擎配置、数据库、作业等。

## Activiti构架与组件

Activiti架构

- Modeling
- Runtime
- Management

Activiti组件

- Activiti Engine：核心模块，解析、执行、创建、管理任务和流程实例、查询历史揭露并生成报表。
- Activiti Modeler：模型设计器。
- Activiti Designer：类似于Activiti Modeler，提供可视化设计功能。
- Activiti Explorer：用于管理仓库、用户、组，启动流程、任务办理。采用REST风格API。
- Activiti REST：提供RESTful风格的服务。