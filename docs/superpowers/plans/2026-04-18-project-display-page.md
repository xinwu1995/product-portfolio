# 项目展示页 Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 构建 `project.html` 项目详情展示模板，读取 JSON 数据动态渲染 8 种内容块，支持图片放大、轮播、上下翻页导航。

**Architecture:** 单文件 `project.html`（内联 CSS + JS），通过 URL 参数 `?id=N` 确定项目，`fetch()` 读取 `data/project-N.json` 渲染内容块。项目列表存于 `data/projects.json`，用于前后翻页导航。

**Tech Stack:** 纯 HTML5 / CSS3 / Vanilla JS，无外部依赖（除 Google Fonts）。

---

## 文件结构

```
作品集/
├── project.html                    ← 新建：展示模板
├── data/
│   ├── projects.json               ← 新建：项目列表（id、标题、类型、年份、简介）
│   ├── project-1.json              ← 新建：项目 1 内容块
│   ├── project-2.json              ← 新建：项目 2 内容块
│   ├── project-3.json              ← 新建：项目 3 内容块
│   └── project-4.json              ← 新建：项目 4 内容块
└── index.html                      ← 修改：项目卡片 href 指向 project.html?id=N
```

---

## Task 1：创建数据文件

**文件：**
- 新建：`/Users/admin/Desktop/作品集/data/projects.json`
- 新建：`/Users/admin/Desktop/作品集/data/project-1.json`
- 新建：`/Users/admin/Desktop/作品集/data/project-2.json`
- 新建：`/Users/admin/Desktop/作品集/data/project-3.json`
- 新建：`/Users/admin/Desktop/作品集/data/project-4.json`

- [ ] **Step 1：创建 `data/projects.json`**

```json
[
  {
    "id": 1,
    "title": "AI 课堂 · 奇思妙问",
    "type": "UX 设计",
    "year": "2023",
    "desc": "国内首家数字人课堂，服务 2w+ 所学校",
    "thumb": ""
  },
  {
    "id": 2,
    "title": "必胜客 APP & 小程序",
    "type": "交互设计",
    "year": "2020",
    "desc": "累计会员 1.3 亿的餐饮数字化产品",
    "thumb": ""
  },
  {
    "id": 3,
    "title": "SaaS 教务平台",
    "type": "产品设计",
    "year": "2022",
    "desc": "服务 303 个区域、1w+ 学校的教育 SaaS",
    "thumb": ""
  },
  {
    "id": 4,
    "title": "智能导师 · openclaw",
    "type": "UX 设计",
    "year": "2024",
    "desc": "让教师学会像教学专家一样教学",
    "thumb": ""
  }
]
```

- [ ] **Step 2：创建 `data/project-1.json`（包含所有 8 种块类型的示例）**

```json
{
  "id": 1,
  "title": "AI 课堂 · 奇思妙问",
  "blocks": [
    {
      "id": "b1",
      "type": "heading",
      "level": 1,
      "content": "项目背景"
    },
    {
      "id": "b2",
      "type": "body",
      "content": "这里填写项目背景介绍。可以换行。\n第二行内容。"
    },
    {
      "id": "b3",
      "type": "quote",
      "content": "这里放关键数据或用户洞察，比如：国内首家推出数字人课堂，2024 年被总理表扬并登上新闻联播。"
    },
    {
      "id": "b4",
      "type": "heading",
      "level": 2,
      "content": "设计目标"
    },
    {
      "id": "b5",
      "type": "body",
      "content": "这里填写设计目标说明。"
    },
    {
      "id": "b6",
      "type": "image-single",
      "src": "assets/images/projects/placeholder.jpg",
      "caption": "图片说明文字"
    },
    {
      "id": "b7",
      "type": "heading",
      "level": 2,
      "content": "设计过程"
    },
    {
      "id": "b8",
      "type": "image-carousel",
      "images": [
        "assets/images/projects/placeholder.jpg",
        "assets/images/projects/placeholder.jpg"
      ],
      "captions": ["界面截图 1", "界面截图 2"]
    },
    {
      "id": "b9",
      "type": "image-text",
      "layout": "left",
      "image": "assets/images/projects/placeholder.jpg",
      "caption": "",
      "text": "这里填写配图说明文字。可以多行。\n适合用来解释某张截图的设计思路。"
    },
    {
      "id": "b10",
      "type": "heading",
      "level": 2,
      "content": "最终方案"
    },
    {
      "id": "b11",
      "type": "image-stack",
      "images": [
        "assets/images/projects/placeholder.jpg",
        "assets/images/projects/placeholder.jpg"
      ]
    },
    {
      "id": "b12",
      "type": "heading",
      "level": 2,
      "content": "项目演示"
    },
    {
      "id": "b13",
      "type": "video",
      "videoType": "embed",
      "url": "https://www.bilibili.com/video/BV1xx411c7mD"
    }
  ]
}
```

