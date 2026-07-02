---
type: knowledge
tags: [项目, SpringBoot3, Vue3, 企业级后台, 策略工厂, RBAC, 多模块Maven, NaiveUI]
article_id: OBA-2026060303
created_at: 2026/06/03
updated_at: 2026/06/03
---

# Flora Admin — 企业级后台管理系统

> 基于 Spring Boot 3 + Vue 3 的企业级后台管理系统。功能覆盖：RBAC 权限、系统监控、日志管理、消息中心、文件管理（多存储后端）、代码生成器、定时任务、多种登录方式、RSA+AES 接口加密。

## 技术栈

| 层 | 技术 |
|---|---|
| 后端 | Java 17 / Spring Boot 3.2.2 / MyBatis-Plus 3.5.5 / Sa-Token 1.37.0 |
| 数据库 | MySQL 8.0+ / Druid / Redis 7.0+ |
| 定时任务 | Quartz 2.3.2 |
| 前端 | Vue 3 + TypeScript / Vite 5 / Naive UI 2.37.3 / Pinia / ECharts |
| 移动端 | UniApp + uView Plus |
| 安全 | RSA + AES 混合加密传输 |

## 六层 Maven 模块化架构

依赖方向严格单向，flora-core 不直接依赖 flora-infra：

```
flora-starter          — 启动入口，聚合所有模块
├── flora-api          — 接口层（Controller / WebSocket / 拦截器）
│   ├── flora-admin-api    — 后台管理接口（~30 个 Controller）
│   ├── flora-app-api      — App/小程序接口
│   └── flora-web-api      — PC 前台接口
├── flora-core         — 业务核心层
│   ├── flora-system       — 系统管理（用户/角色/菜单/部门/岗位/字典/配置/日志）
│   ├── flora-auth         — 认证授权（策略工厂模式）
│   ├── flora-file         — 文件管理
│   ├── flora-gen          — 代码生成
│   ├── flora-message      — 消息中心（公告/私聊/群聊）
│   └── flora-biz          — 自定义业务扩展模块
├── flora-infra        — 基础设施层
│   ├── flora-db           — MyBatis-Plus + 数据权限拦截器
│   ├── flora-redis        — Redis 配置
│   ├── flora-oss          — 文件存储策略（Local/MinIO/AliyunOSS/TencentCOS/RustFS）
│   ├── flora-sms          — 短信策略（Console/阿里云/腾讯云/七牛）
│   ├── flora-push         — 推送策略（钉钉/飞书/企业微信/Webhook）
│   ├── flora-pay          — 支付策略（支付宝/微信）
│   ├── flora-social       — 社交登录策略
│   ├── flora-crypto       — RSA+AES 加解密
│   └── flora-websocket    — WebSocket 容器配置
├── flora-common       — 公共基础（BaseEntity / Result / 异常体系 / 工具类）
└── flora-job          — 定时任务（Quartz 封装）
```

**关键设计**：每个 infra 子模块独立 pom.xml，可按需裁剪。flora-biz 是预留的业务扩展模块，不与核心耦合。

## 核心设计模式

### 1. 策略 + 工厂模式（贯穿全项目的核心范式）

这是该项目最突出的架构模式，在 **6 个子系统**中统一使用：

| 领域 | 接口 | 工厂 | 实现 |
|---|---|---|---|
| 登录 | `LoginStrategy` | `LoginStrategyFactory` | 密码/短信/小程序/社交 |
| 文件存储 | `FileStorage` | `FileStorageFactory` | Local/MinIO/AliyunOSS/TencentCOS/RustFS |
| 短信 | `SmsService` | `SmsServiceFactory` | Console/阿里云/腾讯云/七牛 |
| 推送 | `PushService` | `PushServiceFactory` | 钉钉/飞书/企业微信/Webhook |
| 支付 | `PayService` | `PayServiceFactory` | 支付宝/微信 |
| 社交登录 | `SocialLoginService` | `SocialLoginFactory` | 支付宝/苹果/微信公众号/微信小程序 |

