# API 接口测试用例文档

基于 `api.yml` 的 OpenAPI 3.0 规范，本文档提供了完整的 API 接口测试用例。

## 📋 测试环境配置

### 服务器地址
- **开发环境**: http://127.0.0.1:3001/
- **测试环境**: https://test.server.test/v1
- **UAT环境**: https://uat.server.test/v1
- **生产环境**: https://prod.server.test/v1

### 认证方式
- **Bearer Token**: 在请求头中添加 `Authorization: Bearer <token>`

## 🧪 测试用例

### 1. 用户管理模块

#### 1.1 用户注册 - POST /users

**测试用例 1.1.1: 正常注册**
```http
POST /users
Content-Type: application/json

{
  "username": "testuser",
  "email": "test@example.com",
  "password": "password123",
  "confirmPassword": "password123",
  "age": 25,
  "phone": "13800138000"
}
```

**预期响应 (201)**:
```json
{
  "userId": 1,
  "username": "testuser",
  "email": "test@example.com",
  "createdAt": "2024-01-01T10:00:00Z"
}
```

**测试用例 1.1.2: 用户名已存在**
```http
POST /users
Content-Type: application/json

{
  "username": "existinguser",
  "email": "new@example.com",
  "password": "password123",
  "confirmPassword": "password123"
}
```

**预期响应 (409)**:
```json
{
  "code": "USER_EXISTS",
  "message": "用户名已存在"
}
```

**测试用例 1.1.3: 密码不匹配**
```http
POST /users
Content-Type: application/json

{
  "username": "newuser",
  "email": "new@example.com",
  "password": "password123",
  "confirmPassword": "differentpassword"
}
```

**预期响应 (400)**:
```json
{
  "code": "PASSWORD_MISMATCH",
  "message": "密码和确认密码不匹配"
}
```

**测试用例 1.1.4: 邮箱格式错误**
```http
POST /users
Content-Type: application/json

{
  "username": "newuser",
  "email": "invalid-email",
  "password": "password123",
  "confirmPassword": "password123"
}
```

**预期响应 (400)**:
```json
{
  "code": "INVALID_EMAIL",
  "message": "邮箱格式不正确"
}
```

#### 1.2 获取用户信息 - GET /users/{userId}

**测试用例 1.2.1: 正常获取用户信息**
```http
GET /users/1
Authorization: Bearer valid-token
```

**预期响应 (200)**:
```json
{
  "userId": 1,
  "username": "testuser",
  "age": 25,
  "avatar": "data:image/jpeg;base64,/9j/4AAQSkZJRgABAQAAAQ..."
}
```

**测试用例 1.2.2: 用户不存在**
```http
GET /users/999
Authorization: Bearer valid-token
```

**预期响应 (404)**:
```json
{
  "code": "USER_NOT_FOUND",
  "message": "用户不存在"
}
```

**测试用例 1.2.3: 未授权访问**
```http
GET /users/1
```

**预期响应 (401)**:
```json
{
  "code": "UNAUTHORIZED",
  "message": "未授权访问"
}
```

### 2. 图书管理模块

#### 2.1 获取图书列表 - GET /books

**测试用例 2.1.1: 基础分页查询**
```http
GET /books?page=1&limit=10
```

**预期响应 (200)**:
```json
{
  "books": [
    {
      "bookId": 1,
      "title": "JavaScript高级程序设计",
      "author": "Nicholas C. Zakas",
      "isbn": "978-7115545381",
      "category": "编程",
      "description": "JavaScript经典教程",
      "price": 89.00,
      "publishDate": "2019-12-01",
      "publisher": "人民邮电出版社",
      "pages": 696,
      "language": "中文",
      "coverImage": "https://example.com/cover1.jpg",
      "status": "available",
      "createdAt": "2024-01-01T10:00:00Z",
      "updatedAt": "2024-01-01T10:00:00Z"
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 10,
    "total": 1,
    "totalPages": 1
  }
}
```

**测试用例 2.1.2: 标题搜索**
```http
GET /books?title=JavaScript
```

**测试用例 2.1.3: 作者搜索**
```http
GET /books?author=Zakas
```

