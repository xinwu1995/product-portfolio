# 个人作品集首页 Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 完成作品集首页，结构为：封面 → 简历 → 项目，导航只保留「项目」「简历」，视觉风格沿用现有 demo。

**Architecture:** 单文件 `index.html`，内联所有 CSS 和 JS，无外部依赖（除 Google Fonts）。在现有 demo 基础上修改，不重建。

**Tech Stack:** 纯 HTML5 / CSS3 / Vanilla JS，Google Fonts（Inter）

---

## 文件结构

```
Desktop/作品集/
├── index.html          ← 唯一修改文件，首页所有内容
└── assets/
    └── images/         ← 创建空目录，备用（放项目截图）
```

---

## Task 1：修复导航——去掉「联系」链接

**文件：**
- 修改：`/Users/admin/Desktop/作品集/index.html`（第 602–604 行）

- [ ] **Step 1：找到导航胶囊，删除「联系」链接**

将第 601–605 行的 `.nav-links` 内容从：
```html
<div class="nav-links">
  <a href="#projects">项目</a>
  <a href="#resume">简历</a>
  <a href="#contact">联系</a>
</div>
```
改为：
```html
<div class="nav-links">
  <a href="#projects">项目</a>
  <a href="#resume">简历</a>
</div>
```

- [ ] **Step 2：浏览器验证**

打开 `/Users/admin/Desktop/作品集/index.html`，检查：
- 导航胶囊只有「项目」「简历」两个选项 ✓
- 右侧「联系我」按钮仍在 ✓
- 点击「项目」平滑滚动到项目区块 ✓
- 点击「简历」平滑滚动到简历区块 ✓

---

## Task 2：调整区块顺序——简历移到项目前面

**文件：**
- 修改：`/Users/admin/Desktop/作品集/index.html`（第 674–855 行）

**背景：** 当前顺序是 Hero → 项目（674 行）→ 简历（767 行）。需要改为 Hero → 简历 → 项目。

- [ ] **Step 1：将简历区块整体剪切，粘贴到项目区块前面**

找到第 765 行结束的 `</section>`（项目区块结尾）和第 767 行开始的 `<div class="resume-section"`（简历区块开头）。

将整个简历区块（从 `<div class="resume-section" id="resume">` 到其对应 `</div>`，约第 767–856 行）**移动到**项目区块（`<section id="projects">` 开头，约第 674 行）**之前**。

结构变为：
```html
<!-- 封面 Hero -->
...封面内容...

<!-- 简历（移到这里） -->
<div class="resume-section" id="resume">
  ...简历内容...
</div>

<!-- 项目（后移到这里） -->
<section id="projects">
  ...项目内容...
</section>
```

- [ ] **Step 2：浏览器验证**

刷新页面，检查：
- 页面从上到下顺序：封面 → 简历 → 项目 ✓
- 点击导航「简历」跳到简历区块 ✓
- 点击导航「项目」跳到项目区块 ✓

---

## Task 3：完善简历区块视觉

**文件：**
- 修改：`/Users/admin/Desktop/作品集/index.html`（简历区块 CSS + HTML）

- [ ] **Step 1：给简历区块加上与 Hero 的视觉过渡**

在 `.resume-section` 的 CSS 样式里，增加顶部内边距，使其和封面的分隔更自然：
```css
.resume-section {
  background: white;
  border-top: 1px solid var(--border);
}
```
确认这行已存在（约第 468 行），如不存在则添加。

- [ ] **Step 2：确保「下载简历」按钮样式正确**

找到简历区块内的「下载简历」按钮，确认它使用 `.btn-secondary` 样式：
```html
<a href="#" class="btn-secondary">
  <svg width="14" height="14" viewBox="0 0 14 14" fill="none">
    <path d="M7 1v8M4 6l3 3 3-3M2 11h10" stroke="currentColor" stroke-width="1.6" stroke-linecap="round" stroke-linejoin="round"/>
  </svg>
  下载简历
</a>
```

- [ ] **Step 3：浏览器验证**

检查简历区块：
- 白色背景，与上下区块有明显分隔 ✓
- 左栏：简介文字 + 技能标签 ✓
- 右栏：绿色节点时间轴 ✓
- 右上角有「下载简历」按钮 ✓

---

## Task 4：完善项目缩略区块

