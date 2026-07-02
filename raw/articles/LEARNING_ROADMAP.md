# Mars Admin 项目学习路线

> 📚 本文档为学习者提供系统化的学习路径，从基础到进阶，循序渐进地掌握整个项目。

---

## 📋 目录

- [项目概览](#项目概览)
- [学习路线总览](#学习路线总览)
- [第一阶段：环境搭建与项目启动](#第一阶段环境搭建与项目启动)
- [第二阶段：理解项目架构](#第二阶段理解项目架构)
- [第三阶段：后端核心模块深入](#第三阶段后端核心模块深入)
- [第四阶段：前端开发学习](#第四阶段前端开发学习)
- [第五阶段：高级特性与实战](#第五阶段高级特性与实战)
- [核心知识点速查表](#核心知识点速查表)
- [常见问题与解决方案](#常见问题与解决方案)

---

## 项目概览

**Mars Admin** 是一个基于 **Spring Boot 3 + Vue 3** 的现代化企业级后台管理系统。

### 技术栈一览

| 层级 | 技术栈 |
|------|--------|
| **后端框架** | Spring Boot 3.2.2 + Java 17 |
| **ORM 框架** | MyBatis-Plus 3.5.5 |
| **权限认证** | Sa-Token 1.37.0 |
| **缓存** | Redis 7.0+ |
| **数据库** | MySQL 8.0+ |
| **前端框架** | Vue 3.4 + TypeScript 5.3 |
| **UI 组件库** | Naive UI 2.37 |
| **构建工具** | Vite 5.0 |
| **移动端** | UniApp (小程序) |

### 项目模块结构

```
mars-admin
├── mars-common          # 公共基础模块（实体基类、统一响应、异常处理）
├── mars-infra           # 基础设施层（数据库、缓存、文件存储、第三方服务）
├── mars-core            # 业务核心层（系统管理、认证、消息、代码生成）
├── mars-api             # 接口层（管理端、APP端、Web端 API）
├── mars-job             # 定时任务模块
├── mars-starter         # 启动入口模块
├── mars-ui              # PC 管理后台前端
└── mars-uniapp          # 移动端小程序
```

---

## 学习路线总览

```
阶段一：环境搭建与项目启动
    ↓
阶段二：理解项目架构
    ↓
阶段三：后端核心模块深入
    ↓
阶段四：前端开发学习
    ↓
阶段五：高级特性与实战
```

**预计学习时间**：
- 快速上手：1-2 天
- 深入理解：1-2 周
- 完全掌握：3-4 周

---

## 第一阶段：环境搭建与项目启动

### 1.1 环境准备

**必装软件**：
- JDK 17+
- Maven 3.8+
- MySQL 8.0+
- Redis 7.0+
- Node.js 18+
- IDE（推荐 IntelliJ IDEA）

### 1.2 数据库初始化

```bash
# 1. 创建数据库
mysql -u root -p
CREATE DATABASE `mars-system` DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;

# 2. 导入 SQL 脚本
mysql -u root -p mars-system < sql/mars-system.sql
```

### 1.3 后端启动

```bash
# 1. 修改数据库配置
vim mars-starter/src/main/resources/application-dev.yml

# 2. 启动 Redis
redis-server

# 3. 运行主启动类
MarsAdminApplication.java
```

### 1.4 前端启动

```bash
cd mars-ui

# 1. 安装依赖
npm install

# 2. 启动开发服务器
npm run dev
```

### ✅ 阶段一检验标准

- [ ] 后端服务成功启动，无报错
- [ ] 前端页面可以访问
- [ ] 可以使用默认账号登录系统

---

## 第二阶段：理解项目架构

### 2.1 分层架构理解

```
┌─────────────────────────────────────────────────────────┐
│                    mars-starter (启动层)                   │
│                   - 应用启动入口                           │
│                   - 配置文件管理                           │
├─────────────────────────────────────────────────────────┤
│                    mars-api (接口层)                       │
│         - Controller 接口定义                             │
│         - 请求参数校验                                    │
│         - 响应数据封装                                    │
├─────────────────────────────────────────────────────────┤
│                    mars-core (业务核心层)                  │
│         - 业务逻辑实现                                    │
│         - 数据实体定义                                    │
│         - 服务接口与实现                                   │
├─────────────────────────────────────────────────────────┤
│                    mars-infra (基础设施层)                 │
│         - 数据库访问                                      │
│         - 缓存管理                                       │
│         - 第三方服务集成                                   │
├─────────────────────────────────────────────────────────┤
│                    mars-common (公共基础层)                │
│         - 基础工具类                                      │
│         - 统一响应封装                                    │
│         - 全局异常处理                                    │
└─────────────────────────────────────────────────────────┘
```

### 2.2 核心设计模式

#### 2.2.1 策略工厂模式

**应用场景**：文件存储、短信服务、支付服务、登录策略

```java
// 以登录策略为例
public interface LoginStrategy {
    LoginVo login(LoginDto dto);
}

@Service
public class LoginStrategyFactory {
    @Autowired
    private Map<String, LoginStrategy> strategyMap;

    public LoginStrategy getStrategy(String loginType) {
        return strategyMap.get(loginType);
    }
}
```

**学习要点**：
- 理解策略模式如何消除 `if-else` 判断
- 掌握 Spring 自动注入 Map 的技巧

#### 2.2.2 统一响应封装

```java
// Result.java - 统一响应
public class Result<T> {
    private int code;       // 状态码
    private String msg;     // 提示信息
    private T data;         // 数据
}

// PageResult.java - 分页响应
public class PageResult<T> {
    private List<T> list;   // 数据列表
    private long total;     // 总条数
}
```

#### 2.2.3 全局异常处理

```java
@RestControllerAdvice
public class GlobalExceptionHandler {
    @ExceptionHandler(BusinessException.class)
    public Result<?> handleBusinessException(BusinessException e) {
        return Result.fail(e.getMessage());
    }
}
```

### 2.3 请求处理流程

```
客户端请求
    ↓
Controller（参数校验）
    ↓
Service（业务逻辑）
    ↓
Mapper（数据访问）
    ↓
数据库
```

### ✅ 阶段二检验标准

- [ ] 能画出项目的分层架构图
- [ ] 理解策略工厂模式的应用场景
- [ ] 掌握请求从 Controller 到数据库的完整流程

---

## 第三阶段：后端核心模块深入

### 3.1 mars-common 模块

**学习路径**：
1. `BaseEntity` → 理解实体基类设计
2. `Result` / `PageResult` → 统一响应封装
3. `BusinessException` → 自定义异常
4. `GlobalExceptionHandler` → 全局异常处理

**关键文件**：
- `mars-common/src/main/java/com/mars/common/entity/BaseEntity.java`
- `mars-common/src/main/java/com/mars/common/result/Result.java`
- `mars-common/src/main/java/com/mars/common/exception/GlobalExceptionHandler.java`

### 3.2 mars-infra 模块

#### 3.2.1 数据库配置（mars-db）

**学习要点**：
- MyBatis-Plus 配置
- 数据权限拦截器实现
- 分页插件配置

```java
@Configuration
public class MybatisPlusConfig {
    @Bean
    public MybatisPlusInterceptor mybatisPlusInterceptor() {
        MybatisPlusInterceptor interceptor = new MybatisPlusInterceptor();
        // 分页插件
        interceptor.addInnerInterceptor(new PaginationInnerInterceptor());
        // 数据权限插件
        interceptor.addInnerInterceptor(new DataPermissionInterceptor());
        return interceptor;
    }
}
```

#### 3.2.2 文件存储（mars-oss）

**策略工厂模式实践**：
```java
public interface FileStorageService {
    String upload(MultipartFile file);
    boolean delete(String url);
}

@Service
public class FileStorageFactory {
    @Autowired
    private Map<String, FileStorageService> storageMap;

    public FileStorageService getStorage(String type) {
        return storageMap.get(type);
    }
}
```

**支持的存储类型**：
- 本地存储
- MinIO
- 阿里云 OSS
- 腾讯 COS
- RustFS

### 3.3 mars-core 模块

#### 3.3.1 系统管理（mars-system）

**核心实体**：
- `SysUser` - 系统用户
- `SysRole` - 角色
- `SysMenu` - 菜单/权限
- `SysDept` - 部门
- `SysPost` - 岗位

**RBAC 权限模型**：
```
用户 (SysUser)
    ↓ N:N
角色 (SysRole)
    ↓ N:N
菜单/权限 (SysMenu)
```

#### 3.3.2 认证授权（mars-auth）

**多种登录策略**：
1. 密码登录（`PasswordLoginStrategy`）
2. 短信验证码登录（`SmsCodeLoginStrategy`）
3. 社交登录（`SocialLoginStrategy`）
4. 小程序登录（`MiniProgramLoginStrategy`）

**Sa-Token 集成**：
```java
// 登录
StpUtil.login(userId);

// 权限校验
@SaCheckPermission("system:user:list")
public Result<?> listUsers() {
    // ...
}
```

#### 3.3.3 AOP 切面

**操作日志记录**：
```java
@Log(module = "用户管理", type = LogType.INSERT)
@SaCheckPermission("system:user:add")
public Result<?> addUser(@RequestBody SysUser user) {
    // ...
}
```

**防重复提交**：
```java
@RepeatSubmit(interval = 2000)
public Result<?> submitForm(@RequestBody FormDto dto) {
    // ...
}
```

### ✅ 阶段三检验标准

- [ ] 能独立阅读并理解一个完整的业务模块代码
- [ ] 掌握 RBAC 权限模型的设计与实现
- [ ] 理解 Sa-Token 的使用方式
- [ ] 掌握 AOP 切面的实际应用

---

## 第四阶段：前端开发学习

### 4.1 前端项目结构

```
mars-ui/
├── src/
│   ├── api/           # API 接口定义
│   ├── components/    # 公共组件
│   ├── layout/        # 布局组件
│   ├── router/        # 路由配置
│   ├── stores/        # Pinia 状态管理
│   ├── utils/         # 工具函数
│   └── views/         # 页面组件
├── public/            # 静态资源
└── vite.config.ts     # Vite 配置
```

### 4.2 核心技术点

#### 4.2.1 Vue 3 组合式 API

```vue
<script setup lang="ts">
import { ref, onMounted } from 'vue'
import { getUserList } from '@/api/system/user'

const userList = ref([])
const loading = ref(false)

const fetchData = async () => {
  loading.value = true
  const { data } = await getUserList()
  userList.value = data.list
  loading.value = false
}

onMounted(() => {
  fetchData()
})
</script>
```

#### 4.2.2 TypeScript 类型定义

```typescript
// 用户类型定义
interface User {
  id: number
  username: string
  nickname: string
  email: string
  phone: string
  status: number
}

// API 响应类型
interface ApiResponse<T> {
  code: number
  msg: string
  data: T
}
```

#### 4.2.3 Pinia 状态管理

```typescript
// stores/user.ts
export const useUserStore = defineStore('user', () => {
  const token = ref('')
  const userInfo = ref<User | null>(null)

  const login = async (loginForm: LoginForm) => {
    const { data } = await loginApi(loginForm)
    token.value = data.token
  }

  const logout = () => {
    token.value = ''
    userInfo.value = null
  }

  return { token, userInfo, login, logout }
})
```

#### 4.2.4 Axios 请求封装

```typescript
// utils/request.ts
const service = axios.create({
  baseURL: import.meta.env.VITE_API_URL,
  timeout: 10000
})

// 请求拦截器
service.interceptors.request.use(config => {
  const userStore = useUserStore()
  if (userStore.token) {
    config.headers['Authorization'] = `Bearer ${userStore.token}`
  }
  return config
})

// 响应拦截器
service.interceptors.response.use(response => {
  const { code, msg, data } = response.data
  if (code === 200) {
    return data
  }
  return Promise.reject(new Error(msg))
})
```

### 4.3 页面开发流程

1. **定义 API** → `api/system/user.ts`
2. **定义类型** → `types/user.ts`
3. **创建页面** → `views/system/user/index.vue`
4. **配置路由** → `router/modules/system.ts`

### ✅ 阶段四检验标准

- [ ] 掌握 Vue 3 组合式 API 的使用
- [ ] 理解 TypeScript 类型定义
- [ ] 掌握 Pinia 状态管理
- [ ] 能独立开发一个新的管理页面

---

## 第五阶段：高级特性与实战

### 5.1 数据权限

**注解方式**：
```java
@DataScope(deptAlias = "d", userAlias = "u")
public List<SysUser> selectUserList(SysUser user) {
    return userMapper.selectUserList(user);
}
```

**实现原理**：
- 通过 MyBatis 拦截器实现
- 自动拼接 SQL 条件
- 根据用户角色过滤数据

### 5.2 定时任务

**Quartz 集成**：
```java
@DisallowConcurrentExecution
public class ApiAccessLogJob implements Job {
    @Override
    public void execute(JobExecutionContext context) {
        // 定时刷新 API 访问日志到数据库
    }
}
```

### 5.3 WebSocket 实时通信

**消息推送**：
```java
@Component
public class MessageWebSocketHandler extends TextWebSocketHandler {
    @Override
    protected void handleTextMessage(WebSocketSession session, TextMessage message) {
        // 处理消息
    }
}
```

### 5.4 代码生成器

**功能**：
- 根据数据库表自动生成 CRUD 代码
- 支持自定义模板
- 一键生成前后端代码

### 5.5 实战练习建议

#### 练习一：添加新模块

1. 在数据库创建新表
2. 使用代码生成器生成基础代码
3. 根据业务需求修改 Service 层逻辑
4. 添加权限控制

#### 练习二：集成新的第三方服务

1. 在 `mars-infra` 创建新模块
2. 实现策略接口
3. 注册到工厂类
4. 在业务层调用

#### 练习三：开发新功能页面

1. 设计数据库表结构
2. 编写后端接口
3. 开发前端页面
4. 联调测试

### ✅ 阶段五检验标准

- [ ] 理解数据权限的实现原理
- [ ] 掌握定时任务的配置与使用
- [ ] 能使用代码生成器提高开发效率
- [ ] 能独立完成一个完整功能的开发

---

## 核心知识点速查表

### 后端知识点

| 知识点 | 关键类/接口 | 位置 |
|--------|------------|------|
| 统一响应 | `Result`, `PageResult` | mars-common |
| 全局异常 | `GlobalExceptionHandler` | mars-common |
| 实体基类 | `BaseEntity` | mars-common |
| 数据权限 | `DataScope`, `DataPermissionInterceptor` | mars-db |
| 操作日志 | `@Log`, `LogAspect` | mars-system |
| 防重复提交 | `@RepeatSubmit`, `RepeatSubmitAspect` | mars-system |
| 登录策略 | `LoginStrategy`, `LoginStrategyFactory` | mars-auth |
| 文件存储 | `FileStorageService`, `FileStorageFactory` | mars-oss |
| 短信服务 | `SmsService`, `SmsServiceFactory` | mars-sms |
| 推送服务 | `PushService`, `PushServiceFactory` | mars-push |

### 前端知识点

| 知识点 | 关键文件 | 说明 |
|--------|----------|------|
| API 封装 | `utils/request.ts` | Axios 拦截器配置 |
| 状态管理 | `stores/*.ts` | Pinia 状态定义 |
| 路由配置 | `router/modules/*.ts` | 动态路由生成 |
| 权限指令 | `directives/permission.ts` | 按钮级权限控制 |
| 表格封装 | `components/Table.vue` | 通用表格组件 |
| 表单封装 | `components/Form.vue` | 通用表单组件 |

---

## 常见问题与解决方案

### Q1: 项目启动报错，数据库连接失败

**解决方案**：
1. 检查 MySQL 服务是否启动
2. 检查 `application-dev.yml` 中的数据库配置
3. 确认数据库 `mars-system` 已创建并导入 SQL

### Q2: 前端页面白屏

**解决方案**：
1. 检查 Node.js 版本是否为 18+
2. 清除 `node_modules` 重新安装
3. 检查控制台是否有报错信息

### Q3: 登录后提示权限不足

**解决方案**：
1. 检查用户是否分配了角色
2. 检查角色是否分配了菜单权限
3. 检查菜单的权限标识是否正确

### Q4: 如何添加新的 API 接口

**步骤**：
1. 在 `mars-core` 中定义 Service 接口和实现
2. 在 `mars-api` 中创建 Controller
3. 在 `mars-ui` 中定义 API 调用函数
4. 在页面中调用 API

---

## 学习资源推荐

### 官方文档

- [Spring Boot 官方文档](https://spring.io/projects/spring-boot)
- [MyBatis-Plus 官方文档](https://baomidou.com/)
- [Sa-Token 官方文档](https://sa-token.dev33.cn/)
- [Vue 3 官方文档](https://vuejs.org/)
- [Naive UI 官方文档](https://www.naiveui.com/)

### 推荐阅读顺序

1. 先通读本文档，建立整体认知
2. 按照阶段顺序学习，每个阶段完成后检验标准
3. 遇到问题时查阅官方文档
4. 多动手实践，通过修改代码加深理解

---

## 总结

Mars Admin 是一个非常适合学习的企业级项目，它涵盖了：

✅ **后端开发**：Spring Boot 3、MyBatis-Plus、Sa-Token 权限认证

✅ **前端开发**：Vue 3、TypeScript、Pinia 状态管理

✅ **架构设计**：分层架构、策略模式、AOP 切面

✅ **工程实践**：统一响应、全局异常、数据权限

通过系统学习这个项目，你将掌握企业级后台管理系统的完整开发流程，为实际工作打下坚实基础。

---

*最后更新：2026-06-02*
