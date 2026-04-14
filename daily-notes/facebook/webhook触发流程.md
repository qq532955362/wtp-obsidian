
### 第一阶段

`账号准备阶段`

- 创建facebook个人账号（也是开发者账号 此时是个人账号使用`facebook.com`登录 也可以使用个人账号登录meta开发者后台`developers.meta.com`）
- 使用个人账号创建facebook公共主页（个人账号成为公共主页管理员）
- 创建业务账号（`business.facebook.com`）相当于公司账号 这时候个人账号成为了管理员
- 将公共主页的添加到业务账号中
- 创建或者使用facebook登录instagram账号（此时instagram是**个人账号**）
- 将instagram转为**专业账号**
- 将该instagram**专业账号**绑定到facebook公共主页
- 可以使用业务账号授权给开发为管理员或者开发者（或者直接使用这个个人账号在meta开发者后台授权给开发为管理员或者开发者）

### 第二阶段

`开发者准备阶段`

- 开发获得meta开发后台的权限之后创建app
- 选择产品`webhook`
	- 选择对应的的需要订阅的内容
		- Page --> 公共主页相关
			- feed
			- messages
		- Instagram --> Instagram专业账户相关
			- comments
			- messages
- 选择产品`Messenger`
	- Instagram设置
		- 配置webhook
			- 选择对应的公共主页
			- 选择对应的订阅字段

### 第三阶段

`测试webhook回调`

- 本地可以使用内网穿透工具将http代理到本地
- 写好回调的接口
- meta开发者后台调用测试事件
	- facebook公共主页的消息
	- Instagram专业账号的消息



> [!NOTE] 注意
> 在mata开发者后台测试webhook回调发送事件的时候，如果回调事件没有发送需要将应用改成上线模式
> 




```mermaid
graph TD
    %% 定义样式
    classDef user fill:#e3f2fd,stroke:#1565c0,stroke-width:2px,color:#0d47a1;
    classDef biz fill:#e8f5e9,stroke:#2e7d32,stroke-width:2px,color:#1b5e20;
    classDef dev fill:#fff9c4,stroke:#fbc02d,stroke-width:2px,color:#f57f17;
    classDef action fill:#f3e5f5,stroke:#7b1fa2,stroke-width:2px,color:#4a148c;

    %% 第一阶段：账号准备
    subgraph Phase1 [第一阶段：账号准备与关联]
        direction TB
        P_User(👤 个人账号/FB登录):::user
        P_Page(📄 FB公共主页):::user
        P_Biz(🏢 商务管理平台):::biz
        P_InstaPro(📸 IG专业账号):::user
        
        P_User -->|1.创建并管理| P_Page
        P_User -->|2.创建并成为管理员| P_Biz
        P_Page -.->|3.添加到| P_Biz
        P_InstaPro -.->|4.绑定关联| P_Page
    end

    %% 第二阶段：开发者准备
    subgraph Phase2 [第二阶段：开发者配置]
        direction TB
        D_App(📱 Meta App):::dev
        D_Webhook(⚙️ Webhook产品):::dev
        D_Messenger(💬 Messenger产品):::dev
        
        P_Biz -.->|5.授权开发者| D_App
        D_App -->|6.添加产品| D_Webhook
        D_App -->|7.添加产品| D_Messenger
        
        %% Webhook 订阅详情
        subgraph Sub_Webhook [订阅字段配置]
            W_Page[Page: feed, messages]
            W_Insta[Instagram: comments, messages]
        end
        D_Webhook --> Sub_Webhook

        %% Messenger 配置详情
        subgraph Config_Mess [Instagram设置]
            M_Connect[配置Webhook -> 选择公共主页]
        end
        D_Messenger --> M_Connect
    end

    %% 第三阶段：测试回调
    subgraph Phase3 [第三阶段：测试与联调]
        direction TB
        T_Local(💻 本地服务器):::action
        T_Tool(🔌 内网穿透):::action
        T_Meta(🧪 开发者后台测试):::dev
        
        T_Local -->|8.暴露接口| T_Tool
        T_Tool -.->|9.回调URL| D_Webhook
        T_Meta -->|10.触发测试事件| D_Webhook
        D_Webhook -.->|11.推送消息| T_Tool
    end

    %% 跨阶段连接
    P_InstaPro -.- D_Webhook
    style Phase1 fill:#fafafa,stroke:#ccc,stroke-dasharray: 5 5
    style Phase2 fill:#fafafa,stroke:#ccc,stroke-dasharray: 5 5
    style Phase3 fill:#fafafa,stroke:#ccc,stroke-dasharray: 5 5
```
