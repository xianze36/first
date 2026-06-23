# 黑科MES制造执行系统

基于 Spring Boot 2.1.7 + MyBatis-Plus + Layui + FreeMarker 的制造执行系统（MES）。

## 环境要求

| 组件 | 版本 |
|:---|:---|
| JDK | 1.8+ |
| MySQL | 5.7+ / 8.0 |
| Redis | 3.0+ |
| Maven | 3.6+ |
| Node.js | 可选（仅前端构建时） |

## 快速部署

### 1. 初始化数据库

```sql
-- 创建数据库
CREATE DATABASE IF NOT EXISTS sparchetype DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;

-- 导入初始数据（主脚本）
source scripts/sql/MySQL-20210225.sql
```

> **注意**：主脚本 `scripts/sql/MySQL-20210225.sql` 包含完整的建表和初始数据。

### 2. 新增模块数据库脚本（按需执行）

额外 SQL 脚本位于项目根目录（`mes/` 下）：

| 脚本文件 | 说明 |
|:---|:---|
| `create_process_definition.sql` | 创建工序定义表 |
| `create_process_route.sql` | 创建工艺路线表 |
| `create_process_content.sql` | 创建工艺内容表 |
| `create_full_menu.sql` | 创建完整菜单数据 |
| `insert_bom_data.sql` | 插入BOM示例数据 |
| `insert_process_menu.sql` | 插入工艺菜单 |
| `insert_equipment_group_menu.sql` | 插入设备组菜单 |
| `fix_process_menu.sql` | 修复工艺菜单 |

### 3. 修改数据库配置

编辑 `src/main/resources/application-dev.yml`，修改数据库连接信息：

```yaml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/sparchetype?useUnicode=true&characterEncoding=utf8&useSSL=false&serverTimezone=Hongkong
    username: root        # 修改为你的数据库用户名
    password: 20050324asd # 修改为你的数据库密码
  redis:
    host: localhost
    port: 6379
```

### 4. 启动项目

```bash
# 方式一：Maven 直接运行
cd mes
mvn spring-boot:run

# 方式二：打成JAR包运行
mvn clean package -DskipTests
java -jar target/mes-1.0.0.jar

# 方式三：Docker 部署
mvn clean package -DskipTests
docker build -t mes:latest .
docker run -d -p 9090:80 --name mes mes:latest
```

### 5. 访问系统

- **地址**：http://localhost:9090
- **默认账号**：admin / admin
- **验证码**：任意输入4位数字

## 项目结构

```
mes/
├── src/main/java/com/wangziyang/mes/
│   ├── common/                  # 公共模块
│   │   ├── config/              # 配置类（WebMvc、MybatisPlus等）
│   │   ├── util/                # 工具类
│   │   ├── BaseController.java  # 基础控制器
│   │   ├── BaseEntity.java      # 基础实体
│   │   └── Result.java          # 统一返回结果
│   ├── basedata/                # 基础数据模块
│   │   ├── controller/          # 控制器
│   │   ├── entity/              # 实体类
│   │   ├── mapper/              # Mapper接口
│   │   └── service/             # 服务层
│   ├── system/                  # 系统管理模块
│   │   ├── controller/admin/    # 后台控制器
│   │   │   ├── SysUserController.java    # 用户管理
│   │   │   ├── SysRoleController.java    # 角色管理
│   │   │   ├── ProcessRouteController.java  # 工艺流程管理
│   │   │   ├── ProcessContentController.java # 工艺内容管理
│   │   │   └── ProcessDefinitionController.java # 工序定义
│   │   ├── entity/              # 实体类
│   │   ├── mapper/              # Mapper接口
│   │   └── service/             # 服务层
│   ├── technology/              # 技术数据模块（BOM、Flow等）
│   └── order/                   # 订单模块
├── src/main/resources/
│   ├── templates/               # FreeMarker模板
│   │   ├── admin/processRoute/  # 工艺流程管理页面
│   │   ├── admin/processContent/# 工艺内容管理页面
│   │   ├── admin/processDefinition/ # 工序定义页面
│   │   ├── admin/system/        # 系统管理页面
│   │   └── basedata/materile/   # 物料管理页面
│   ├── static/                  # 静态资源
│   ├── application.yml          # 主配置
│   └── application-dev.yml      # 开发环境配置
├── src/main/docker/
│   └── Dockerfile               # Docker部署文件
├── scripts/sql/
│   └── MySQL-20210225.sql       # 数据库初始化主脚本
├── pom.xml                      # Maven构建文件
├── create_*.sql                 # 新增模块建表脚本
└── insert_*.sql                 # 新增模块数据脚本
```

## 功能模块

| 模块 | 说明 |
|:---|:---|
| 系统管理 | 用户、角色、菜单、部门管理 |
| 物料管理 | 物料维护、物料信息查询 |
| 工艺流程管理 | 工艺路线树形管理、工序配置 |
| 工艺内容编制 | 工序内容、工序要求、技术文档 |
| 产品工艺查询 | 按物料查询完整工艺流程 |
| 工序定义 | 工序主数据（GX编号） |
| BOM管理 | 物料清单维护 |
| 生产订单 | 生产工单管理 |

## 关键技术

- **后端**：Spring Boot 2.1.7, MyBatis-Plus, Shiro, Redis, Druid, Swagger
- **前端**：Layui 2.x, FreeMarker, jQuery
- **数据库**：MySQL 8.0, Ehcache
- **构建**：Maven, Docker