# 🐍 SvelteKit 贪吃蛇游戏

[English](README.md) | [简体中文](README_zh.md)

一个使用 SvelteKit 和 TypeScript 构建的经典贪吃蛇游戏。这个现代化的实现具有流畅的动画、递进式难度和本地最高分记录功能。

![贪吃蛇游戏](https://img.shields.io/badge/SvelteKit-v2.22.0-ff3c00?style=flat-square&logo=svelte)
![TypeScript](https://img.shields.io/badge/TypeScript-v5.0.0-blue?style=flat-square&logo=typescript)
![Svelte](https://img.shields.io/badge/Svelte-v5.0.0-ff3c00?style=flat-square&logo=svelte)

## 🎮 功能特性

- **经典玩法**：传统贪吃蛇机制，现代化精装
- **响应式控制**：支持 WASD 和方向键
- **递进式难度**：收集食物越多，游戏速度越快
- **计分系统**：追踪当前分数和持久化最高分
- **暂停/继续**：随时按空格键暂停游戏
- **流畅动画**：食物脉动效果和流畅的蛇身移动
- **响应式设计**：适配桌面和移动设备
- **本地存储**：最高分本地保存

## 🚀 快速开始

### 前置要求

- Node.js（v16 或更高版本）
- npm、yarn 或 pnpm

### 安装步骤

1. 克隆仓库：
```bash
git clone https://github.com/zym9863/sveltekit-snake.git
cd sveltekit-snake
```

2. 安装依赖：
```bash
npm install
# 或
pnpm install
# 或
yarn install
```

3. 启动开发服务器：
```bash
npm run dev
# 或启动并在浏览器中打开
npm run dev -- --open
```

## 🎯 游戏玩法

1. **开始**：点击"开始游戏"按钮或按空格键开始
2. **控制**：使用 WASD 或方向键控制蛇的移动
   - **W/↑**：向上移动
   - **S/↓**：向下移动
   - **A/←**：向左移动
   - **D/→**：向右移动
3. **暂停**：游戏过程中按空格键暂停/继续
4. **目标**：吃掉红色食物来增长蛇身并增加分数
5. **避免**：不要撞到墙壁或自己的尾巴！

## 🏗️ 项目结构

```
sveltekit-snake/
├── src/
│   ├── lib/
│   │   └── Snake.svelte    # 主游戏组件
│   ├── routes/
│   │   ├── +layout.svelte  # 根布局
│   │   └── +page.svelte    # 主页
│   └── app.d.ts           # TypeScript 声明
├── static/                 # 静态资源
├── package.json           # 项目依赖
├── svelte.config.js       # SvelteKit 配置
├── tsconfig.json          # TypeScript 配置
└── vite.config.ts         # Vite 配置
```

## 🛠️ 技术细节

### 游戏配置

- **网格大小**：20x20 格子
- **格子大小**：20px
- **初始速度**：每步 150ms
- **速度递增**：每吃一个食物快 10ms
- **最快速度**：50ms（最高难度）

### 核心技术

- **SvelteKit**：用于构建 Web 应用的全栈框架
- **TypeScript**：类型安全的 JavaScript，提供更好的开发体验
- **Svelte 5**：具有增强响应性的最新版本
- **Vite**：快速的构建工具和开发服务器

## 📦 生产构建

创建生产版本：

```bash
npm run build
```

预览生产版本：

```bash
npm run preview
```

## 🚢 部署

本项目默认使用自动适配器，它会自动检测并使用适合您部署平台的正确适配器。对于特定的部署目标，您可能需要安装和配置不同的[适配器](https://svelte.dev/docs/kit/adapters)。

### 部署选项示例：

- **Vercel**：安装 `@sveltejs/adapter-vercel`
- **Netlify**：安装 `@sveltejs/adapter-netlify`
- **Node.js**：安装 `@sveltejs/adapter-node`
- **静态站点**：安装 `@sveltejs/adapter-static`

## 📝 脚本命令

- `npm run dev` - 启动开发服务器
- `npm run build` - 构建生产版本
- `npm run preview` - 预览生产版本
- `npm run check` - 运行 svelte-check 进行类型检查
- `npm run check:watch` - 监听模式下运行 svelte-check

## 🤝 贡献

欢迎贡献！请随时提交 Pull Request。

## 📄 许可证

本项目是开源的，基于 [MIT 许可证](LICENSE)。

## 🎨 未来增强功能

- [ ] 多种难度级别
- [ ] 不同的游戏模式
- [ ] 道具和特殊食物
- [ ] 多人游戏支持
- [ ] 移动端触摸控制
- [ ] 音效和背景音乐
- [ ] 排行榜系统
- [ ] 主题自定义