**统一注册模式**：
1. 通过 Spring `List<T>` 注入所有策略实现
2. `@PostConstruct` 时期注册到 `Map<String, T>`
3. 运行时按配置/参数动态选取

**扩展方式**：新增厂商只需 `实现接口 → 加 @Component → 自动注册到工厂`，无需修改已有代码，符合 [[开闭原则]]。

部分工厂（`FileStorageFactory`、`PushServiceFactory`）额外实现了**双重检查锁的实例缓存**和 `refresh()` 方法。

### 2. 数据库驱动的动态配置中心

`SystemConfigHelper` 是整个系统最核心的配置抽象：

- 所有运行时可变配置存储在 `sys_config_group` 表（JSON 格式，按分组编码）
- 提供类型安全的 getter 方法
- 实现 `CryptoConfigProvider` 和 `MailConfigProvider` 接口
- 被所有基础设施工厂类依赖
- **配置变更无需重启，即时生效**

**通用价值**：运维人员通过管理后台直接调整系统行为，无需修改配置文件。

### 3. AOP 切面三件套

| 注解 | 切面 | 功能 |
|---|---|---|
| `@Log` | `LogAspect` | 操作日志（请求参数/响应/异常/耗时） |
| `@RepeatSubmit` | `RepeatSubmitAspect` | 防重复提交 |
| `@DataScope` | `DataPermissionInterceptor` | 数据权限过滤（5 级） |

### 4. 数据权限拦截器

基于 MyBatis-Plus `InnerInterceptor`，SQL 执行前动态追加 WHERE 条件。支持 5 级数据权限：

1. 全部数据
2. 自定义部门数据
3. 本部门数据
4. 本部门及下级数据
5. 仅本人数据

admin 角色自动跳过。通过反射动态获取 Mapper 解决跨模块依赖。

### 5. RSA + AES 混合加密传输

```
前端启动 → 预加载公钥 + AES 密钥
登录请求 → RSA 加密密码字段
API 响应 → 自动检测加密 → AES-GCM 解密
```

支持两种模式：全局加密 / 部分加密（`@EncryptResponse` 注解标注）。前后端透明，业务代码无感知。

### 6. 动态路由 + 权限注解的双层权限控制

**前端**：后端返回菜单树 → `addDynamicRoutes()` 动态注册 Vue Router 路由 → 页面级权限
**后端**：Sa-Token `@SaCheckPermission("system:user:add")` → 方法级权限校验

双层防护确保前后端权限一致。参见 [[RBAC]]

### 7. WebSocket 消息系统

- 后端：`MessageWebSocketHandler` 管理 `ConcurrentHashMap<Long, WebSocketSession>`
- 前端：`WebSocketManager` 单例（自动重连 5 次 / 心跳 30s / 事件分发 on/off/send）
- 消息 Store 监听 4 种事件：notice / chat / groupChat / unread

### 8. 演示模式安全兜底

`DemoModeInterceptor` 通过配置开关控制，演示环境下全局禁止写操作（POST/PUT/DELETE），白名单放行登录/注册/聊天。

## 前端架构

- **状态管理**：5 个 Pinia Store（user / message / site / theme / tabs）
- **布局**：三种侧边栏位置 + 两种主题 + 可配置主题色
- **组件**：消息通知浮窗（未读计数 + 无限滚动）、全局水印、Web SSH 终端（xterm.js）
- **部署**：Vite 构建产物直接嵌入 Spring Boot JAR（`resources/static`），单 JAR 部署

## 相关链接

- 设计模式：[[策略模式]], [[工厂模式]], [[RBAC]], [[AOP]], [[模板方法模式]], [[责任链模式]]
- 技术栈：[[SpringBoot]], [[MyBatisPlus]], [[Sa-Token]], [[Quartz]], [[NaiveUI]]
- 原始代码：`raw/i/flora-admin/`