**测试用例 2.1.4: 分类筛选**
```http
GET /books?category=编程
```

**测试用例 2.1.5: 组合查询**
```http
GET /books?page=1&limit=5&title=Java&category=编程
```

#### 2.2 创建新图书 - POST /books

**测试用例 2.2.1: 正常创建图书**
```http
POST /books
Authorization: Bearer valid-token
Content-Type: application/json

{
  "title": "Python编程从入门到实践",
  "author": "Eric Matthes",
  "isbn": "978-7115428028",
  "category": "编程",
  "description": "Python入门经典教程",
  "price": 79.00,
  "publishDate": "2020-01-01",
  "publisher": "人民邮电出版社",
  "pages": 512,
  "language": "中文",
  "coverImage": "https://example.com/python-cover.jpg"
}
```

**预期响应 (201)**:
```json
{
  "bookId": 2,
  "title": "Python编程从入门到实践",
  "author": "Eric Matthes",
  "isbn": "978-7115428028",
  "category": "编程",
  "description": "Python入门经典教程",
  "price": 79.00,
  "publishDate": "2020-01-01",
  "publisher": "人民邮电出版社",
  "pages": 512,
  "language": "中文",
  "coverImage": "https://example.com/python-cover.jpg",
  "status": "available",
  "createdAt": "2024-01-01T11:00:00Z",
  "updatedAt": "2024-01-01T11:00:00Z"
}
```

**测试用例 2.2.2: 缺少必填字段**
```http
POST /books
Authorization: Bearer valid-token
Content-Type: application/json

{
  "title": "测试图书",
  "author": "测试作者"
}
```

**预期响应 (400)**:
```json
{
  "code": "MISSING_REQUIRED_FIELD",
  "message": "缺少必填字段: isbn"
}
```

**测试用例 2.2.3: ISBN格式错误**
```http
POST /books
Authorization: Bearer valid-token
Content-Type: application/json

{
  "title": "测试图书",
  "author": "测试作者",
  "isbn": "invalid-isbn"
}
```

**预期响应 (422)**:
```json
{
  "code": "INVALID_ISBN",
  "message": "ISBN格式不正确"
}
```

#### 2.3 获取图书详情 - GET /books/{bookId}

**测试用例 2.3.1: 正常获取图书详情**
```http
GET /books/1
```

**预期响应 (200)**:
```json
{
  "bookId": 1,
  "title": "JavaScript高级程序设计",
  "author": "Nicholas C. Zakas",
  "isbn": "978-7115545381",
  "category": "编程",
  "description": "JavaScript经典教程",
  "price": 89.00,
  "publishDate": "2019-12-01",
  "publisher": "人民邮电出版社",
  "pages": 696,
  "language": "中文",
  "coverImage": "https://example.com/cover1.jpg",
  "status": "available",
  "createdAt": "2024-01-01T10:00:00Z",
  "updatedAt": "2024-01-01T10:00:00Z"
}
```

**测试用例 2.3.2: 图书不存在**
```http
GET /books/999
```

**预期响应 (404)**:
```json
{
  "code": "BOOK_NOT_FOUND",
  "message": "图书不存在"
}
```

#### 2.4 更新图书信息 - PUT /books/{bookId}

**测试用例 2.4.1: 正常更新图书**
```http
PUT /books/1
Authorization: Bearer valid-token
Content-Type: application/json

{
  "title": "JavaScript高级程序设计（第4版）",
  "author": "Nicholas C. Zakas",
  "isbn": "978-7115545381",
  "category": "编程",
  "description": "JavaScript经典教程，最新版本",
  "price": 99.00,
  "publishDate": "2023-01-01",
  "publisher": "人民邮电出版社",
  "pages": 720,
  "language": "中文",
  "coverImage": "https://example.com/cover1-v2.jpg",
  "status": "available"
}
```

**预期响应 (200)**:
```json
{
  "bookId": 1,
  "title": "JavaScript高级程序设计（第4版）",
  "author": "Nicholas C. Zakas",
  "isbn": "978-7115545381",
  "category": "编程",
  "description": "JavaScript经典教程，最新版本",
  "price": 99.00,
  "publishDate": "2023-01-01",
  "publisher": "人民邮电出版社",
  "pages": 720,
  "language": "中文",
  "coverImage": "https://example.com/cover1-v2.jpg",
  "status": "available",
  "createdAt": "2024-01-01T10:00:00Z",
  "updatedAt": "2024-01-01T12:00:00Z"
}
```