- [ ] **Step 3：创建 `data/project-2.json`**

```json
{
  "id": 2,
  "title": "必胜客 APP & 小程序",
  "blocks": [
    { "id": "b1", "type": "heading", "level": 1, "content": "项目背景" },
    { "id": "b2", "type": "body", "content": "这里填写项目背景介绍。" }
  ]
}
```

- [ ] **Step 4：创建 `data/project-3.json`**

```json
{
  "id": 3,
  "title": "SaaS 教务平台",
  "blocks": [
    { "id": "b1", "type": "heading", "level": 1, "content": "项目背景" },
    { "id": "b2", "type": "body", "content": "这里填写项目背景介绍。" }
  ]
}
```

- [ ] **Step 5：创建 `data/project-4.json`**

```json
{
  "id": 4,
  "title": "智能导师 · openclaw",
  "blocks": [
    { "id": "b1", "type": "heading", "level": 1, "content": "项目背景" },
    { "id": "b2", "type": "body", "content": "这里填写项目背景介绍。" }
  ]
}
```

- [ ] **Step 6：创建占位图片**

在 `assets/images/projects/` 目录下创建一个 1px 透明 PNG 作为占位图（避免 404 报错）：

```bash
mkdir -p /Users/admin/Desktop/作品集/assets/images/projects
# 创建一个简单的 SVG 占位图
cat > /Users/admin/Desktop/作品集/assets/images/projects/placeholder.jpg << 'EOF'
<svg xmlns="http://www.w3.org/2000/svg" width="800" height="450" viewBox="0 0 800 450">
  <rect width="800" height="450" fill="#f0f0f0"/>
  <text x="400" y="225" font-family="sans-serif" font-size="24" fill="#aaa" text-anchor="middle" dominant-baseline="middle">图片占位</text>
</svg>
EOF
```

注意：占位图是 SVG 格式但扩展名为 .jpg，浏览器可以正常显示。管理后台完成后会被真实图片替换。

- [ ] **Step 7：提交**

```bash
cd /Users/admin/Desktop/作品集
git add data/ assets/images/projects/
git commit -m "feat: add project data files and placeholder image"
```

---

## Task 2：创建 project.html

**文件：**
- 新建：`/Users/admin/Desktop/作品集/project.html`

