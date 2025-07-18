# RESTful API 文档展示项目

这是一个使用 Swagger UI 展示 OpenAPI 3.0 规范文档的 Node.js 项目。项目提供了一个简洁的 Web 界面来查看和测试 API 接口。

## 🚀 功能特性

- 📖 基于 OpenAPI 3.0.3 规范的 API 文档
- 🎨 使用 Swagger UI 提供美观的交互式文档界面
- 🔧 支持多环境服务器配置（开发、测试、UAT、生产）
- 🔐 支持 Bearer Token 认证
- 📝 完整的 API 接口定义和响应示例

## 📋 项目结构

```
restful-demo/
├── api.yml          # OpenAPI 3.0 规范文档
├── main.js          # Express 服务器主文件
├── package.json     # 项目依赖配置
├── .gitignore       # Git 忽略文件
└── README.md        # 项目说明文档
```

## 🛠️ 技术栈

- **Node.js** - JavaScript 运行时环境
- **Express.js** - Web 应用框架
- **swagger-ui-express** - Swagger UI 集成中间件
- **yaml** - YAML 文件解析器

## 📦 安装和运行

### 前置要求

- Node.js (版本 14 或更高)
- npm 或 yarn 包管理器

### 安装依赖

```bash
npm install
```

### 启动服务

```bash
npm start
```

或者直接运行：

```bash
node main.js
```

### 访问地址

- **服务器地址**: http://localhost:3000
- **API 文档**: http://localhost:3000/api-docs

## 📚 API 文档说明

### 当前包含的接口

#### 用户管理
1. **POST /users** - 用户注册
2. **GET /users/{userId}** - 根据用户ID获取用户信息

#### 图书管理
3. **GET /books** - 获取图书列表（支持分页、搜索、筛选）
4. **POST /books** - 创建新图书
5. **GET /books/{bookId}** - 获取图书详情
6. **PUT /books/{bookId}** - 更新图书信息
7. **DELETE /books/{bookId}** - 删除图书

#### 用户图书管理
8. **GET /users/{userId}/books** - 获取用户的图书列表
9. **POST /users/{userId}/books** - 为用户添加图书
10. **DELETE /users/{userId}/books/{bookId}** - 移除用户的图书

### 环境配置

项目支持多环境配置：

- **开发环境**: https://dev.server.test/v1
- **测试环境**: https://test.server.test/v1
- **UAT环境**: https://uat.server.test/v1
- **生产环境**: https://prod.server.test/v1

### 认证方式

项目使用 Bearer Token 认证方式，在 Swagger UI 界面中可以输入 Token 进行接口测试。

### API 功能特性

#### 图书管理功能
- **完整的CRUD操作**: 支持图书的创建、读取、更新、删除
- **分页查询**: 支持分页获取图书列表
- **搜索筛选**: 支持按标题、作者、分类进行搜索
- **状态管理**: 图书状态包括可用(available)、借出(borrowed)、预约(reserved)

#### 用户图书管理功能
- **个人书单**: 用户可以管理自己的图书收藏
- **阅读状态**: 支持阅读中(reading)、已完成(completed)、愿望清单(wishlist)三种状态
- **批量操作**: 支持为用户批量添加和移除图书

#### 数据验证
- **输入验证**: 所有接口都包含完整的参数验证
- **格式检查**: ISBN、邮箱、手机号等字段包含格式验证
- **长度限制**: 字符串字段包含合理的长度限制

## 🔧 开发指南

### 修改 API 文档

编辑 `api.yml` 文件来修改 API 规范：

```yaml
openapi: '3.0.3'
info:
  title: 你的项目名称
  version: '1.0'
# ... 其他配置
```

### 添加新的接口

在 `api.yml` 文件的 `paths` 部分添加新的接口定义：

```yaml
paths:
  /your-endpoint:
    get:
      summary: 接口描述
      responses:
        200:
          description: 成功响应
```

## 📝 依赖说明

- **swagger-ui-express**: 提供 Swagger UI 的 Express 中间件
- **yaml**: 用于解析 YAML 格式的 OpenAPI 规范文件



## 📄 许可证

本项目采用 ISC 许可证 - 查看 [LICENSE](LICENSE) 文件了解详情。

## 🆘 常见问题

### Q: 如何修改服务器端口？
A: 在 `main.js` 文件中修改 `port` 变量的值。

### Q: 如何添加新的数据模型？
A: 在 `api.yml` 文件的 `components/schemas` 部分添加新的模型定义。

### Q: 如何启用 CORS？
A: 安装 `cors` 包并在 `main.js` 中添加相应的中间件配置。

## 📞 联系方式

如有问题或建议，请通过以下方式联系：

- 创建 Issue
- 发送邮件至项目维护者

---

**注意**: 这是一个演示项目，生产环境使用前请确保进行适当的安全配置和优化。 