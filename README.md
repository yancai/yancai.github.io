个人页面: [https://yancai.github.io](https://yancai.github.io)  
此为 hexo 博客源码仓库（hexo 分支），master 分支存储生成的静态页面。


## 环境要求

- Node.js >= 20.19.0
- npm


## 快速开始

### 1. 克隆代码并切换分支

```bash
git clone git@github.com:yancai/yancai.github.io.git
cd yancai.github.io
git checkout hexo
```

### 2. 安装依赖

```bash
npm install
```

### 3. 本地预览

```bash
npx hexo clean    # 清理缓存
npx hexo server   # 启动本地服务，默认 http://localhost:4000
```

### 4. 生成静态文件

```bash
npx hexo generate
```

生成的文件在 `public/` 目录下。

### 5. 发布到 GitHub Pages

```bash
npx hexo deploy
```

此命令会将生成的静态文件推送到 master 分支。


## 常用命令

| 命令 | 说明 |
|------|------|
| `npx hexo new <title>` | 新建文章 |
| `npx hexo clean` | 清理缓存文件 |
| `npx hexo generate` | 生成静态文件 |
| `npx hexo server` | 启动本地预览服务 |
| `npx hexo deploy` | 发布到 GitHub Pages |


## 目录结构

```
.
├── _config.yml          # Hexo 主配置
├── _config.next.yml     # NexT 主题配置
├── package.json         # 依赖管理
├── scaffolds/           # 文章模板
├── source/              # 源码目录
│   ├── _posts/          # 文章 (Markdown)
│   ├── tags/            # 标签页
│   └── README.md        # 主页展示的说明
└── themes/              # 主题目录 (已废弃，通过 npm 管理)
```