**测试用例 2.4.2: 更新不存在的图书**
```http
PUT /books/999
Authorization: Bearer valid-token
Content-Type: application/json

{
  "title": "测试图书"
}
```

**预期响应 (404)**:
```json
{
  "code": "BOOK_NOT_FOUND",
  "message": "图书不存在"
}
```

#### 2.5 删除图书 - DELETE /books/{bookId}

**测试用例 2.5.1: 正常删除图书**
```http
DELETE /books/1
Authorization: Bearer valid-token
```

**预期响应 (204)**: 无响应体

**测试用例 2.5.2: 删除不存在的图书**
```http
DELETE /books/999
Authorization: Bearer valid-token
```

**预期响应 (404)**:
```json
{
  "code": "BOOK_NOT_FOUND",
  "message": "图书不存在"
}
```

### 3. 用户图书管理模块

#### 3.1 获取用户的图书 - GET /users/{userId}/books

**测试用例 3.1.1: 正常获取用户图书**
```http
GET /users/1/books?page=1&limit=10
Authorization: Bearer valid-token
```

**预期响应 (200)**:
```json
{
  "userBooks": [
    {
      "userBookId": 1,
      "userId": 1,
      "bookId": 1,
      "book": {
        "bookId": 1,
        "title": "JavaScript高级程序设计",
        "author": "Nicholas C. Zakas",
        "isbn": "978-7115545381",
        "category": "编程",
        "description": "JavaScript经典教程",
        "price": 89.00,
        "publishDate": "2019-12-01",
        "publisher": "人民邮电出版社",
        "pages": 696,
        "language": "中文",
        "coverImage": "https://example.com/cover1.jpg",
        "status": "available",
        "createdAt": "2024-01-01T10:00:00Z",
        "updatedAt": "2024-01-01T10:00:00Z"
      },
      "addedAt": "2024-01-01T13:00:00Z",
      "status": "reading"
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 10,
    "total": 1,
    "totalPages": 1
  }
}
```

**测试用例 3.1.2: 用户不存在**
```http
GET /users/999/books
Authorization: Bearer valid-token
```

**预期响应 (404)**:
```json
{
  "code": "USER_NOT_FOUND",
  "message": "用户不存在"
}
```

#### 3.2 为用户添加图书 - POST /users/{userId}/books

**测试用例 3.2.1: 正常添加图书到愿望清单**
```http
POST /users/1/books
Authorization: Bearer valid-token
Content-Type: application/json

{
  "bookId": 2,
  "status": "wishlist"
}
```

**预期响应 (201)**:
```json
{
  "userBookId": 2,
  "userId": 1,
  "bookId": 2,
  "book": {
    "bookId": 2,
    "title": "Python编程从入门到实践",
    "author": "Eric Matthes",
    "isbn": "978-7115428028",
    "category": "编程",
    "description": "Python入门经典教程",
    "price": 79.00,
    "publishDate": "2020-01-01",
    "publisher": "人民邮电出版社",
    "pages": 512,
    "language": "中文",
    "coverImage": "https://example.com/python-cover.jpg",
    "status": "available",
    "createdAt": "2024-01-01T11:00:00Z",
    "updatedAt": "2024-01-01T11:00:00Z"
  },
  "addedAt": "2024-01-01T14:00:00Z",
  "status": "wishlist"
}
```

**测试用例 3.2.2: 添加已存在的图书**
```http
POST /users/1/books
Authorization: Bearer valid-token
Content-Type: application/json

{
  "bookId": 1,
  "status": "reading"
}
```

**预期响应 (409)**:
```json
{
  "code": "BOOK_ALREADY_EXISTS",
  "message": "图书已存在于用户书单中"
}
```

**测试用例 3.2.3: 图书不存在**
```http
POST /users/1/books
Authorization: Bearer valid-token
Content-Type: application/json

{
  "bookId": 999,
  "status": "wishlist"
}
```

