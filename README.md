# RESTful API 文档展示项目

这是一个使用 Swagger UI 展示 OpenAPI 3.0 规范文档的 Node.js 项目。项目提供了一个简洁的 Web 界面来查看和测试 API 接口。

## 🚀 功能特性

- 📖 基于 OpenAPI 3.0.3 规范的 API 文档
- 🎨 使用 Swagger UI 提供美观的交互式文档界面
- 🔧 支持多环境服务器配置（开发、测试、UAT、生产）
- 🔐 支持 Bearer Token 认证
- 📝 完整的 API 接口定义和响应示例
- 🎭 **API Mock 服务器**: 基于 OpenAPI 规范提供真实的 Mock 响应
- 🧪 **双服务器模式**: 支持文档展示和 API 测试两种模式

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
- **@stoplight/prism-cli** - API Mock 服务器

## 📦 安装和运行

### 前置要求

- Node.js (版本 14 或更高)
- npm 或 yarn 包管理器

### 安装依赖

```bash
npm install
```

### 启动服务

#### 启动 Swagger UI 文档服务器
```bash
npm start
```

或者直接运行：
```bash
node main.js
```

#### 启动 API Mock 服务器
```bash
npm run mock
```

### 访问地址

#### Swagger UI 文档服务器
- **服务器地址**: http://localhost:3000
- **API 文档**: http://localhost:3000/api-docs

#### API Mock 服务器
- **Mock 服务器**: http://127.0.0.1:3001
- **支持真实的 API 调用和响应模拟**

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

- **开发环境**: http://127.0.0.1:3001/ (Mock 服务器)
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

项目依赖如下（详见 package.json）：

- **swagger-ui-express**: 提供 Swagger UI 的 Express 中间件
- **yaml**: 用于解析 YAML 格式的 OpenAPI 规范文件
- **@stoplight/prism-cli**: 提供 API Mock 功能，可以基于 OpenAPI 规范生成真实的 Mock 响应

## 🎯 项目脚本

- **npm start**: 启动 Swagger UI 文档服务器（端口 3000）
- **npm run mock**: 启动 API Mock 服务器（端口 3001），支持真实的 API 调用测试
- **npm test**: 运行 API 测试（需配置 test 目录下的 YAML 测试集合，依赖 inso 工具）

