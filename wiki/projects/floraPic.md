---
type: knowledge
tags: [项目, SpringBoot, Vue3, DDD, MyBatisPlus, ShardingSphere, WebSocket, Disruptor, 云图库]
article_id: OBA-2026060302
created_at: 2026/06/03
updated_at: 2026/06/03
---

# FloraPic — 云图库管理平台

> 功能丰富的云图库平台，同时提供**标准分层架构**和 **DDD 架构**两种后端实现。核心功能：图片上传/编辑/审核、空间管理、以图搜图、颜色搜索、AI 扩图、WebSocket 协作编辑、数据分析面板、VIP 会员体系。

## 技术栈

| 层      | 技术                                                             |
| ------ | -------------------------------------------------------------- |
| 后端     | Java 8 / Spring Boot 2.7.6 / MyBatis-Plus 3.5.9                |
| 缓存     | Redis + Caffeine（二级缓存）                                         |
| 权限     | Sa-Token 1.39.0                                                |
| 分库分表   | ShardingSphere 5.2.0                                           |
| 高性能队列  | LMAX Disruptor 3.4.2                                           |
| 对象存储   | 腾讯云 COS                                                        |
| 前端     | Vue 3 + TypeScript / Vite 6 / Ant Design Vue / Pinia / ECharts |
| API 契约 | @umijs/openapi（从 OpenAPI 规范自动生成 TS 客户端）                        |

## 双架构并行

### 标准分层架构

`Controller → Service → Mapper → Database`，Manager 层处理跨切面关注点。

```
controller/    — 6 个控制器
service/       — 业务接口 + impl
manager/       — 跨切面：权限、分表、上传模板、WebSocket+Disruptor
mapper/        — MyBatis Mapper
model/         — dto / entity / enums / vo
```

### DDD 领域驱动架构

严格四层 + 限界上下文：

```
interfaces/        — 接口层（Controller + Assembler + DTO + VO）
application/       — 应用层（薄代理，编排领域服务）
domain/            — 领域层（实体含业务方法 + 仓储接口 + 领域服务）
infrastructure/    — 基础设施层（仓储实现 + 外部 API + 配置）
shared/            — 共享层（跨领域的权限、分表、WebSocket）
```

**DDD 关键差异**：
- `Picture` 实体自身包含 `validPicture()` 业务方法
- 应用层是薄代理，委托给领域服务
- 仓储通过接口解耦：`PictureRepository`（领域层）→ `PictureRepositoryImpl extends ServiceImpl`（基础设施层）
- Assembler 负责 DTO ↔ Entity 转换

参见 [[DDD]]

## 核心设计模式

### 1. 模板方法 + 策略混合模式（图片上传）

`PictureUploadTemplate` 定义上传骨架算法（6 步），3 个抽象方法由子类实现：

| 子类 | 策略 |
|---|---|
| `FilePictureUpload` | 处理 MultipartFile |
| `UrlPictureUpload` | 处理 URL 下载 |

调用端通过 `instanceof` 选择策略。这是**模板方法内嵌策略**的混合模式 — 骨架固定，细节可替换。

### 2. 二级缓存防雪崩

```
查询顺序：Caffeine(进程内) → Redis(分布式) → Database
写回顺序：同时写入 Redis + Caffeine
防雪崩：Redis TTL = 300 + Random(0,300) 秒随机偏移
缓存 Key：查询条件 MD5 哈希
```

- L1 Caffeine：最大 10000 条，5 分钟过期
- L2 Redis：5-10 分钟随机 TTL

### 3. 门面模式（以图搜图）

`ImageSearchApiFacade` 封装三个子 API 的编排：
1. `GetImageFirstUrlApi` — 获取搜索首页 URL
2. `GetImagePageUrlApi` — 获取详情页 URL
3. `GetImageListApi` — 解析图片列表

### 4. AOP + 自定义注解（声明式权限控制）

**双轨制权限校验**：

| 层级 | 注解 | 实现 |
|---|---|---|
| 系统级 | `@AuthCheck(mustRole="admin")` | AOP 切面 `AuthInterceptor` |
| 空间级 | `@SaSpaceCheckPermission("picture:upload")` | Sa-Token + 自定义 `StpInterfaceImpl` |

`StpInterfaceImpl` 从 HTTP 请求中提取上下文（spaceId/pictureId），根据空间类型动态计算权限列表。权限配置外置于 JSON 文件（`biz/spaceUserAuthConfig.json`），实现 [[RBAC]] 模型。

### 5. Disruptor 高性能事件驱动（WebSocket）

WebSocket 消息不直接在 I/O 线程处理，而是发布到 Disruptor RingBuffer（256K 容量）：

```
WebSocket 消息 → PictureEditEventProducer → RingBuffer → PictureEditEventWorkHandler
```

- Event：`PictureEditEvent`
- Producer：发布到 RingBuffer
- Consumer：`WorkHandler`，按消息类型分发处理

**通用价值**：生产者-消费者解耦 + 无锁高吞吐，适合 WebSocket 消息处理场景。

### 6. 动态分表（ShardingSphere）

- 按 `spaceId` 对 `picture` 表分片
- 仅为旗舰版团队空间创建 `picture_{spaceId}` 子表
- 运行时通过 ShardingSphere `ContextManager` 动态更新 `actual-data-nodes`

### 7. 编程式事务 + 异步资源清理

- 上传/删除使用 `TransactionTemplate` 编程式事务（非声明式 `@Transactional`）
- COS 文件清理使用 `@Async` 异步执行，不阻塞主流程
- 空间配额通过 `setSql` 在 SQL 层面做增量更新，避免并发问题

## 前端架构要点

- **API 契约自动生成**：`@umijs/openapi` 从后端 Knife4j OpenAPI 规范生成 TypeScript 客户端
- **Pinia 状态管理**：仅一个 `useLoginUserStore`，管理当前用户信息
- **动态路由守卫**：首次加载等待后端返回用户信息后再校验
- **颜色搜索**：RGB 欧氏距离计算相似度，全量加载空间内图片排序取 Top 12

## 统一异常体系

```
ErrorCode 枚举 → BusinessException → ThrowUtils.throwIf() → GlobalExceptionHandler → BaseResponse
```

`ThrowUtils.throwIf(condition, errorCode)` — 条件断言式异常抛出，一行代码替代 if-throw。

## 数据库设计

4 张核心表，主键均为雪花算法：

- `user` — 用户（含 VIP 字段）
- `picture` — 图片（含元数据、审核、空间归属）
- `space` — 空间（含配额：maxSize/maxCount/totalSize/totalCount）
- `space_user` — 空间成员（M:N，含角色 viewer/editor/admin）

## 相关链接

- 架构模式：[[DDD]], [[RBAC]], [[模板方法模式]], [[策略模式]], [[门面模式]]
- 技术栈：[[MyBatisPlus]], [[ShardingSphere]], [[Disruptor]], [[Sa-Token]], [[Caffeine]]
- 原始代码：`raw/i/FloraPic/`
