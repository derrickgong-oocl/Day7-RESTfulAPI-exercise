# RESTful API 设计练习指南

本文档为学员提供详细的API设计练习指导，包含具体的练习题目、设计思路和实现要求。

## 📋 练习概览

本练习旨在帮助学员掌握RESTful API设计的最佳实践，通过实际项目练习来理解API设计原则、OpenAPI 3.0规范的使用，以及不同业务场景下的API设计模式。

## 🎯 练习目标

1. **掌握RESTful设计原则**
   - 理解HTTP方法的语义
   - 设计合理的URL结构
   - 正确使用HTTP状态码

2. **熟练使用OpenAPI 3.0规范**
   - 编写完整的API文档
   - 定义数据模型和验证规则
   - 设计错误处理机制

3. **理解不同业务场景的API设计**
   - 状态机设计
   - 关联关系处理
   - 复杂查询设计

## 📚 练习题目详解

### 练习1: 订单管理系统 API

#### 业务背景
设计一个电商订单管理系统，支持订单的完整生命周期管理，包括创建、确认、发货、交付等状态流转。

#### 核心功能需求
1. **订单管理**: 创建、查询、更新、取消订单
2. **状态管理**: 订单状态流转（待确认→已确认→已发货→已交付→已取消）
3. **商品管理**: 订单包含多个商品，每个商品有数量和价格
4. **查询功能**: 支持按状态、时间范围、用户等条件查询

#### 设计要点

**1. 状态机设计**
```yaml
OrderStatus:
  type: string
  enum:
    - pending      # 待确认
    - confirmed    # 已确认
    - shipped      # 已发货
    - delivered    # 已交付
    - cancelled    # 已取消
```

**2. 嵌套资源设计**
- 订单包含多个商品项
- 使用 `/orders/{orderId}/items` 访问订单商品
- 支持对订单商品的单独操作

**3. 部分更新操作**
- 使用 `PATCH /orders/{orderId}/status` 更新订单状态
- 只更新状态字段，不影响其他信息

#### 示例API设计
```yaml
paths:
  /orders:
    get:
      summary: 获取订单列表
      parameters:
        - name: status
          in: query
          schema:
            $ref: "#/components/schemas/OrderStatus"
        - name: startDate
          in: query
          schema:
            type: string
            format: date
        - name: endDate
          in: query
          schema:
            type: string
            format: date
    post:
      summary: 创建新订单
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: "#/components/schemas/CreateOrderRequest"
  
  /orders/{orderId}:
    get:
      summary: 获取订单详情
    put:
      summary: 更新订单信息
    delete:
      summary: 取消订单
  
  /orders/{orderId}/status:
    patch:
      summary: 更新订单状态
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              properties:
                status:
                  $ref: "#/components/schemas/OrderStatus"
  
  /orders/{orderId}/items:
    get:
      summary: 获取订单商品列表
    post:
      summary: 添加商品到订单
```

### 练习2: 产品目录 API

#### 业务背景
设计一个产品目录管理系统，支持产品分类、属性管理、库存跟踪等功能。

#### 核心功能需求
1. **分类管理**: 支持层级分类结构
2. **产品管理**: 产品的CRUD操作
3. **变体管理**: 产品可以有多个变体（如颜色、尺寸）
4. **搜索筛选**: 支持多条件搜索和筛选

#### 设计要点

**1. 层级结构设计**
```yaml
Category:
  type: object
  properties:
    categoryId:
      type: integer
    name:
      type: string
    parentId:
      type: integer
      nullable: true
    level:
      type: integer
      description: 分类层级深度
```

**2. 关联查询设计**
- 通过 `/categories/{categoryId}/products` 获取分类下的产品
- 支持递归查询子分类
- 支持向上查询父分类

**3. 产品变体设计**
```yaml
ProductVariant:
  type: object
  properties:
    variantId:
      type: integer
    productId:
      type: integer
    sku:
      type: string
    attributes:
      type: object
      additionalProperties: true
      description: 变体属性（如颜色、尺寸）
    price:
      type: number
    stock:
      type: integer
```

#### 示例API设计
```yaml
paths:
  /categories:
    get:
      summary: 获取分类列表
      parameters:
        - name: parentId
          in: query
          schema:
            type: integer
            nullable: true
        - name: level
          in: query
          schema:
            type: integer
    post:
      summary: 创建分类
  
  /categories/{categoryId}/products:
    get:
      summary: 获取分类下的产品
      parameters:
        - name: page
          in: query
          schema:
            type: integer
        - name: limit
          in: query
          schema:
            type: integer
  
  /products:
    get:
      summary: 获取产品列表
      parameters:
        - name: categoryId
          in: query
          schema:
            type: integer
        - name: minPrice
          in: query
          schema:
            type: number
        - name: maxPrice
          in: query
          schema:
            type: number
        - name: keyword
          in: query
          schema:
            type: string
```

### 练习3: 评论系统 API