**文件：**
- 修改：`/Users/admin/Desktop/作品集/index.html`（项目区块 HTML）

- [ ] **Step 1：去除项目卡片的跳转链接**

当前项目卡片是 `<a href="#" class="project-card">`，暂时不做跳转，将 `href="#"` 改为 `href="javascript:void(0)"` 防止点击后页面跳到顶部：

```html
<a href="javascript:void(0)" class="project-card">
```

对全部 4 张卡片都做这个修改。

- [ ] **Step 2：给项目区块加上浅灰背景**

确认项目区块的 `<section id="projects">` 外层有背景色，或在 CSS 里为 `#projects` 添加：
```css
#projects {
  background: var(--bg);
}
```

- [ ] **Step 3：浏览器验证**

检查项目区块：
- 4 张卡片 2 列排布 ✓
- 每张卡片有渐变色缩略图、标签、标题、描述 ✓
- 鼠标悬停时卡片上浮、阴影加深、箭头变黑 ✓
- 点击卡片不会跳转或滚动到顶部 ✓

---

## Task 5：移动端基础适配

**文件：**
- 修改：`/Users/admin/Desktop/作品集/index.html`（CSS 末尾添加媒体查询）

- [ ] **Step 1：在 `</style>` 之前添加响应式样式**

```css
@media (max-width: 768px) {
  nav {
    padding: 0 20px;
  }
  .nav-links {
    display: none;
  }
  .hero-inner {
    grid-template-columns: 1fr;
    gap: 40px;
    padding: 0 20px;
  }
  .hero-visual {
    order: -1;
  }
  .hero h1 {
    font-size: 2.6rem;
  }
  section {
    padding: 60px 20px;
  }
  .resume-grid {
    grid-template-columns: 1fr;
    gap: 40px;
  }
  .timeline-item {
    grid-template-columns: 80px 1fr;
    gap: 16px;
  }
  .timeline-item::before {
    left: 80px;
  }
  .timeline-dot {
    left: 74px;
  }
  .projects-grid {
    grid-template-columns: 1fr;
  }
  footer {
    padding: 32px 20px;
    flex-direction: column;
    gap: 16px;
    align-items: flex-start;
  }
  .section-header {
    flex-direction: column;
    align-items: flex-start;
    gap: 16px;
  }
}
```

- [ ] **Step 2：浏览器验证（模拟手机）**

在 Chrome 中按 `F12` 打开开发者工具，点击左上角手机图标切换移动端视图（选 iPhone 12 Pro，宽度 390px），检查：
- 导航不溢出，胶囊隐藏 ✓
- Hero 单列排布，头像卡在上，文字在下 ✓
- 简历单列排布 ✓
- 项目卡片单列排布 ✓

---

## Task 6：最终检查与收尾

**文件：**
- 修改：`/Users/admin/Desktop/作品集/index.html`（标题、footer）

- [ ] **Step 1：更新页面 `<title>` 标签**

确认 `<title>` 使用占位名字，格式为：
```html
<title>张明 · 产品设计师</title>
```
（真实姓名由用户后续替换）

- [ ] **Step 2：确认 footer 存在且内容完整**

确认页面底部有 footer，包含：
```html
<footer id="contact">
  <div>
    <div class="footer-name">张明</div>
    <div class="footer-copy">产品设计师 · 北京</div>
  </div>
  <div class="footer-links">
    <a href="#">GitHub</a>
    <a href="#">LinkedIn</a>
    <a href="#">Dribbble</a>
    <a href="mailto:hello@example.com">邮箱</a>
  </div>
</footer>
```

- [ ] **Step 3：创建 assets 目录备用**

```bash
mkdir -p /Users/admin/Desktop/作品集/assets/images
```

- [ ] **Step 4：完整浏览器通读**

打开页面，从头到尾滚动一遍，逐项确认：
- [ ] 导航固定在顶部，滚动后出现底部边框
- [ ] 导航胶囊只有「项目」「简历」
- [ ] 封面：badge、大标题、描述、两个按钮、统计数字、个人卡片
- [ ] 简历：白色背景、左侧技能、右侧时间轴绿色节点
- [ ] 项目：4 张卡片 2 列、悬停动效正常
- [ ] footer 可见
- [ ] 无明显错位或样式异常
