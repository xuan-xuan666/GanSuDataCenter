# 甘肃文旅融合科学数据中心

基于 Vue 3 的科学数据中心网页项目

## 项目结构

```
甘肃文旅融合科学数据中心/
├── index.html
├── package.json
├── vite.config.js
├── src/
│   ├── main.js
│   ├── App.vue
│   ├── assets/
│   │   └── style.css
│   └── components/
│       ├── HeaderComponent.vue
│       ├── CarouselComponent.vue
│       ├── DataOverviewComponent.vue
│       ├── NewsSectionComponent.vue
│       ├── DataResourcesComponent.vue
│       ├── SpecialTopicComponent.vue
│       ├── ModelResourcesComponent.vue
│       ├── SciencePopularizationComponent.vue
│       ├── PlatformIntroComponent.vue
│       └── FooterComponent.vue
├── html/
├── css/
└── js/
```

## 安装和运行

### 安装依赖
```bash
npm install
```

### 开发模式运行
```bash
npm run dev
```

### 生产环境构建
```bash
npm run build
```

### 预览生产构建
```bash
npm run preview
```

## 功能特性

- **响应式设计**：支持不同屏幕尺寸
- **轮播图**：自动轮播展示图片
- **核心数据总览**：动态数字面板和实时数据
- **客流热力图**：可视化展示景区客流
- **天气监测**：实时天气和预警信息
- **最新数据更新**：滚动显示最新入库数据

## 技术栈

- Vue 3.2.45
- Vite 4.0.0
- HTML5
- CSS3

## 许可证

ISC
