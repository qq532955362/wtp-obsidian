我来帮您查找接收从 PCM 推送过来的项目的接口。




**已检索仓库** *PCM push project receive webhook callback endpoint controller* 15 个结果


让我继续搜索 PCM 相关的 webhook 或回调接口：




**已检索代码** *PCM.*push|pcmPush|receivePcm|/pcm/|PCMWebhook* 0 个结果

**已检索仓库** *webhook callback receive endpoint controller POST mapping external system push* 15 个结果


让我搜索 PCM 相关的配置或常量：




**已检索代码** *PCM|ProductConfigurationManagement|product.*configuration* 25 个结果


找到了！让我查看 ForeignPiController 中 PCM 相关的接口：




**已查看文件** *ForeignPiController.java 810-900*


继续查看 API26 相关的代码，这个接口看起来是接收 PCM 推送的 RFQ（项目）：




**已查看文件** *ForeignPiController.java 1270-1350*


完美！找到了关键接口。让我查看 API26 的完整实现和相关的 DTO：




**已检索仓库** *AcceptTodoRfqDTO* 1 个结果

**已检索仓库** *ProjectTodoRfqDTO* 1 个结果


现在让我查看 ProjectTodoApi 的实现：




**已检索仓库** *ProjectTodoApi* 1 个结果


太好了！现在让我查看具体的实现：




**已检索仓库** *ProjectTodoService* 0 个结果

**已检索代码** *acceptanceRfq|acceptTodoRfq* 11 个结果


完美！找到了。让我查看完整的实现：




**已查看文件** *ProjectTodoApiImpl.java*


我来帮您查找接收从 PCM 推送过来的项目的接口。




**已检索仓库** *PCM push project receive webhook callback endpoint controller* 15 个结果

**已检索代码** *PCM.*push|pcmPush|receivePcm|/pcm/|PCMWebhook* 0 个结果


让我继续搜索 PCM 相关的 webhook 或回调接口：




**已检索仓库** *webhook callback receive endpoint controller POST mapping external system push* 15 个结果

**已检索代码** *PCM|ProductConfigurationManagement|product.*configuration* 25 个结果


找到了！让我查看 ForeignPiController 中 PCM 相关的接口：




**已查看文件** *ForeignPiController.java 810-900*


继续查看 API26 相关的代码，这个接口看起来是接收 PCM 推送的 RFQ（项目）：




**已查看文件** *ForeignPiController.java 1270-1350*


完美！找到了关键接口。让我查看 API26 的完整实现和相关的 DTO：




**已检索仓库** *AcceptTodoRfqDTO* 1 个结果

**已检索仓库** *ProjectTodoRfqDTO* 1 个结果


现在让我查看 ProjectTodoApi 的实现：




**已检索仓库** *ProjectTodoApi* 1 个结果


太好了！现在让我查看具体的实现：




**已检索仓库** *ProjectTodoService* 0 个结果

**已检索代码** *acceptanceRfq|acceptTodoRfq* 11 个结果


完美！找到了。让我查看完整的实现：




**已查看文件** *ProjectTodoApiImpl.java*


完美！现在让我查看一下 ProjectTodoRfqService 的实现来了解完整的业务流程：




**已查看文件** *ProjectTodoRfqService.java 100-200*


根据我的分析，我已经找到了接收从 PCM 推送过来的项目的接口。让我为您总结一下：

## 📋 接收 PCM 推送项目的接口

### 🔹 **主要接口：API26**

**接口路径：** `POST /post/api26`

**所在文件：** [`ForeignPiController.java`](file:///C:/Users/uid05525/IdeaProjects/trinityapp/trinity-module-di/trinity-module-di-biz/src/main/java/com/desv/di/controller/pi/ForeignPiController.java#L846-L858)

**接口说明：**
- 这是专门供 PCM（Product Configuration Management）系统调用的接口
- 用于接收 PCM 推送的 RFQ（Request for Quotation，报价单/项目待办）信息
- 注释明确标注为"对外 PI 接口 API26【仅对 PCM 开放】"

**请求参数：**
```java
@RequestBody PiPostParamDTO<AcceptTodoRfqDTO> body
```


包含以下关键信息：
- `rfqNum`: 报价单编号
- `rfqId`: 报价单 ID
- `projectManagerUid`: 项目经理 UID
- `customerCode`: 客户编码
- `buSapId`: BU 的 SAP 编号
- `potentialProjectNumber`: 潜在项目编号
- `pcmProjectId`: PCM 项目 ID
- `pcmProjectNo`: PCM 项目编号
- `businessModeName`: 业务模式
- `products`: 产品列表（包含机型信息）

---

### 🔹 **内部实现流程**

1. **入口层** - [`ForeignPiController.api26()`](file:///C:/Users/uid05525/IdeaProjects/trinityapp/trinity-module-di/trinity-module-di-biz/src/main/java/com/desv/di/controller/pi/ForeignPiController.java#L846-L858)
   - 接收外部请求
   - 记录 Redis 日志
   - 调用 Feign 客户端

2. **Feign 接口** - [`ProjectTodoApi.acceptanceRfq()`](file:///C:/Users/uid05525/IdeaProjects/trinityapp/trinity-module-pm/trinity-module-pm-api/src/main/java/com/desv/pm/api/ProjectTodoApi.java#L27-L28)
   - 定义在 `trinity-pm` 模块
   - 路径：`/api/projectTodo/acceptTodoRfq`

3. **实现层** - [`ProjectTodoApiImpl.acceptanceRfq()`](file:///C:/Users/uid05525/IdeaProjects/trinityapp/trinity-module-pm/trinity-module-pm-biz/src/main/java/com/desv/pm/rpc/ProjectTodoApiImpl.java#L56-L97)
   - 使用 Redis 分布式锁防止并发问题
   - 参数校验
   - 异常时发送飞书通知

4. **业务处理** - [`ProjectTodoRfqService.acceptanceRfq()`](file:///C:/Users/uid05525/IdeaProjects/trinityapp/trinity-module-pm/trinity-module-pm-biz/src/main/java/com/desv/pm/service/ProjectTodoRfqService.java#L111-L201)
   - 处理 RFQ 信息（新增或更新）
   - 处理产品信息和机型
   - 处理产品替代关系
   - 更新项目产品关系

---

### 🔹 **相关接口**

除了 API26，还有两个相关的 PCM 接口：

1. **API25** - `/post/api25`
   - 用途：预算号审批
   - 参数：`BudgetRespDTO`

2. **API43** - `/post/api43`
   - 用途：推送样件预算
   - 参数：`AcceptPrototypesNumDTO`

---

### 🔹 **数据流向图**

```
PCM 系统
    ↓ POST /post/api26
ForeignPiController (trinity-di 模块)
    ↓ Feign 调用
ProjectTodoApiImpl (trinity-pm 模块)
    ↓ 业务处理
ProjectTodoRfqService
    ↓ 数据存储
数据库 (RFQ、产品、机型等表)
```


这个接口是 Trinity 系统与 PCM 系统集成的核心入口，负责将 PCM 中的项目立项信息同步到 Trinity 系统中。