#### 业务背景
设计一个通用的评论系统，支持用户对产品、文章、服务等进行评论和评分。

#### 核心功能需求
1. **多态评论**: 评论可以针对不同类型的对象
2. **嵌套评论**: 支持评论的回复功能
3. **点赞功能**: 用户可以对评论进行点赞
4. **评分统计**: 计算平均评分和评分分布

#### 设计要点

**1. 多态关联设计**
```yaml
Review:
  type: object
  properties:
    reviewId:
      type: integer
    userId:
      type: integer
    targetType:
      type: string
      enum: [product, article, service]
    targetId:
      type: integer
    content:
      type: string
    rating:
      type: integer
      minimum: 1
      maximum: 5
```

**2. 嵌套评论设计**
- 使用 `parentId` 字段实现评论层级
- 支持多级回复（通常限制在2-3级）
- 提供评论树形结构查询

**3. 点赞功能设计**
```yaml
ReviewLike:
  type: object
  properties:
    likeId:
      type: integer
    reviewId:
      type: integer
    userId:
      type: integer
    createdAt:
      type: string
      format: date-time
```

#### 示例API设计
```yaml
paths:
  /reviews:
    get:
      summary: 获取评论列表
      parameters:
        - name: targetType
          in: query
          schema:
            type: string
        - name: targetId
          in: query
          schema:
            type: integer
        - name: rating
          in: query
          schema:
            type: integer
        - name: sort
          in: query
          schema:
            type: string
            enum: [newest, oldest, rating]
  
  /reviews/{reviewId}/replies:
    get:
      summary: 获取评论回复
    post:
      summary: 回复评论
  
  /reviews/{reviewId}/likes:
    post:
      summary: 点赞评论
    delete:
      summary: 取消点赞
```

### 练习4: 文件上传 API

#### 业务背景
设计一个文件上传和管理系统，支持多种文件类型的上传、下载、分享等功能。

#### 核心功能需求
1. **文件上传**: 支持多种文件格式
2. **文件管理**: 文件信息查询、删除、更新
3. **文件分享**: 生成分享链接，支持访问控制
4. **文件下载**: 支持直接下载和流式下载

#### 设计要点

**1. 文件上传设计**
```yaml
FileUpload:
  type: object
  properties:
    file:
      type: string
      format: binary
    metadata:
      type: object
      properties:
        description:
          type: string
        tags:
          type: array
          items:
            type: string
```

**2. 分享功能设计**
```yaml
FileShare:
  type: object
  properties:
    shareId:
      type: integer
    fileId:
      type: integer
    shareToken:
      type: string
    expiresAt:
      type: string
      format: date-time
    accessCount:
      type: integer
    maxAccess:
      type: integer
```

#### 示例API设计
```yaml
paths:
  /files/upload:
    post:
      summary: 上传文件
      requestBody:
        required: true
        content:
          multipart/form-data:
            schema:
              type: object
              properties:
                file:
                  type: string
                  format: binary
                metadata:
                  type: string
                  description: JSON格式的元数据
  
  /files/{fileId}/download:
    get:
      summary: 下载文件
      responses:
        200:
          description: 文件内容
          content:
            application/octet-stream:
              schema:
                type: string
                format: binary
  
  /files/{fileId}/share:
    post:
      summary: 生成分享链接
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              properties:
                expiresIn:
                  type: integer
                  description: 过期时间（秒）
                maxAccess:
                  type: integer
                  description: 最大访问次数
```

### 练习5: 通知系统 API

#### 业务背景
设计一个通知系统，支持系统通知、用户消息、推送通知等多种类型的通知管理。

#### 核心功能需求
1. **通知管理**: 创建、查询、更新、删除通知
2. **状态管理**: 已读/未读状态跟踪
3. **批量操作**: 批量标记已读、批量删除
4. **统计功能**: 未读通知数量统计

#### 设计要点

**1. 通知类型设计**
```yaml
NotificationType:
  type: string
  enum:
    - system      # 系统通知
    - user        # 用户消息
    - order       # 订单相关
    - promotion   # 促销信息
    - security    # 安全通知
```

**2. 批量操作设计**
- 使用 `PUT /notifications/read-all` 批量标记已读
- 支持条件批量操作（如按类型批量标记）
- 提供操作结果统计

**3. 统计查询设计**
```yaml
NotificationStats:
  type: object
  properties:
    totalCount:
      type: integer
    unreadCount:
      type: integer
    typeStats:
      type: object
      additionalProperties:
        type: integer
```

#### 示例API设计
```yaml
paths:
  /notifications:
    get:
      summary: 获取通知列表
      parameters:
        - name: type
          in: query
          schema:
            $ref: "#/components/schemas/NotificationType"
        - name: read
          in: query
          schema:
            type: boolean
        - name: page
          in: query
          schema:
            type: integer
  
  /notifications/read-all:
    put:
      summary: 标记所有通知为已读
      requestBody:
        required: false
        content:
          application/json:
            schema:
              type: object
              properties:
                type:
                  $ref: "#/components/schemas/NotificationType"
  
  /notifications/unread-count:
    get:
      summary: 获取未读通知数量
      responses:
        200:
          description: 未读数量
          content:
            application/json:
              schema:
                type: object
                properties:
                  count:
                    type: integer
```

