# AiName-front - 取名小助手前端项目

AiName-front是一个基于Vue.js和Uni-app框架开发的移动应用前端项目，为用户提供智能取名服务。

## 技术栈

- **框架**：Vue.js (支持Vue2/Vue3)
- **开发框架**：Uni-app
- **构建工具**：Vite
- **UI组件**：Uni-app内置组件
- **网络请求**：封装的uni.request

## 项目结构

```
AiName-front/
├── http/
│   └── http.js                # 网络请求封装
├── pages/
│   ├── index/                 # 首页（取名功能）
│   │   └── index.vue
│   ├── login/                 # 登录页面
│   │   └── login.vue
│   └── register/              # 注册页面
│       └── register.vue
├── static/
│   └── logo.png              # 静态资源
├── App.vue                   # 应用根组件
├── main.js                   # 应用入口文件
├── manifest.json             # 应用配置文件
├── pages.json                # 页面路由配置
├── uni.promisify.adaptor.js  # Promise适配器
├── uni.scss                  # 全局样式
└── vite.config.js            # Vite配置
```

## 功能模块

### 1. 用户认证
- **注册功能**：支持邮箱注册，包含验证码验证
- **登录功能**：邮箱密码登录，登录后存储Token

### 2. 取名功能
- 生成名字（核心功能，在首页实现）

### 3. 网络请求
- 封装了统一的请求接口
- 支持Token认证
- 环境配置区分开发和生产

## 安装和运行

### 开发环境

1. **安装依赖**
   ```bash
   npm install
   ```

2. **运行开发服务器**
   ```bash
   npm run dev
   ```

3. **构建生产版本**
   ```bash
   npm run build
   ```

### HBuilderX环境

1. 下载并安装 [HBuilderX](https://www.dcloud.io/hbuilderx.html)
2. 导入项目
3. 选择运行平台（微信小程序、H5、App等）
4. 点击运行按钮

## 开发环境配置

### 代理配置

在 `vite.config.js` 中配置了代理：

```javascript
server: {
  port: 5173,
  proxy: {
    '/api': {
      target: 'http://127.0.0.1:8000', // 目标后端地址
      changeOrigin: true, // 允许跨域
      rewrite: (path) => path.replace(/^\/api/, '')
    }
  }
}
```

### 网络请求配置

在 `http/http.js` 中配置了API地址：

```javascript
let BASE_URL = "";
if(process.env.NODE_ENV === "development"){
    BASE_URL = "/api";
}else{
    BASE_URL = "https://www.baidu.com"; // 生产环境API地址
}
```

## 接口说明

### 用户相关接口

- `POST /auth/register` - 用户注册
- `GET /auth/code` - 获取邮箱验证码
- `POST /auth/login` - 用户登录

### 取名相关接口

- `POST /name/` - 生成名字

## 注意事项

1. 开发环境下需确保后端服务运行在 `http://127.0.0.1:8000`
2. 生产环境需修改 `http/http.js` 中的BASE_URL
3. 项目支持多端编译，可在HBuilderX中选择不同运行平台
4. 登录成功后，Token会存储在本地存储中

## License

MIT