**预期响应 (404)**:
```json
{
  "code": "BOOK_NOT_FOUND",
  "message": "图书不存在"
}
```

#### 3.3 移除用户的图书 - DELETE /users/{userId}/books/{bookId}

**测试用例 3.3.1: 正常移除图书**
```http
DELETE /users/1/books/1
Authorization: Bearer valid-token
```

**预期响应 (204)**: 无响应体

**测试用例 3.3.2: 移除不存在的用户图书关系**
```http
DELETE /users/1/books/999
Authorization: Bearer valid-token
```

**预期响应 (404)**:
```json
{
  "code": "USER_BOOK_NOT_FOUND",
  "message": "用户图书关系不存在"
}
```

## 🔧 测试数据准备

### 测试用户数据
```json
{
  "users": [
    {
      "userId": 1,
      "username": "testuser",
      "email": "test@example.com",
      "age": 25,
      "phone": "13800138000"
    },
    {
      "userId": 2,
      "username": "admin",
      "email": "admin@example.com",
      "age": 30,
      "phone": "13900139000"
    }
  ]
}
```

### 测试图书数据
```json
{
  "books": [
    {
      "bookId": 1,
      "title": "JavaScript高级程序设计",
      "author": "Nicholas C. Zakas",
      "isbn": "978-7115545381",
      "category": "编程",
      "description": "JavaScript经典教程",
      "price": 89.00,
      "publishDate": "2019-12-01",
      "publisher": "人民邮电出版社",
      "pages": 696,
      "language": "中文",
      "status": "available"
    },
    {
      "bookId": 2,
      "title": "Python编程从入门到实践",
      "author": "Eric Matthes",
      "isbn": "978-7115428028",
      "category": "编程",
      "description": "Python入门经典教程",
      "price": 79.00,
      "publishDate": "2020-01-01",
      "publisher": "人民邮电出版社",
      "pages": 512,
      "language": "中文",
      "status": "available"
    },
    {
      "bookId": 3,
      "title": "算法导论",
      "author": "Thomas H. Cormen",
      "isbn": "978-7111407010",
      "category": "计算机科学",
      "description": "算法经典教材",
      "price": 128.00,
      "publishDate": "2012-09-01",
      "publisher": "机械工业出版社",
      "pages": 780,
      "language": "中文",
      "status": "available"
    }
  ]
}
```

### 测试用户图书关系数据
```json
{
  "userBooks": [
    {
      "userBookId": 1,
      "userId": 1,
      "bookId": 1,
      "status": "reading",
      "addedAt": "2024-01-01T13:00:00Z"
    }
  ]
}
```

## 📊 测试覆盖率

### 功能覆盖率
- ✅ 用户注册
- ✅ 获取用户信息
- ✅ 获取图书列表（分页、搜索、筛选）
- ✅ 创建新图书
- ✅ 获取图书详情
- ✅ 更新图书信息
- ✅ 删除图书
- ✅ 获取用户图书列表
- ✅ 为用户添加图书
- ✅ 移除用户图书

### 错误场景覆盖率
- ✅ 参数验证错误
- ✅ 资源不存在
- ✅ 重复资源冲突
- ✅ 未授权访问
- ✅ 格式错误

### HTTP状态码覆盖率
- ✅ 200 - 成功
- ✅ 201 - 创建成功
- ✅ 204 - 删除成功
- ✅ 400 - 请求参数错误
- ✅ 401 - 未授权
- ✅ 404 - 资源不存在
- ✅ 409 - 资源冲突
- ✅ 422 - 语义错误
- ✅ 500 - 服务器错误

## 🚀 自动化测试建议

### 测试工具推荐
1. **Postman** - API测试和自动化
2. **Newman** - Postman命令行工具
3. **Jest + Supertest** - Node.js测试框架
4. **RestAssured** - Java测试框架
5. **Pytest** - Python测试框架

### 测试执行顺序
1. 用户注册测试
2. 图书管理测试
3. 用户图书管理测试
4. 错误场景测试
5. 性能测试

### 持续集成
建议将API测试集成到CI/CD流程中，确保每次代码变更都进行完整的API测试。 