- [ ] **Step 1：创建完整的 `project.html`**

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>项目详情 · 吴欣</title>
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700;800&display=swap" rel="stylesheet">
  <style>
    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
    :root {
      --bg: #fafafa;
      --surface: #ffffff;
      --border: #e4e4e7;
      --text-primary: #09090b;
      --text-secondary: #52525b;
      --text-muted: #a1a1aa;
      --accent: #10b981;
      --accent-light: #d1fae5;
      --accent-text: #065f46;
    }
    body {
      font-family: 'Inter', sans-serif;
      background: var(--bg);
      color: var(--text-primary);
      min-height: 100vh;
    }

    /* ── 导航 ── */
    nav {
      position: fixed;
      top: 0; left: 0; right: 0;
      height: 56px;
      display: flex;
      align-items: center;
      justify-content: space-between;
      padding: 0 40px;
      background: rgba(250,250,250,0.85);
      backdrop-filter: blur(12px);
      -webkit-backdrop-filter: blur(12px);
      z-index: 100;
      border-bottom: 1px solid transparent;
      transition: border-color 0.2s;
    }
    nav.scrolled { border-bottom-color: var(--border); }
    .nav-back {
      display: flex;
      align-items: center;
      gap: 6px;
      font-size: 14px;
      font-weight: 500;
      color: var(--text-secondary);
      text-decoration: none;
      transition: color 0.2s;
    }
    .nav-back:hover { color: var(--text-primary); }
    .nav-title {
      font-size: 14px;
      font-weight: 600;
      color: var(--text-primary);
      letter-spacing: -0.02em;
      position: absolute;
      left: 50%;
      transform: translateX(-50%);
    }

    /* ── 内容区 ── */
    main {
      max-width: 800px;
      margin: 0 auto;
      padding: 96px 40px 80px;
    }

    /* ── 通用块间距 ── */
    .block { margin-bottom: 48px; }
    .block:last-child { margin-bottom: 0; }

    /* ── 标题块 ── */
    .block-h1 {
      font-size: 2rem;
      font-weight: 800;
      letter-spacing: -0.04em;
      line-height: 1.15;
      color: var(--text-primary);
    }
    .block-h2 {
      font-size: 1.4rem;
      font-weight: 700;
      letter-spacing: -0.03em;
      line-height: 1.25;
      color: var(--text-primary);
    }
    .block-h3 {
      font-size: 1.1rem;
      font-weight: 600;
      letter-spacing: -0.02em;
      line-height: 1.35;
      color: var(--text-primary);
    }

    /* ── 正文块 ── */
    .block-body {
      font-size: 15px;
      line-height: 1.85;
      color: var(--text-secondary);
    }

    /* ── 引用块 ── */
    .block-quote {
      border-left: 4px solid var(--accent);
      padding: 16px 20px;
      background: #f0fdf4;
      border-radius: 0 8px 8px 0;
      font-size: 16px;
      font-style: italic;
      color: var(--accent-text);
      line-height: 1.7;
    }

    /* ── 单张图片 ── */
    .block-image-single img {
      width: 100%;
      border-radius: 12px;
      display: block;
    }
    .img-caption {
      margin-top: 10px;
      font-size: 13px;
      color: var(--text-muted);
      text-align: center;
      line-height: 1.5;
    }

    /* ── 图片轮播 ── */
    .carousel {
      position: relative;
      overflow: hidden;
      border-radius: 12px;
      background: var(--border);
    }
    .carousel-track {
      display: flex;
      transition: transform 0.3s ease;
    }
    .carousel-track img {
      width: 100%;
      flex-shrink: 0;
      display: block;
      aspect-ratio: 16/9;
      object-fit: cover;
    }
    .carousel-btn {
      position: absolute;
      top: 50%;
      transform: translateY(-50%);
      background: rgba(255,255,255,0.92);
      border: 1px solid var(--border);
      border-radius: 50%;
      width: 36px; height: 36px;
      display: flex; align-items: center; justify-content: center;
      cursor: pointer;
      font-size: 18px;
      line-height: 1;
      transition: background 0.2s;
      z-index: 2;
    }
    .carousel-btn:hover { background: white; }
    .carousel-btn-prev { left: 12px; }
    .carousel-btn-next { right: 12px; }
    .carousel-dots {
      display: flex;
      justify-content: center;
      gap: 6px;
      margin-top: 12px;
    }
    .carousel-dot {
      width: 6px; height: 6px;
      border-radius: 50%;
      background: var(--border);
      cursor: pointer;
      transition: background 0.2s;
    }
    .carousel-dot.active { background: var(--accent); }

    /* ── 图片拼接 ── */
    .block-image-stack img {
      width: 100%;
      display: block;
      border-radius: 12px;
      margin-bottom: 12px;
    }
    .block-image-stack img:last-child { margin-bottom: 0; }

    /* ── 图文混排 ── */
    .block-image-text {
      display: grid;
      grid-template-columns: 1fr 1fr;
      gap: 32px;
      align-items: center;
    }
    .block-image-text.layout-right .it-img { order: 2; }
    .block-image-text.layout-right .it-text { order: 1; }
    .block-image-text img {
      width: 100%;
      border-radius: 12px;
      display: block;
    }
    .it-text {
      font-size: 15px;
      line-height: 1.8;
      color: var(--text-secondary);
    }

    /* ── 视频块 ── */
    .block-video video {
      width: 100%;
      border-radius: 12px;
      display: block;
    }
    .video-embed {
      position: relative;
      padding-bottom: 56.25%;
      height: 0;
      overflow: hidden;
      border-radius: 12px;
    }
    .video-embed iframe {
      position: absolute;
      top: 0; left: 0;
      width: 100%; height: 100%;
      border: none;
    }

    /* ── 底部翻页 ── */
    .project-nav {
      max-width: 800px;
      margin: 0 auto;
      padding: 40px 40px 64px;
      display: flex;
      justify-content: space-between;
      border-top: 1px solid var(--border);
    }
    .project-nav a {
      font-size: 14px;
      font-weight: 500;
      color: var(--text-secondary);
      text-decoration: none;
      display: flex;
      align-items: center;
      gap: 6px;
      transition: color 0.2s;
    }
    .project-nav a:hover { color: var(--text-primary); }
    .project-nav a.hidden { visibility: hidden; }

    /* ── Lightbox ── */
    .lightbox {
      position: fixed;
      inset: 0;
      background: rgba(0,0,0,0.88);
      display: flex;
      align-items: center;
      justify-content: center;
      z-index: 999;
      cursor: zoom-out;
      opacity: 0;
      pointer-events: none;
      transition: opacity 0.2s;
    }
    .lightbox.active {
      opacity: 1;
      pointer-events: all;
    }
    .lightbox img {
      max-width: 90vw;
      max-height: 90vh;
      object-fit: contain;
      border-radius: 8px;
      cursor: default;
    }
    .zoomable { cursor: zoom-in; }

    /* ── 响应式 ── */
    @media (max-width: 768px) {
      nav { padding: 0 20px; }
      .nav-title { display: none; }
      main { padding: 80px 20px 60px; }
      .block-image-text {
        grid-template-columns: 1fr;
      }
      .block-image-text.layout-right .it-img { order: 0; }
      .block-image-text.layout-right .it-text { order: 0; }
      .project-nav { padding: 32px 20px 48px; }
    }
  </style>