> test 脚本说明：
> - 该脚本会运行 `inso run collection -w ./test/*.yaml -e '<collection name>' wrk_scratc`
> - 请确保已安装 [Insomnia/Inso CLI](https://docs.insomnia.rest/inso-cli/introduction) 并在 test 目录下准备好测试用例 YAML 文件。
> - Inso CLI 安装方法，详情见官方文档。

## 📄 许可证

本项目采用 ISC 许可证 - 查看 [LICENSE](LICENSE) 文件了解详情。

## 🆘 常见问题

### Q: 如何修改服务器端口？
A: 在 `main.js` 文件中修改 `port` 变量的值。

### Q: 如何添加新的数据模型？
A: 在 `api.yml` 文件的 `components/schemas` 部分添加新的模型定义。

### Q: 如何启用 CORS？
A: 安装 `cors` 包并在 `main.js` 中添加相应的中间件配置。

### Q: 如何使用 Mock 服务器进行 API 测试？
A: 运行 `npm run mock` 启动 Mock 服务器，然后可以使用 Postman 或其他工具调用 http://127.0.0.1:3001 上的 API 接口进行测试。

### Q: Mock 服务器和文档服务器有什么区别？
A: 
- **文档服务器** (npm start): 提供 Swagger UI 界面，用于查看和测试 API 文档
- **Mock 服务器** (npm run mock): 提供真实的 API 响应模拟，可以用于前端开发和集成测试

## 🎓 API 练习题目

以下是为学员设计的API练习题目，涵盖了不同的RESTful设计模式和业务场景。学员需要基于OpenAPI 3.0规范设计并实现这些API。

### 📚 练习题目列表

#### 1. 订单管理系统 API
**业务场景**: 设计一个电商订单管理系统，支持订单的创建、查询、状态更新等操作。

**要求实现的接口**:
- `GET /orders` - 获取订单列表（支持分页、状态筛选、时间范围查询）
- `POST /orders` - 创建新订单
- `GET /orders/{orderId}` - 获取订单详情
- `PUT /orders/{orderId}` - 更新订单信息
- `DELETE /orders/{orderId}` - 取消订单
- `PATCH /orders/{orderId}/status` - 更新订单状态
- `GET /orders/{orderId}/items` - 获取订单商品列表

**数据模型要求**:
- Order: 包含订单ID、用户ID、订单状态、总金额、创建时间等
- OrderItem: 包含商品ID、数量、单价、小计等
- OrderStatus: 枚举值（pending, confirmed, shipped, delivered, cancelled）

**练习要点**:
- 状态机设计（订单状态流转）
- 嵌套资源设计（订单商品）
- 部分更新操作（PATCH方法）
- 复杂查询参数设计

#### 2. 产品目录 API
**业务场景**: 设计一个产品目录管理系统，支持产品分类、属性、库存等管理。

**要求实现的接口**:
- `GET /categories` - 获取产品分类列表
- `POST /categories` - 创建产品分类
- `GET /categories/{categoryId}/products` - 获取分类下的产品
- `GET /products` - 获取产品列表（支持分类筛选、价格范围、关键词搜索）
- `POST /products` - 创建新产品
- `GET /products/{productId}` - 获取产品详情
- `PUT /products/{productId}` - 更新产品信息
- `DELETE /products/{productId}` - 删除产品

**数据模型要求**:
- Category: 包含分类ID、名称、描述、父分类ID等
- Product: 包含产品ID、名称、描述、价格、库存、分类ID等
- ProductVariant: 包含变体ID、产品ID、规格、价格、库存等

**练习要点**:
- 层级结构设计（分类树）
- 关联资源查询
- 复杂搜索和筛选
- 产品变体管理

#### 3. 评论系统 API
**业务场景**: 设计一个评论系统，支持用户对产品、文章等进行评论和评分。

**要求实现的接口**:
- `GET /reviews` - 获取评论列表（支持分页、评分筛选、时间排序）
- `POST /reviews` - 创建新评论
- `GET /reviews/{reviewId}` - 获取评论详情
- `PUT /reviews/{reviewId}` - 更新评论
- `DELETE /reviews/{reviewId}` - 删除评论
- `POST /reviews/{reviewId}/likes` - 点赞评论
- `DELETE /reviews/{reviewId}/likes` - 取消点赞
- `GET /reviews/{reviewId}/replies` - 获取评论回复
- `POST /reviews/{reviewId}/replies` - 回复评论

**数据模型要求**:
- Review: 包含评论ID、用户ID、目标类型、目标ID、内容、评分、创建时间等
- ReviewLike: 包含点赞ID、评论ID、用户ID、创建时间等
- ReviewReply: 包含回复ID、评论ID、用户ID、内容、创建时间等

**练习要点**:
- 多态关联设计（评论可以针对不同类型的目标）
- 嵌套评论设计（回复功能）
- 点赞功能设计
- 评分统计和排序

#### 4. 通知系统 API
**业务场景**: 设计一个通知系统，支持系统通知、用户消息、推送通知等。

**要求实现的接口**:
- `GET /notifications` - 获取通知列表（支持分页、类型筛选、已读状态）
- `POST /notifications` - 创建系统通知
- `GET /notifications/{notificationId}` - 获取通知详情
- `PUT /notifications/{notificationId}/read` - 标记通知为已读
- `DELETE /notifications/{notificationId}` - 删除通知
- `PUT /notifications/read-all` - 标记所有通知为已读
- `GET /notifications/unread-count` - 获取未读通知数量
- `POST /notifications/broadcast` - 发送广播通知

**数据模型要求**:
- Notification: 包含通知ID、用户ID、类型、标题、内容、已读状态、创建时间等
- NotificationType: 枚举值（system, user, order, promotion等）

**练习要点**:
- 批量操作设计
- 统计查询设计
- 广播功能设计
- 状态管理设计

### 🎯 练习要求

#### 基础要求
1. **遵循RESTful设计原则**
   - 使用合适的HTTP方法
   - 设计合理的URL结构
   - 返回正确的HTTP状态码

2. **完整的OpenAPI 3.0规范**
   - 定义完整的请求/响应模型
   - 包含参数验证规则
   - 提供响应示例

3. **错误处理设计**
   - 定义统一的错误响应格式
   - 包含适当的错误状态码
   - 提供详细的错误信息

#### 进阶要求
1. **查询优化**
   - 支持分页查询
   - 实现搜索和筛选功能
   - 支持排序操作

2. **关联关系设计**
   - 设计合理的资源关联
   - 支持嵌套资源查询
   - 实现关联数据的CRUD操作

3. **业务逻辑设计**
   - 实现状态机逻辑
   - 设计数据验证规则
   - 考虑并发和一致性

### 📝 练习提交

学员需要提交以下内容：
1. **OpenAPI 3.0规范文档** (YAML格式)
2. **API设计说明文档** (包含设计思路、业务逻辑说明)
3. **测试用例** (包含主要场景的测试数据)

### 🏆 评分标准

- **设计规范性** (30%): RESTful设计原则的遵循程度
- **功能完整性** (25%): 接口功能的完整性和合理性
- **数据模型设计** (20%): 数据结构的合理性和扩展性
- **错误处理** (15%): 错误处理机制的完善程度
- **文档质量** (10%): 文档的清晰度和完整性

---

## 📞 联系方式

如有问题或建议，请通过以下方式联系：

- 创建 Issue
- 发送邮件至项目维护者

---

**注意**: 这是一个演示项目，生产环境使用前请确保进行适当的安全配置和优化。 