

| 接口名称                                   | Controller全限定类名                                                                                            |
| -------------------------------------- | ---------------------------------------------------------------------------------------------------------- |
| findItemOptionVosMapBySamePlatformType | com.desv.options.controller.field.AlmEnumItemController<br>com.desv.options.rpc.AlmEnumItemFeignController |
| getProjectsByParams                    | com.desv.pm.controller.alm.ProjectController                                                               |
| getUserOptionsByRole                   | **未找到该接口**（可能不存在或名称不同）                                                                                     |
| getRolesByCurrentUser                  | com.desv.pm.controller.alm.ProjectController                                                               |
| queryBudgetNumber                      | com.desv.pm.controller.project.ProjectApproveController                                                    |
| queryLibraryBu                         | com.desv.options.controller.field.AlmEnumController                                                        |
| listWorkflowTransitions                | **Service层方法**<br>com.desv.pm.service.project.ProjectsService<br>（无直接Controller暴露）                         |
| getAllCustomerDataVo                   | com.desv.pm.controller.project.ProjectConfigController                                                     |
| listApproveLog                         | com.desv.pm.controller.project.ProjectApproveController                                                    |
| getPpLeaderList                        | com.desv.options.controller.member.ProjectRoleLeaderConfigController                                       |
| projPrototypeBudgetStat                | com.desv.pm.controller.tp.WorkItemTpController                                                             |
| getProjectProductDetail                | com.desv.pm.controller.alm.ProjectController                                                               |
| listCustomerPartNoRel                  | com.desv.pm.controller.project.ProjectProductController                                                    |
|                                        |                                                                                                            |
| /post/api26                            | PCM推送RFQ报价单的接口                                                                                             |

**说明：**

1. **findItemOptionVosMapBySamePlatformType**: 在两个Controller中都有定义（AlmEnumItemController 和 AlmEnumItemFeignController）

2. **getUserOptionsByRole**: 经过全面搜索，未在代码库中找到此接口的定义，可能是：
   - 接口名称有误
   - 该接口已被移除或重命名
   - 该接口在其他模块中

3. **listWorkflowTransitions**: 这是一个Service层的方法，在`ProjectsService`中实现，但没有直接通过Controller暴露为REST API接口

4. 其他接口都已找到对应的Controller位置