</head>
<body>

  <nav id="navbar">
    <a href="index.html" class="nav-back">
      <svg width="16" height="16" viewBox="0 0 16 16" fill="none">
        <path d="M10 12L6 8l4-4" stroke="currentColor" stroke-width="1.8" stroke-linecap="round" stroke-linejoin="round"/>
      </svg>
      返回首页
    </a>
    <span class="nav-title" id="nav-title"></span>
    <div style="width: 80px"></div>
  </nav>

  <main id="content"></main>

  <div class="project-nav" id="project-nav">
    <a id="prev-link" href="#" class="hidden">
      <svg width="14" height="14" viewBox="0 0 14 14" fill="none">
        <path d="M9 11L5 7l4-4" stroke="currentColor" stroke-width="1.8" stroke-linecap="round" stroke-linejoin="round"/>
      </svg>
      上一个项目
    </a>
    <a id="next-link" href="#" class="hidden">
      下一个项目
      <svg width="14" height="14" viewBox="0 0 14 14" fill="none">
        <path d="M5 3l4 4-4 4" stroke="currentColor" stroke-width="1.8" stroke-linecap="round" stroke-linejoin="round"/>
      </svg>
    </a>
  </div>

  <div class="lightbox" id="lightbox">
    <img id="lightbox-img" src="" alt="">
  </div>

  <script>
    // ── 导航滚动边框 ──
    window.addEventListener('scroll', () => {
      document.getElementById('navbar').classList.toggle('scrolled', window.scrollY > 10);
    });

    // ── Lightbox ──
    const lightbox = document.getElementById('lightbox');
    const lightboxImg = document.getElementById('lightbox-img');

    function openLightbox(src) {
      lightboxImg.src = src;
      lightbox.classList.add('active');
    }

    lightbox.addEventListener('click', () => lightbox.classList.remove('active'));
    lightboxImg.addEventListener('click', e => e.stopPropagation());
    document.addEventListener('keydown', e => {
      if (e.key === 'Escape') lightbox.classList.remove('active');
    });

    // ── Block 渲染器 ──
    function renderBlock(block) {
      switch (block.type) {
        case 'heading':      return renderHeading(block);
        case 'body':         return renderBody(block);
        case 'quote':        return renderQuote(block);
        case 'image-single': return renderImageSingle(block);
        case 'image-carousel': return renderImageCarousel(block);
        case 'image-stack':  return renderImageStack(block);
        case 'image-text':   return renderImageText(block);
        case 'video':        return renderVideo(block);
        default:
          const div = document.createElement('div');
          return div;
      }
    }

    function renderHeading(block) {
      const el = document.createElement('h' + block.level);
      el.className = 'block block-h' + block.level;
      el.textContent = block.content;
      return el;
    }

    function renderBody(block) {
      const p = document.createElement('p');
      p.className = 'block block-body';
      p.innerHTML = block.content.replace(/\n/g, '<br>');
      return p;
    }

    function renderQuote(block) {
      const div = document.createElement('div');
      div.className = 'block block-quote';
      div.textContent = block.content;
      return div;
    }

    function renderImageSingle(block) {
      const wrap = document.createElement('div');
      wrap.className = 'block block-image-single';
      const img = document.createElement('img');
      img.src = block.src;
      img.alt = block.caption || '';
      img.className = 'zoomable';
      img.addEventListener('click', () => openLightbox(block.src));
      wrap.appendChild(img);
      if (block.caption) {
        const cap = document.createElement('p');
        cap.className = 'img-caption';
        cap.textContent = block.caption;
        wrap.appendChild(cap);
      }
      return wrap;
    }

    function renderImageCarousel(block) {
      const wrap = document.createElement('div');
      wrap.className = 'block';

      const carousel = document.createElement('div');
      carousel.className = 'carousel';

      const track = document.createElement('div');
      track.className = 'carousel-track';
      block.images.forEach((src, i) => {
        const img = document.createElement('img');
        img.src = src;
        img.alt = (block.captions && block.captions[i]) || '';
        img.className = 'zoomable';
        img.addEventListener('click', () => openLightbox(src));
        track.appendChild(img);
      });
      carousel.appendChild(track);

      const prevBtn = document.createElement('button');
      prevBtn.className = 'carousel-btn carousel-btn-prev';
      prevBtn.innerHTML = '&#8249;';
      const nextBtn = document.createElement('button');
      nextBtn.className = 'carousel-btn carousel-btn-next';
      nextBtn.innerHTML = '&#8250;';
      carousel.appendChild(prevBtn);
      carousel.appendChild(nextBtn);
      wrap.appendChild(carousel);

      const dotsWrap = document.createElement('div');
      dotsWrap.className = 'carousel-dots';
      const dotEls = block.images.map((_, i) => {
        const dot = document.createElement('div');
        dot.className = 'carousel-dot' + (i === 0 ? ' active' : '');
        dot.addEventListener('click', () => goTo(i));
        dotsWrap.appendChild(dot);
        return dot;
      });
      wrap.appendChild(dotsWrap);

      let current = 0;
      function goTo(idx) {
        current = Math.max(0, Math.min(idx, block.images.length - 1));
        track.style.transform = 'translateX(-' + (current * 100) + '%)';
        dotEls.forEach((d, i) => d.classList.toggle('active', i === current));
      }
      prevBtn.addEventListener('click', () => goTo(current - 1));
      nextBtn.addEventListener('click', () => goTo(current + 1));

      return wrap;
    }

    function renderImageStack(block) {
      const wrap = document.createElement('div');
      wrap.className = 'block block-image-stack';
      block.images.forEach(src => {
        const img = document.createElement('img');
        img.src = src;
        img.className = 'zoomable';
        img.addEventListener('click', () => openLightbox(src));
        wrap.appendChild(img);
      });
      return wrap;
    }

    function renderImageText(block) {
      const wrap = document.createElement('div');
      wrap.className = 'block block-image-text' + (block.layout === 'right' ? ' layout-right' : '');

      const imgWrap = document.createElement('div');
      imgWrap.className = 'it-img';
      const img = document.createElement('img');
      img.src = block.image;
      img.alt = block.caption || '';
      img.className = 'zoomable';
      img.addEventListener('click', () => openLightbox(block.image));
      imgWrap.appendChild(img);
      if (block.caption) {
        const cap = document.createElement('p');
        cap.className = 'img-caption';
        cap.textContent = block.caption;
        imgWrap.appendChild(cap);
      }

      const text = document.createElement('div');
      text.className = 'it-text';
      text.innerHTML = block.text.replace(/\n/g, '<br>');

      wrap.appendChild(imgWrap);
      wrap.appendChild(text);
      return wrap;
    }

    function renderVideo(block) {
      const wrap = document.createElement('div');
      wrap.className = 'block block-video';
      if (block.videoType === 'upload') {
        const video = document.createElement('video');
        video.src = block.src;
        video.controls = true;
        video.playsInline = true;
        wrap.appendChild(video);
      } else {
        const embedDiv = document.createElement('div');
        embedDiv.className = 'video-embed';
        const iframe = document.createElement('iframe');
        iframe.allowFullscreen = true;
        iframe.allow = 'accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture';
        if (block.url.includes('bilibili.com') || block.url.includes('b23.tv')) {
          const bvMatch = block.url.match(/BV[\w]+/);
          iframe.src = bvMatch
            ? 'https://player.bilibili.com/player.html?bvid=' + bvMatch[0] + '&page=1&high_quality=1'
            : block.url;
        } else if (block.url.includes('youtube.com') || block.url.includes('youtu.be')) {
          const ytMatch = block.url.match(/(?:v=|youtu\.be\/)([^&?/]+)/);
          iframe.src = ytMatch
            ? 'https://www.youtube.com/embed/' + ytMatch[1]
            : block.url;
        } else {
          iframe.src = block.url;
        }
        embedDiv.appendChild(iframe);
        wrap.appendChild(embedDiv);
      }
      return wrap;
    }

    // ── 加载项目 ──
    const params = new URLSearchParams(location.search);
    const id = parseInt(params.get('id')) || 1;

    Promise.all([
      fetch('data/project-' + id + '.json').then(r => {
        if (!r.ok) throw new Error('项目不存在');
        return r.json();
      }),
      fetch('data/projects.json').then(r => r.json())
    ]).then(([project, projects]) => {
      document.title = project.title + ' · 吴欣';
      document.getElementById('nav-title').textContent = project.title;

      const content = document.getElementById('content');
      project.blocks.forEach(block => {
        content.appendChild(renderBlock(block));
      });

      // 前后翻页
      const idx = projects.findIndex(p => p.id === id);
      const prev = projects[idx - 1];
      const next = projects[idx + 1];

      const prevLink = document.getElementById('prev-link');
      const nextLink = document.getElementById('next-link');

      if (prev) {
        prevLink.href = 'project.html?id=' + prev.id;
        prevLink.querySelector('svg + *') && (prevLink.lastChild.textContent = prev.title);
        prevLink.innerHTML = '<svg width="14" height="14" viewBox="0 0 14 14" fill="none"><path d="M9 11L5 7l4-4" stroke="currentColor" stroke-width="1.8" stroke-linecap="round" stroke-linejoin="round"/></svg>' + prev.title;
        prevLink.classList.remove('hidden');
      }
      if (next) {
        nextLink.href = 'project.html?id=' + next.id;
        nextLink.innerHTML = next.title + '<svg width="14" height="14" viewBox="0 0 14 14" fill="none"><path d="M5 3l4 4-4 4" stroke="currentColor" stroke-width="1.8" stroke-linecap="round" stroke-linejoin="round"/></svg>';
        nextLink.classList.remove('hidden');
      }
    }).catch(err => {
      document.getElementById('content').innerHTML =
        '<p style="color:var(--text-muted);padding:40px 0;">项目内容加载失败：' + err.message + '</p>';
    });
  </script>
