# 柚子软件库 - uni-app版本

## 📱 项目介绍
基于uni-app开发的跨平台应用，支持打包为Android、iOS、微信小程序等多个平台。

## 🚀 快速开始

### 环境要求
- HBuilderX 3.0+
- Node.js 12+
- 微信开发者工具（小程序调试）

### 导入项目
1. 打开HBuilderX
2. 文件 → 导入 → 从本地目录导入
3. 选择 `youz-app-uniapp` 文件夹
4. 项目类型选择：uni-app

### 运行调试
- **浏览器预览**: 运行 → 运行到浏览器
- **微信小程序**: 运行 → 运行到小程序模拟器 → 微信开发者工具
- **Android真机**: 运行 → 运行到手机或模拟器 → Android
- **iOS真机**: 运行 → 运行到手机或模拟器 → iOS

### 云端打包
1. 发行 → 原生App-云端打包
2. 选择平台（Android/iOS）
3. 配置证书和签名信息
4. 等待打包完成

## 📁 项目结构

```
youz-app-uniapp/
├── pages/                 # 页面文件
│   └── index/
│       └── index.vue     # 首页
├── static/               # 静态资源
│   ├── css/             # 样式文件
│   └── icons/           # 图标文件
├── components/           # 组件目录
├── App.vue              # 应用主组件
├── main.js              # 入口文件
├── manifest.json        # 应用配置
├── pages.json           # 页面路由配置
├── uni.scss             # 全局样式变量
└── README.md            # 项目说明
```

## 🎨 主要特性

- ✅ **跨平台支持** - 一套代码多端运行
- ✅ **Vue 3 Composition API** - 现代化开发体验
- ✅ **响应式设计** - 完美适配各种屏幕尺寸
- ✅ **组件化架构** - 易于维护和扩展
- ✅ **TypeScript支持** - 类型安全（可选）
- ✅ **主题定制** - 支持深色模式和自定义主题
- ✅ **国际化** - 多语言支持框架

## 📋 页面功能

### 首页 (index.vue)
- 🔍 智能搜索功能
- 📱 应用分类筛选
- 📄 分页加载
- 🎨 现代化UI设计
- ⬇️ 原生下载支持

## 🛠 开发指南

### 添加新页面
1. 在 `pages/` 目录下创建页面文件
2. 在 `pages.json` 中注册页面路由
3. 使用 `uni.navigateTo()` 进行页面跳转

### 添加新组件
1. 在 `components/` 目录下创建组件
2. 在页面中导入并使用组件

### 样式开发
- 使用 `uni.scss` 中的样式变量
- 遵循BEM命名规范
- 支持SCSS预处理器

### API调用
```javascript
// 网络请求
uni.request({
  url: 'https://api.example.com/data',
  method: 'GET',
  success: (res) => {
    console.log(res.data);
  }
});

// 本地存储
uni.setStorageSync('key', 'value');
const value = uni.getStorageSync('key');
```

## 📦 打包发布

### Android打包
1. 申请Android签名证书
2. 在manifest.json中配置包名和版本
3. 云端打包生成APK文件

### iOS打包
1. 申请Apple开发者账号
2. 配置iOS证书和描述文件
3. 云端打包生成IPA文件

### 小程序打包
1. 申请小程序账号
2. 配置AppID
3. 上传代码并提交审核

## 🤝 贡献指南

1. Fork本项目
2. 创建特性分支
3. 提交代码变更
4. 创建Pull Request

## 📄 许可证

本项目采用MIT许可证，详见LICENSE文件。

## 📞 联系我们

如有问题或建议，请提交Issue或联系开发团队。