# 项目详情页 + 管理后台 设计文档

**日期：** 2026-04-18
**状态：** 待开发
**技术：** 纯 HTML / CSS / Vanilla JS，GitHub API
**部署：** GitHub Pages

---

## 一、目标

为作品集网站新增：
1. **项目展示页**（`project.html`）——访客通过 URL 查看项目详情
2. **管理后台**（`admin.html`）——作者配置项目内容，通过 GitHub API 发布

---

## 二、整体架构

### 文件结构

```
作品集/
├── index.html              ← 首页（已有）
├── project.html            ← 4 个项目共用的展示模板
├── admin.html              ← 管理后台
├── data/
│   ├── projects.json       ← 项目列表（id、标题、首页卡片信息）
│   ├── project-1.json      ← 项目 1 内容
│   ├── project-2.json      ← 项目 2 内容
│   ├── project-3.json      ← 项目 3 内容
│   └── project-4.json      ← 项目 4 内容
└── assets/
    └── images/
        └── projects/       ← 项目图片（管理页上传）
```

### 数据流

```
管理页填写内容 → 点「发布」
→ GitHub API 写入 data/project-N.json + 上传图片到 assets/images/projects/
→ GitHub Pages 自动重建（约 1-2 分钟）
→ 访客打开 project.html?id=N → JS 读取 JSON → 渲染内容块
```

### URL 设计

| 页面 | URL |
|------|-----|
| 首页 | `/index.html` |
| 项目 1 | `/project.html?id=1` |
| 项目 2 | `/project.html?id=2` |
| 管理后台 | `/admin.html` |

---

## 三、数据结构

### projects.json（项目列表）

```json
[
  {
    "id": 1,
    "title": "项目名称",
    "type": "UX 设计",
    "year": "2024",
    "desc": "一句话简介",
    "thumb": "assets/images/projects/p1-thumb.jpg"
  }
]
```

### project-N.json（项目内容）

```json
{
  "id": 1,
  "title": "项目名称",
  "blocks": [
    { "id": "b1", "type": "heading", "level": 1, "content": "项目背景" },
    { "id": "b2", "type": "body", "content": "正文内容..." },
    { "id": "b3", "type": "quote", "content": "关键洞察或数据" },
    { "id": "b4", "type": "image-single", "src": "assets/images/projects/p1-img1.jpg", "caption": "图片说明" },
    { "id": "b5", "type": "image-carousel", "images": ["...jpg", "...jpg"], "captions": ["", ""] },
    { "id": "b6", "type": "image-stack", "images": ["...jpg", "...jpg"] },
    { "id": "b7", "type": "image-text", "layout": "left", "image": "...jpg", "caption": "", "text": "..." },
    { "id": "b8", "type": "video", "videoType": "upload", "src": "assets/videos/p1-demo.mp4" },
    { "id": "b9", "type": "video", "videoType": "embed", "url": "https://www.bilibili.com/video/xxx" }
  ]
}
```

---

## 四、项目展示页（project.html）

### 布局结构

```
固定顶部导航栏（← 返回首页 | 项目标题）
─────────────────────────────────────
内容区（max-width: 800px，居中）
  Block 1
  Block 2
  Block 3
  ...
─────────────────────────────────────
底部导航（← 上一个项目 | 下一个项目 →）
```

### 各块渲染规范

| 块类型 | 渲染方式 |
|--------|---------|
| `heading` level 1/2/3 | `<h1>` / `<h2>` / `<h3>`，字重和字号递减 |
| `body` | `<p>`，14px，行高 1.8，灰色 |
| `quote` | 左侧 4px 绿色竖线，字号 16px，斜体，浅灰背景 |
| `image-single` | 100% 宽，圆角 12px，caption 居中灰字 |
| `image-carousel` | 100% 宽图片区，左右箭头，底部圆点指示器，支持键盘左右键 |
| `image-stack` | 图片垂直排列，间距 12px，每张 100% 宽 |
| `image-text` | 两列 grid，图片占 50%，文字占 50%；`layout: "left"` 图在左，`layout: "right"` 图在右 |
| `video` upload | `<video>` 标签，controls，100% 宽 |
| `video` embed | `<iframe>` 嵌入，16:9 比例，B站/YouTube 自动识别 |

### 交互

- **图片点击放大**：点击任意图片，全屏 lightbox，点击空白或按 Esc 关闭
- **轮播**：左右箭头点击切换，支持键盘方向键
- **顶部导航**：滚动超过 10px 显示底部细边框（沿用首页导航样式）

---

## 五、管理后台（admin.html）

### 整体布局

```
顶部栏：「作品集管理」标题 | GitHub 设置按钮
─────────────────────────────────────
左侧边栏（240px）：4 个项目列表，点击切换
─────────────────────────────────────
右侧主区：
  当前项目名称编辑框
  ┌─────────────────────────────┐
  │  块 1  [类型标签] [↑][↓][删]  │
  │  编辑区                      │
  ├─────────────────────────────┤
  │  块 2  ...                   │
  └─────────────────────────────┘
  [+ 添加块] 按钮
─────────────────────────────────────
底部栏：[预览] [发布] 按钮
```

### GitHub 配置

首次使用弹出设置框，填写：
- GitHub 用户名
- 仓库名（如 `portfolio`）
- GitHub Personal Access Token（需要 `contents:write` 权限）

配置保存在 `localStorage`，后续无需重填。

### 块编辑器

每种块类型有对应编辑 UI：

| 块类型 | 编辑 UI |
|--------|---------|
| heading | 下拉选 H1/H2/H3 + 文本输入框 |
| body | 多行文本框（textarea） |
| quote | 单行文本框 |
| image-single | 图片上传按钮 + caption 输入框 |
| image-carousel | 多图上传（可添加多张）+ 每张 caption |
| image-stack | 多图上传（可添加多张） |
| image-text | 图片上传 + 左/右布局选择 + 文本框 |
| video | 切换「上传文件 / 粘贴链接」+ 对应输入 |

### 排序

块列表支持**上移 / 下移**按钮排序（不做拖拽，保持简单）。

### 图片上传流程

1. 用户在编辑器点「上传图片」，选择本地文件
2. 前端读取文件，转为 base64
3. 点「发布」时，通过 GitHub API `PUT /repos/{owner}/{repo}/contents/assets/images/projects/{filename}` 上传
4. JSON 中记录图片路径（相对路径）

### 发布流程

1. 点「发布」按钮
2. 依次：上传所有待上传图片 → 写入 `data/project-N.json` → 写入 `data/projects.json`
3. 显示进度状态（上传中… / 发布成功 / 发布失败）
4. 成功后显示线上链接，可一键打开预览

---

## 六、视觉规范

沿用首页已有设计系统：
- 字体：Inter（Google Fonts）
- 主色：`#10b981`（绿色）
- 背景：`#fafafa`
- 白色卡片：`#ffffff`，圆角 `20px`，边框 `#e4e4e7`
- 内容区最大宽度：`800px`，左右 padding `40px`

管理后台视觉比展示页更实用，采用简洁的工具型界面，不需要和首页完全一致。

---

## 七、首页联动

`index.html` 的 4 张项目卡片，`href` 从 `javascript:void(0)` 改为 `/project.html?id=N`，缩略图和标题从 `data/projects.json` 读取（可选：首期直接硬编码，后续再联动）。