</body>
</html>
```

- [ ] **Step 2：提交**

```bash
cd /Users/admin/Desktop/作品集
git add project.html
git commit -m "feat: add project detail page with 8 block renderers"
```

---

## Task 3：更新首页项目卡片链接

**文件：**
- 修改：`/Users/admin/Desktop/作品集/index.html`（项目区块的 4 张卡片）

- [ ] **Step 1：找到 4 张项目卡片的 `href`，从 `javascript:void(0)` 改为对应链接**

将：
```html
<a href="javascript:void(0)" class="project-card">
```

4 张卡片依次改为：
```html
<a href="project.html?id=1" class="project-card">
<a href="project.html?id=2" class="project-card">
<a href="project.html?id=3" class="project-card">
<a href="project.html?id=4" class="project-card">
```

- [ ] **Step 2：同时更新 4 张卡片的标题和描述，与 `projects.json` 保持一致**

卡片 1：
```html
<div class="project-title">AI 课堂 · 奇思妙问</div>
<div class="project-desc">国内首家数字人课堂，服务 2w+ 所学校，2024 年获总理表扬登上新闻联播。</div>
```

卡片 2：
```html
<div class="project-title">必胜客 APP & 小程序</div>
<div class="project-desc">累计会员 1.3 亿的餐饮数字化产品，负责 UX 设计与交互体验。</div>
```

卡片 3：
```html
<div class="project-title">SaaS 教务平台</div>
<div class="project-desc">服务 303 个区域、1w+ 学校的教育 SaaS 产品，负责产品及 UX 工作。</div>
```

卡片 4：
```html
<div class="project-title">智能导师 · openclaw</div>
<div class="project-desc">让教师学会像教学专家一样教学，让学生学会像科学家一样思考。</div>
```

- [ ] **Step 3：更新卡片的 `project-type` 标签**

卡片 1：`<span class="project-type">UX 设计</span> <span class="project-year">2023</span>`
卡片 2：`<span class="project-type">交互设计</span> <span class="project-year">2020</span>`
卡片 3：`<span class="project-type">产品设计</span> <span class="project-year">2022</span>`
卡片 4：`<span class="project-type">UX 设计</span> <span class="project-year">2024</span>`

- [ ] **Step 4：提交**

```bash
cd /Users/admin/Desktop/作品集
git add index.html
git commit -m "feat: link project cards to project detail pages"
```

---

## Task 4：浏览器验证

**不需要修改文件，纯验证步骤。**

- [ ] **Step 1：用本地文件协议打开首页验证链接**

```bash
open /Users/admin/Desktop/作品集/index.html
```

注意：`fetch()` 在 `file://` 协议下会被浏览器安全策略阻止。需要用本地服务器：