## 🛠️ 实现指导

### 1. 项目结构建议
```
project/
├── api-spec/
│   ├── openapi.yaml          # 主API文档
│   ├── schemas/              # 数据模型定义
│   │   ├── common.yaml       # 通用模型
│   │   ├── orders.yaml       # 订单相关模型
│   │   └── ...
│   └── examples/             # 响应示例
├── docs/
│   ├── design-notes.md       # 设计说明
│   └── api-guide.md          # API使用指南
└── tests/
    ├── test-cases.yaml       # 测试用例
    └── sample-data.json      # 测试数据
```

### 2. 设计检查清单

#### RESTful设计检查
- [ ] 使用正确的HTTP方法（GET, POST, PUT, DELETE, PATCH）
- [ ] URL结构清晰且符合RESTful规范
- [ ] 返回正确的HTTP状态码
- [ ] 使用合适的Content-Type

#### OpenAPI规范检查
- [ ] 完整的路径定义
- [ ] 详细的参数说明
- [ ] 完整的请求/响应模型
- [ ] 适当的验证规则
- [ ] 清晰的错误响应定义

#### 业务逻辑检查
- [ ] 状态流转逻辑合理
- [ ] 关联关系设计正确
- [ ] 查询条件设计合理
- [ ] 权限控制考虑周全

### 3. 常见设计模式

#### 分页查询模式
```yaml
parameters:
  - name: page
    in: query
    schema:
      type: integer
      default: 1
      minimum: 1
  - name: limit
    in: query
    schema:
      type: integer
      default: 10
      minimum: 1
      maximum: 100
```

#### 搜索筛选模式
```yaml
parameters:
  - name: keyword
    in: query
    schema:
      type: string
      description: 关键词搜索
  - name: filters
    in: query
    schema:
      type: object
      description: JSON格式的筛选条件
```

#### 状态更新模式
```yaml
/orders/{orderId}/status:
  patch:
    summary: 更新订单状态
    requestBody:
      required: true
      content:
        application/json:
          schema:
            type: object
            properties:
              status:
                $ref: "#/components/schemas/OrderStatus"
              reason:
                type: string
                description: 状态变更原因
```

## 📝 提交要求

### 1. 文档结构
学员需要提交以下文档：

#### OpenAPI规范文档
- 完整的YAML格式API文档
- 包含所有路径、模型、响应定义
- 提供详细的参数说明和示例

#### 设计说明文档
- 业务背景和需求分析
- 设计思路和架构说明
- 数据模型设计说明
- 状态流转逻辑说明

#### 测试用例文档
- 主要功能测试用例
- 边界条件测试用例
- 错误场景测试用例
- 测试数据示例

### 2. 评分标准

#### 设计规范性 (30%)
- RESTful设计原则的遵循程度
- HTTP方法和状态码的正确使用
- URL结构的合理性

#### 功能完整性 (25%)
- 接口功能的完整性和合理性
- 业务逻辑的正确性
- 功能覆盖的全面性

#### 数据模型设计 (20%)
- 数据结构的合理性和扩展性
- 关联关系的正确设计
- 验证规则的完整性

#### 错误处理 (15%)
- 错误处理机制的完善程度
- 错误信息的清晰性
- 异常情况的考虑

#### 文档质量 (10%)
- 文档的清晰度和完整性
- 示例的实用性
- 说明的详细程度

## 🎯 进阶挑战

### 挑战1: 性能优化
- 设计缓存策略
- 实现分页优化
- 考虑查询性能

### 挑战2: 安全性设计
- 实现权限控制
- 设计数据验证
- 考虑安全威胁

### 挑战3: 扩展性设计
- 支持版本控制
- 设计插件机制
- 考虑微服务架构

### 挑战4: 监控和日志
- 设计监控指标
- 实现日志记录
- 考虑追踪机制

## 📚 参考资料

### RESTful API设计
- [REST API Design Best Practices](https://restfulapi.net/)
- [HTTP Status Codes](https://httpstatuses.com/)
- [RESTful API Design Guidelines](https://github.com/Microsoft/api-guidelines)

### OpenAPI规范
- [OpenAPI Specification](https://swagger.io/specification/)
- [OpenAPI Examples](https://github.com/OAI/OpenAPI-Specification/tree/master/examples)
- [OpenAPI Tools](https://openapi.tools/)

### 设计模式
- [API Design Patterns](https://www.oreilly.com/library/view/api-design-patterns/9781617295850/)
- [REST API Design Patterns](https://www.oreilly.com/library/view/rest-api-design/9781449337909/)

---

**注意**: 本练习旨在提高API设计能力，建议学员在实际项目中应用所学知识，并根据具体业务需求进行调整和优化。 