```bash
cd /Users/admin/Desktop/作品集
python3 -m http.server 8080
```

然后在浏览器访问 `http://localhost:8080`

- [ ] **Step 2：验证首页**

- [ ] 4 张项目卡片标题已更新为真实项目名
- [ ] 点击卡片能跳转到 `project.html?id=N`

- [ ] **Step 3：验证 project.html 各功能**

访问 `http://localhost:8080/project.html?id=1`，逐项检查：

- [ ] 顶部导航显示项目标题，「返回首页」链接有效
- [ ] 页面渲染出所有内容块（H1 标题、正文、引用块、图片占位、轮播、图文、拼接、视频嵌入）
- [ ] 点击图片打开 Lightbox，点击空白或按 Esc 关闭
- [ ] 轮播左右箭头可以切换图片，底部圆点同步高亮
- [ ] 滚动页面后导航栏出现底部细边框
- [ ] 底部显示「下一个项目 →」链接，点击跳转到 `project.html?id=2`

- [ ] **Step 4：验证边界**

访问 `http://localhost:8080/project.html?id=4`：

- [ ] 底部只显示「← 上一个项目」，没有「下一个项目」
- [ ] 点击「← 上一个项目」跳到 id=3

- [ ] **Step 5：验证移动端**

Chrome 开发者工具 → 手机图标 → 选 iPhone 12 Pro（390px）：

- [ ] 导航标题隐藏，「返回首页」仍显示
- [ ] 图文混排变为单列（图在上，文在下）
- [ ] 内容左右 padding 正常，无溢出

- [ ] **Step 6：停止服务器**

```bash
# 在终端按 Ctrl+C 停止 python server
```
