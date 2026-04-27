# 管理后台（admin.html）Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 创建 `admin.html`——一个单文件管理后台，让作者在浏览器里编辑项目内容并通过 GitHub API 发布到 GitHub Pages。

**Architecture:** 单文件 HTML/CSS/JS，无任何框架依赖。GitHub 配置存 localStorage；图片用 FileReader 转 base64 暂存内存，发布时批量 PUT 到 GitHub；项目数据读写 `data/project-N.json` 和 `data/projects.json`。

**Tech Stack:** 纯 HTML5 / CSS3 / Vanilla JS，GitHub Contents API（REST v3）

---

## 文件结构

```
作品集/
├── admin.html          ← 本次唯一新增文件
├── data/
│   ├── projects.json   ← 发布时同步更新
│   └── project-N.json  ← 发布目标
└── assets/
    └── images/projects/ ← 图片上传目的地
```

---

## Task 1：HTML 骨架 + CSS

**文件：**
- 创建：`/Users/admin/Desktop/作品集/admin.html`

- [ ] **Step 1：创建 admin.html，写入完整 HTML 骨架和 CSS**

创建文件 `/Users/admin/Desktop/作品集/admin.html`，内容如下（完整代码，不省略）：

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>作品集管理后台</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
<style>
*, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

:root {
  --accent: #10b981;
  --accent-light: #d1fae5;
  --bg: #f4f4f5;
  --surface: #ffffff;
  --border: #e4e4e7;
  --text: #09090b;
  --text-2: #52525b;
  --text-3: #a1a1aa;
  --sidebar-w: 220px;
  --topbar-h: 52px;
  --bottombar-h: 56px;
  --radius: 10px;
}

body {
  font-family: 'Inter', sans-serif;
  font-size: 14px;
  color: var(--text);
  background: var(--bg);
  height: 100vh;
  overflow: hidden;
  display: flex;
  flex-direction: column;
}

/* ── Topbar ── */
#topbar {
  height: var(--topbar-h);
  background: var(--surface);
  border-bottom: 1px solid var(--border);
  display: flex;
  align-items: center;
  padding: 0 20px;
  gap: 16px;
  flex-shrink: 0;
  z-index: 10;
}
#topbar .logo {
  font-weight: 700;
  font-size: 15px;
  color: var(--text);
  flex: 1;
}
#btn-gh-settings {
  background: none;
  border: 1px solid var(--border);
  border-radius: 8px;
  padding: 6px 12px;
  font-size: 13px;
  cursor: pointer;
  color: var(--text-2);
  display: flex;
  align-items: center;
  gap: 6px;
  transition: border-color .15s;
}
#btn-gh-settings:hover { border-color: var(--accent); color: var(--accent); }

/* ── Body Layout ── */
#body-wrap {
  flex: 1;
  display: flex;
  overflow: hidden;
}

/* ── Sidebar ── */
#sidebar {
  width: var(--sidebar-w);
  background: var(--surface);
  border-right: 1px solid var(--border);
  display: flex;
  flex-direction: column;
  flex-shrink: 0;
  overflow-y: auto;
}
.sidebar-label {
  font-size: 11px;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: .06em;
  color: var(--text-3);
  padding: 16px 16px 8px;
}
.project-item {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 10px 16px;
  cursor: pointer;
  border-left: 3px solid transparent;
  transition: background .12s;
}
.project-item:hover { background: var(--bg); }
.project-item.active {
  border-left-color: var(--accent);
  background: #f0fdf8;
}
.project-num {
  width: 24px;
  height: 24px;
  border-radius: 6px;
  background: var(--bg);
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 12px;
  font-weight: 600;
  color: var(--text-2);
  flex-shrink: 0;
}
.project-item.active .project-num {
  background: var(--accent-light);
  color: #065f46;
}
.project-name {
  font-size: 13px;
  font-weight: 500;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

/* ── Main Editor ── */
#main {
  flex: 1;
  overflow-y: auto;
  padding: 24px 32px;
  padding-bottom: calc(var(--bottombar-h) + 24px);
}

#project-title-wrap {
  margin-bottom: 24px;
}
#project-title-wrap label {
  display: block;
  font-size: 11px;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: .06em;
  color: var(--text-3);
  margin-bottom: 6px;
}
#project-title-input {
  width: 100%;
  font-size: 20px;
  font-weight: 700;
  border: none;
  background: none;
  outline: none;
  color: var(--text);
  padding: 4px 0;
  border-bottom: 2px solid var(--border);
  transition: border-color .15s;
}
#project-title-input:focus { border-bottom-color: var(--accent); }

/* ── Block List ── */
#block-list { display: flex; flex-direction: column; gap: 12px; }

.block-item {
  background: var(--surface);
  border: 1px solid var(--border);
  border-radius: var(--radius);
  overflow: hidden;
}
.block-header {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 10px 14px;
  background: #fafafa;
  border-bottom: 1px solid var(--border);
}
.block-type-badge {
  font-size: 11px;
  font-weight: 600;
  background: var(--accent-light);
  color: #065f46;
  border-radius: 4px;
  padding: 2px 7px;
}
.block-title {
  flex: 1;
  font-size: 13px;
  font-weight: 500;
  color: var(--text-2);
}
.block-controls {
  display: flex;
  gap: 4px;
}
.block-controls button {
  width: 28px;
  height: 28px;
  border: 1px solid var(--border);
  border-radius: 6px;
  background: var(--surface);
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  color: var(--text-3);
  font-size: 14px;
  transition: all .12s;
}
.block-controls button:hover { border-color: var(--accent); color: var(--accent); background: #f0fdf8; }
.block-controls button.del:hover { border-color: #ef4444; color: #ef4444; background: #fef2f2; }
.block-body { padding: 14px; }

/* ── Form Fields ── */
.field-group { margin-bottom: 12px; }
.field-group:last-child { margin-bottom: 0; }
.field-label {
  display: block;
  font-size: 12px;
  font-weight: 500;
  color: var(--text-2);
  margin-bottom: 5px;
}
input[type="text"], textarea, select {
  width: 100%;
  border: 1px solid var(--border);
  border-radius: 7px;
  padding: 7px 10px;
  font-size: 13px;
  font-family: 'Inter', sans-serif;
  color: var(--text);
  background: var(--bg);
  outline: none;
  transition: border-color .15s;
  resize: vertical;
}
input[type="text"]:focus, textarea:focus, select:focus { border-color: var(--accent); background: #fff; }
textarea { min-height: 80px; }
select { cursor: pointer; }

/* ── Image Upload Areas ── */
.upload-zone {
  border: 2px dashed var(--border);
  border-radius: 8px;
  padding: 16px;
  text-align: center;
  cursor: pointer;
  transition: all .15s;
  background: var(--bg);
  position: relative;
}
.upload-zone:hover { border-color: var(--accent); background: #f0fdf8; }
.upload-zone input[type="file"] {
  position: absolute;
  inset: 0;
  opacity: 0;
  cursor: pointer;
  width: 100%;
  height: 100%;
}
.upload-zone-text { font-size: 12px; color: var(--text-3); pointer-events: none; }
.upload-zone-text span { color: var(--accent); font-weight: 500; }
.upload-preview {
  display: flex;
  flex-direction: column;
  gap: 8px;
  margin-top: 8px;
}
.upload-preview-item {
  display: flex;
  align-items: center;
  gap: 10px;
  background: var(--surface);
  border: 1px solid var(--border);
  border-radius: 7px;
  padding: 8px 10px;
}
.upload-preview-thumb {
  width: 48px;
  height: 36px;
  object-fit: cover;
  border-radius: 4px;
  flex-shrink: 0;
}
.upload-preview-name {
  flex: 1;
  font-size: 12px;
  color: var(--text-2);
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}
.upload-preview-remove {
  width: 22px;
  height: 22px;
  border: none;
  background: none;
  cursor: pointer;
  color: var(--text-3);
  font-size: 16px;
  line-height: 1;
  border-radius: 4px;
  display: flex;
  align-items: center;
  justify-content: center;
}
.upload-preview-remove:hover { color: #ef4444; background: #fef2f2; }
.caption-row { margin-top: 4px; }

/* ── Image-Text Layout Toggle ── */
.layout-toggle { display: flex; gap: 8px; }
.layout-btn {
  flex: 1;
  padding: 7px;
  border: 1px solid var(--border);
  border-radius: 7px;
  background: var(--bg);
  cursor: pointer;
  font-size: 12px;
  font-weight: 500;
  color: var(--text-2);
  transition: all .12s;
  text-align: center;
}
.layout-btn.active, .layout-btn:hover {
  border-color: var(--accent);
  background: var(--accent-light);
  color: #065f46;
}

/* ── Video Toggle ── */
.video-type-toggle { display: flex; gap: 8px; margin-bottom: 12px; }
.video-type-btn {
  flex: 1;
  padding: 7px;
  border: 1px solid var(--border);
  border-radius: 7px;
  background: var(--bg);
  cursor: pointer;
  font-size: 12px;
  font-weight: 500;
  color: var(--text-2);
  transition: all .12s;
  text-align: center;
}
.video-type-btn.active {
  border-color: var(--accent);
  background: var(--accent-light);
  color: #065f46;
}

/* ── Add Block Area ── */
#add-block-area { margin-top: 16px; }
#btn-add-block-toggle {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 10px 16px;
  border: 2px dashed var(--border);
  border-radius: var(--radius);
  background: none;
  cursor: pointer;
  color: var(--text-3);
  font-size: 13px;
  font-weight: 500;
  width: 100%;
  transition: all .15s;
}
#btn-add-block-toggle:hover { border-color: var(--accent); color: var(--accent); background: #f0fdf8; }
#add-block-menu {
  display: none;
  margin-top: 10px;
  background: var(--surface);
  border: 1px solid var(--border);
  border-radius: var(--radius);
  padding: 10px;
  display: none;
  grid-template-columns: repeat(4, 1fr);
  gap: 8px;
}
#add-block-menu.open { display: grid; }
.add-type-btn {
  padding: 10px 6px;
  border: 1px solid var(--border);
  border-radius: 8px;
  background: var(--bg);
  cursor: pointer;
  font-size: 12px;
  font-weight: 500;
  color: var(--text-2);
  text-align: center;
  transition: all .12s;
}
.add-type-btn:hover { border-color: var(--accent); background: var(--accent-light); color: #065f46; }

/* ── Bottombar ── */
#bottombar {
  position: fixed;
  bottom: 0;
  left: var(--sidebar-w);
  right: 0;
  height: var(--bottombar-h);
  background: var(--surface);
  border-top: 1px solid var(--border);
  display: flex;
  align-items: center;
  justify-content: flex-end;
  padding: 0 24px;
  gap: 10px;
  z-index: 10;
}
#status-msg {
  flex: 1;
  font-size: 13px;
  color: var(--text-3);
}
#status-msg.ok { color: var(--accent); }
#status-msg.err { color: #ef4444; }
.btn-secondary {
  padding: 8px 18px;
  border: 1px solid var(--border);
  border-radius: 8px;
  background: var(--surface);
  font-size: 13px;
  font-weight: 500;
  cursor: pointer;
  color: var(--text-2);
  transition: all .15s;
}
.btn-secondary:hover { border-color: var(--accent); color: var(--accent); }
.btn-primary {
  padding: 8px 20px;
  border: none;
  border-radius: 8px;
  background: var(--text);
  color: #fff;
  font-size: 13px;
  font-weight: 600;
  cursor: pointer;
  transition: background .15s;
}
.btn-primary:hover { background: #27272a; }
.btn-primary:disabled { background: #a1a1aa; cursor: not-allowed; }

/* ── GitHub Config Modal ── */
#modal-overlay {
  display: none;
  position: fixed;
  inset: 0;
  background: rgba(0,0,0,.4);
  z-index: 100;
  align-items: center;
  justify-content: center;
}
#modal-overlay.open { display: flex; }
#modal {
  background: var(--surface);
  border-radius: 16px;
  padding: 28px;
  width: 440px;
  box-shadow: 0 20px 60px rgba(0,0,0,.15);
}
#modal h2 { font-size: 16px; font-weight: 700; margin-bottom: 6px; }
#modal .modal-sub { font-size: 13px; color: var(--text-2); margin-bottom: 20px; }
.modal-field { margin-bottom: 14px; }
.modal-field label { display: block; font-size: 12px; font-weight: 500; color: var(--text-2); margin-bottom: 5px; }
.modal-actions { display: flex; gap: 10px; justify-content: flex-end; margin-top: 20px; }

/* ── Empty State ── */
#empty-state {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 60vh;
  color: var(--text-3);
  font-size: 14px;
  gap: 12px;
}
#empty-state svg { opacity: .3; }
</style>
</head>
<body>

<!-- Topbar -->
<div id="topbar">
  <div class="logo">作品集管理</div>
  <button id="btn-gh-settings">
    <svg width="14" height="14" viewBox="0 0 16 16" fill="currentColor"><path d="M8 0C3.58 0 0 3.58 0 8c0 3.54 2.29 6.53 5.47 7.59.4.07.55-.17.55-.38 0-.19-.01-.82-.01-1.49-2.01.37-2.53-.49-2.69-.94-.09-.23-.48-.94-.82-1.13-.28-.15-.68-.52-.01-.53.63-.01 1.08.58 1.23.82.72 1.21 1.87.87 2.33.66.07-.52.28-.87.51-1.07-1.78-.2-3.64-.89-3.64-3.95 0-.87.31-1.59.82-2.15-.08-.2-.36-1.02.08-2.12 0 0 .67-.21 2.2.82.64-.18 1.32-.27 2-.27.68 0 1.36.09 2 .27 1.53-1.04 2.2-.82 2.2-.82.44 1.1.16 1.92.08 2.12.51.56.82 1.27.82 2.15 0 3.07-1.87 3.75-3.65 3.95.29.25.54.73.54 1.48 0 1.07-.01 1.93-.01 2.2 0 .21.15.46.55.38A8.013 8.013 0 0016 8c0-4.42-3.58-8-8-8z"/></svg>
    GitHub 设置
  </button>
</div>

<!-- Body -->
<div id="body-wrap">

  <!-- Sidebar -->
  <div id="sidebar">
    <div class="sidebar-label">项目列表</div>
    <div id="project-list"></div>
  </div>

  <!-- Main Editor -->
  <div id="main">
    <div id="empty-state">
      <svg width="48" height="48" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5"><rect x="3" y="3" width="18" height="18" rx="3"/><path d="M3 9h18M9 21V9"/></svg>
      <span>从左侧选择一个项目开始编辑</span>
    </div>
    <div id="editor-area" style="display:none">
      <div id="project-title-wrap">
        <label>项目名称</label>
        <input type="text" id="project-title-input" placeholder="输入项目标题">
      </div>
      <div id="block-list"></div>
      <div id="add-block-area">
        <button id="btn-add-block-toggle">
          <svg width="14" height="14" viewBox="0 0 14 14" fill="none" stroke="currentColor" stroke-width="2"><path d="M7 1v12M1 7h12"/></svg>
          添加内容块
        </button>
        <div id="add-block-menu">
          <button class="add-type-btn" data-type="heading">标题</button>
          <button class="add-type-btn" data-type="body">正文</button>
          <button class="add-type-btn" data-type="quote">引言</button>
          <button class="add-type-btn" data-type="image-single">单图</button>
          <button class="add-type-btn" data-type="image-carousel">轮播图</button>
          <button class="add-type-btn" data-type="image-stack">堆叠图</button>
          <button class="add-type-btn" data-type="image-text">图文</button>
          <button class="add-type-btn" data-type="video">视频</button>
        </div>
      </div>
    </div>
  </div>

</div>

<!-- Bottombar -->
<div id="bottombar">
  <span id="status-msg"></span>
  <button class="btn-secondary" id="btn-preview">预览</button>
  <button class="btn-primary" id="btn-publish">发布</button>
</div>

<!-- GitHub Config Modal -->
<div id="modal-overlay">
  <div id="modal">
    <h2>GitHub 配置</h2>
    <p class="modal-sub">填写后可将项目内容发布到 GitHub Pages。Token 仅存储在本地浏览器。</p>
    <div class="modal-field">
      <label>GitHub 用户名</label>
      <input type="text" id="gh-owner" placeholder="例：octocat">
    </div>
    <div class="modal-field">
      <label>仓库名</label>
      <input type="text" id="gh-repo" placeholder="例：portfolio">
    </div>
    <div class="modal-field">
      <label>Personal Access Token（需要 contents:write 权限）</label>
      <input type="text" id="gh-token" placeholder="ghp_xxxxxxxxxxxx">
    </div>
    <div class="modal-actions">
      <button class="btn-secondary" id="btn-modal-cancel">取消</button>
      <button class="btn-primary" id="btn-modal-save">保存</button>
    </div>
  </div>
</div>

<script>
// ─────────────────────────────────────────
// State
// ─────────────────────────────────────────
const CONFIG_KEY = 'portfolio_gh_config';
let config = { owner: '', repo: '', token: '' };
let projects = [];       // from data/projects.json
let currentId = null;    // currently selected project id
let currentData = null;  // { id, title, blocks }

// pendingUploads: key = "block_<blockIdx>_<imgIdx>" | "block_<blockIdx>_video"
// value: { base64, filename, mimeType, targetPath }
let pendingUploads = {};

// ─────────────────────────────────────────
// Config (localStorage)
// ─────────────────────────────────────────
function loadConfig() {
  const raw = localStorage.getItem(CONFIG_KEY);
  if (raw) {
    try { config = JSON.parse(raw); } catch(e) {}
  }
}

function saveConfig() {
  config.owner = document.getElementById('gh-owner').value.trim();
  config.repo  = document.getElementById('gh-repo').value.trim();
  config.token = document.getElementById('gh-token').value.trim();
  localStorage.setItem(CONFIG_KEY, JSON.stringify(config));
  closeModal();
}

// ─────────────────────────────────────────
// Modal
// ─────────────────────────────────────────
function openModal() {
  document.getElementById('gh-owner').value = config.owner || '';
  document.getElementById('gh-repo').value  = config.repo  || '';
  document.getElementById('gh-token').value = config.token || '';
  document.getElementById('modal-overlay').classList.add('open');
}
function closeModal() {
  document.getElementById('modal-overlay').classList.remove('open');
}

// ─────────────────────────────────────────
// GitHub API helpers
// ─────────────────────────────────────────
async function ghGet(path) {
  const url = `https://api.github.com/repos/${config.owner}/${config.repo}/contents/${path}`;
  const res = await fetch(url, { headers: { Authorization: `token ${config.token}` } });
  if (!res.ok) throw new Error(`GET ${path} failed: ${res.status}`);
  return res.json();
}

async function ghPut(path, base64Content, message) {
  const url = `https://api.github.com/repos/${config.owner}/${config.repo}/contents/${path}`;
  // Get current SHA if file exists (needed for update)
  let sha;
  try {
    const existing = await ghGet(path);
    sha = existing.sha;
  } catch(e) { /* new file, no sha needed */ }

  const body = { message, content: base64Content };
  if (sha) body.sha = sha;

  const res = await fetch(url, {
    method: 'PUT',
    headers: {
      Authorization: `token ${config.token}`,
      'Content-Type': 'application/json'
    },
    body: JSON.stringify(body)
  });
  if (!res.ok) {
    const err = await res.json();
    throw new Error(`PUT ${path} failed: ${err.message || res.status}`);
  }
  return res.json();
}

// Encode UTF-8 text to base64 (safe for Chinese characters)
function textToBase64(str) {
  const bytes = new TextEncoder().encode(str);
  let binary = '';
  bytes.forEach(b => binary += String.fromCharCode(b));
  return btoa(binary);
}

// ─────────────────────────────────────────
// Load project list into sidebar
// ─────────────────────────────────────────
async function loadProjects() {
  try {
    const res = await fetch('data/projects.json?_=' + Date.now());
    projects = await res.json();
  } catch(e) {
    // Fallback: static list if fetch fails (e.g. file:// without server)
    projects = [
      { id:1, title:'项目 1', type:'', year:'', desc:'', thumb:'' },
      { id:2, title:'项目 2', type:'', year:'', desc:'', thumb:'' },
      { id:3, title:'项目 3', type:'', year:'', desc:'', thumb:'' },
      { id:4, title:'项目 4', type:'', year:'', desc:'', thumb:'' }
    ];
  }
  renderSidebar();
}

function renderSidebar() {
  const list = document.getElementById('project-list');
  list.innerHTML = '';
  projects.forEach(p => {
    const item = document.createElement('div');
    item.className = 'project-item' + (p.id === currentId ? ' active' : '');
    item.dataset.id = p.id;
    item.innerHTML = `<div class="project-num">${p.id}</div><div class="project-name">${p.title}</div>`;
    item.addEventListener('click', () => selectProject(p.id));
    list.appendChild(item);
  });
}

// ─────────────────────────────────────────
// Select and load a project
// ─────────────────────────────────────────
async function selectProject(id) {
  currentId = id;
  pendingUploads = {};
  renderSidebar();
  setStatus('加载中…', '');

  try {
    const res = await fetch(`data/project-${id}.json?_=` + Date.now());
    if (res.ok) {
      currentData = await res.json();
    } else {
      // New/empty project
      currentData = { id, title: projects.find(p=>p.id===id)?.title || `项目 ${id}`, blocks: [] };
    }
  } catch(e) {
    currentData = { id, title: `项目 ${id}`, blocks: [] };
  }

  document.getElementById('empty-state').style.display = 'none';
  document.getElementById('editor-area').style.display = 'block';
  document.getElementById('project-title-input').value = currentData.title || '';
  renderBlockList();
  setStatus('', '');
}

// ─────────────────────────────────────────
// Block List Renderer
// ─────────────────────────────────────────
function renderBlockList() {
  const list = document.getElementById('block-list');
  list.innerHTML = '';
  (currentData.blocks || []).forEach((block, idx) => {
    list.appendChild(buildBlockItem(block, idx));
  });
}

const TYPE_LABELS = {
  'heading':       '标题',
  'body':          '正文',
  'quote':         '引言',
  'image-single':  '单图',
  'image-carousel':'轮播图',
  'image-stack':   '堆叠图',
  'image-text':    '图文',
  'video':         '视频'
};

function buildBlockItem(block, idx) {
  const item = document.createElement('div');
  item.className = 'block-item';
  item.dataset.idx = idx;

  const total = currentData.blocks.length;
  const header = document.createElement('div');
  header.className = 'block-header';
  header.innerHTML = `
    <span class="block-type-badge">${TYPE_LABELS[block.type] || block.type}</span>
    <span class="block-title">${getBlockPreview(block)}</span>
    <div class="block-controls">
      <button title="上移" ${idx===0?'disabled':''} data-action="up" data-idx="${idx}">↑</button>
      <button title="下移" ${idx===total-1?'disabled':''} data-action="down" data-idx="${idx}">↓</button>
      <button title="删除" class="del" data-action="del" data-idx="${idx}">×</button>
    </div>`;

  const body = document.createElement('div');
  body.className = 'block-body';
  body.appendChild(buildBlockFields(block, idx));

  item.appendChild(header);
  item.appendChild(body);

  // Control button events
  header.querySelectorAll('[data-action]').forEach(btn => {
    btn.addEventListener('click', e => {
      e.stopPropagation();
      const action = btn.dataset.action;
      const i = parseInt(btn.dataset.idx);
      if (action === 'up' && i > 0) {
        [currentData.blocks[i-1], currentData.blocks[i]] = [currentData.blocks[i], currentData.blocks[i-1]];
        renderBlockList();
      } else if (action === 'down' && i < currentData.blocks.length-1) {
        [currentData.blocks[i], currentData.blocks[i+1]] = [currentData.blocks[i+1], currentData.blocks[i]];
        renderBlockList();
      } else if (action === 'del') {
        currentData.blocks.splice(i, 1);
        renderBlockList();
      }
    });
  });

  return item;
}

function getBlockPreview(block) {
  switch(block.type) {
    case 'heading': return (block.content || '').slice(0, 30) || '（空标题）';
    case 'body':    return (block.content || '').slice(0, 40) || '（空正文）';
    case 'quote':   return (block.content || '').slice(0, 40) || '（空引言）';
    case 'image-single': return block.src ? '已选择图片' : '未选择图片';
    case 'image-carousel': return `${(block.images||[]).length} 张图片`;
    case 'image-stack':    return `${(block.images||[]).length} 张图片`;
    case 'image-text': return block.image ? '已选择图片' : '未选择图片';
    case 'video': return block.videoType === 'embed' ? (block.url||'').slice(0,40)||'未填链接' : (block.src ? '已选择视频' : '未选择视频');
    default: return '';
  }
}

// ─────────────────────────────────────────
// Block Fields Builder
// ─────────────────────────────────────────
function buildBlockFields(block, idx) {
  const wrap = document.createElement('div');

  switch(block.type) {
    case 'heading': wrap.appendChild(buildHeadingFields(block, idx)); break;
    case 'body':    wrap.appendChild(buildBodyFields(block, idx)); break;
    case 'quote':   wrap.appendChild(buildQuoteFields(block, idx)); break;
    case 'image-single':   wrap.appendChild(buildImageSingleFields(block, idx)); break;
    case 'image-carousel': wrap.appendChild(buildImageCarouselFields(block, idx)); break;
    case 'image-stack':    wrap.appendChild(buildImageStackFields(block, idx)); break;
    case 'image-text':     wrap.appendChild(buildImageTextField(block, idx)); break;
    case 'video':          wrap.appendChild(buildVideoFields(block, idx)); break;
  }

  return wrap;
}

// heading
function buildHeadingFields(block, idx) {
  const frag = document.createDocumentFragment();

  const levelGroup = document.createElement('div');
  levelGroup.className = 'field-group';
  const levelLabel = document.createElement('label');
  levelLabel.className = 'field-label';
  levelLabel.textContent = '级别';
  const levelSel = document.createElement('select');
  [['1','H1 一级标题'],['2','H2 二级标题'],['3','H3 三级标题']].forEach(([v,t]) => {
    const opt = document.createElement('option');
    opt.value = v; opt.textContent = t;
    if (String(block.level) === v) opt.selected = true;
    levelSel.appendChild(opt);
  });
  levelSel.addEventListener('change', () => { block.level = parseInt(levelSel.value); });
  levelGroup.appendChild(levelLabel);
  levelGroup.appendChild(levelSel);

  const contentGroup = document.createElement('div');
  contentGroup.className = 'field-group';
  const contentLabel = document.createElement('label');
  contentLabel.className = 'field-label';
  contentLabel.textContent = '标题文字';
  const contentInput = document.createElement('input');
  contentInput.type = 'text';
  contentInput.value = block.content || '';
  contentInput.placeholder = '输入标题';
  contentInput.addEventListener('input', () => { block.content = contentInput.value; });
  contentGroup.appendChild(contentLabel);
  contentGroup.appendChild(contentInput);

  frag.appendChild(levelGroup);
  frag.appendChild(contentGroup);
  return frag;
}

// body
function buildBodyFields(block, idx) {
  const group = document.createElement('div');
  group.className = 'field-group';
  const label = document.createElement('label');
  label.className = 'field-label';
  label.textContent = '正文内容';
  const ta = document.createElement('textarea');
  ta.value = block.content || '';
  ta.placeholder = '输入正文，支持换行';
  ta.rows = 5;
  ta.addEventListener('input', () => { block.content = ta.value; });
  group.appendChild(label);
  group.appendChild(ta);
  return group;
}

// quote
function buildQuoteFields(block, idx) {
  const group = document.createElement('div');
  group.className = 'field-group';
  const label = document.createElement('label');
  label.className = 'field-label';
  label.textContent = '引言内容';
  const input = document.createElement('input');
  input.type = 'text';
  input.value = block.content || '';
  input.placeholder = '关键洞察或数据…';
  input.addEventListener('input', () => { block.content = input.value; });
  group.appendChild(label);
  group.appendChild(input);
  return group;
}

// ── image-single ──
function buildImageSingleFields(block, idx) {
  const frag = document.createDocumentFragment();

  const imgGroup = document.createElement('div');
  imgGroup.className = 'field-group';
  const imgLabel = document.createElement('label');
  imgLabel.className = 'field-label';
  imgLabel.textContent = '图片';
  imgGroup.appendChild(imgLabel);
  imgGroup.appendChild(buildSingleImageUpload(block, idx, 'img', 0));

  const capGroup = document.createElement('div');
  capGroup.className = 'field-group';
  const capLabel = document.createElement('label');
  capLabel.className = 'field-label';
  capLabel.textContent = '图片说明（可选）';
  const capInput = document.createElement('input');
  capInput.type = 'text';
  capInput.value = block.caption || '';
  capInput.placeholder = '图片说明文字';
  capInput.addEventListener('input', () => { block.caption = capInput.value; });
  capGroup.appendChild(capLabel);
  capGroup.appendChild(capInput);

  frag.appendChild(imgGroup);
  frag.appendChild(capGroup);
  return frag;
}

// ── image-carousel ──
function buildImageCarouselFields(block, idx) {
  if (!block.images) block.images = [];
  if (!block.captions) block.captions = [];

  const frag = document.createDocumentFragment();
  const label = document.createElement('label');
  label.className = 'field-label';
  label.textContent = '轮播图片（可添加多张）';
  frag.appendChild(label);

  const listWrap = document.createElement('div');
  listWrap.style.display = 'flex';
  listWrap.style.flexDirection = 'column';
  listWrap.style.gap = '10px';
  frag.appendChild(listWrap);

  function renderCarouselItems() {
    listWrap.innerHTML = '';
    block.images.forEach((src, imgIdx) => {
      const itemWrap = document.createElement('div');
      itemWrap.style.cssText = 'background:#fafafa;border:1px solid var(--border);border-radius:8px;padding:10px;';

      const row = document.createElement('div');
      row.style.cssText = 'display:flex;align-items:center;gap:10px;margin-bottom:8px;';

      // Thumbnail or zone
      const thumb = document.createElement('div');
      thumb.style.cssText = 'width:64px;height:48px;border-radius:6px;overflow:hidden;flex-shrink:0;background:#eee;display:flex;align-items:center;justify-content:center;';
      if (src && !src.startsWith('assets/')) {
        // data URL (pending)
        const img = document.createElement('img');
        img.src = src; img.style.cssText = 'width:100%;height:100%;object-fit:cover;';
        thumb.appendChild(img);
      } else if (src) {
        const img = document.createElement('img');
        img.src = src; img.style.cssText = 'width:100%;height:100%;object-fit:cover;';
        thumb.appendChild(img);
      } else {
        thumb.style.fontSize = '10px'; thumb.style.color = 'var(--text-3)';
        thumb.textContent = '未选择';
      }

      const uploadLabel = document.createElement('label');
      uploadLabel.style.cssText = 'font-size:12px;color:var(--accent);cursor:pointer;font-weight:500;';
      uploadLabel.textContent = src ? '重新上传' : '选择图片';
      const fileInput = document.createElement('input');
      fileInput.type = 'file'; fileInput.accept = 'image/*';
      fileInput.style.display = 'none';
      fileInput.addEventListener('change', () => {
        const file = fileInput.files[0];
        if (!file) return;
        const reader = new FileReader();
        reader.onload = e => {
          const base64Data = e.target.result.split(',')[1];
          const ext = file.name.split('.').pop();
          const fname = `p${currentId}-carousel-${idx}-${imgIdx}-${Date.now()}.${ext}`;
          const targetPath = `assets/images/projects/${fname}`;
          pendingUploads[`block_${idx}_carousel_${imgIdx}`] = { base64: base64Data, filename: fname, targetPath };
          block.images[imgIdx] = e.target.result; // preview with data URL
          block.captions[imgIdx] = block.captions[imgIdx] || '';
          renderCarouselItems();
        };
        reader.readAsDataURL(file);
      });
      uploadLabel.appendChild(fileInput);

      const removeBtn = document.createElement('button');
      removeBtn.className = 'upload-preview-remove';
      removeBtn.textContent = '×';
      removeBtn.title = '删除此图';
      removeBtn.style.marginLeft = 'auto';
      removeBtn.addEventListener('click', () => {
        block.images.splice(imgIdx, 1);
        block.captions.splice(imgIdx, 1);
        delete pendingUploads[`block_${idx}_carousel_${imgIdx}`];
        renderCarouselItems();
      });

      row.appendChild(thumb);
      row.appendChild(uploadLabel);
      row.appendChild(removeBtn);

      const capInput = document.createElement('input');
      capInput.type = 'text';
      capInput.value = block.captions[imgIdx] || '';
      capInput.placeholder = '图片说明（可选）';
      capInput.style.marginTop = '0';
      capInput.addEventListener('input', () => { block.captions[imgIdx] = capInput.value; });

      itemWrap.appendChild(row);
      itemWrap.appendChild(capInput);
      listWrap.appendChild(itemWrap);
    });

    const addBtn = document.createElement('button');
    addBtn.className = 'btn-secondary';
    addBtn.textContent = '+ 添加图片';
    addBtn.style.cssText = 'font-size:12px;padding:6px 12px;width:auto;';
    addBtn.addEventListener('click', () => {
      block.images.push('');
      block.captions.push('');
      renderCarouselItems();
    });
    listWrap.appendChild(addBtn);
  }

  renderCarouselItems();
  return frag;
}

// ── image-stack ──
function buildImageStackFields(block, idx) {
  if (!block.images) block.images = [];

  const frag = document.createDocumentFragment();
  const label = document.createElement('label');
  label.className = 'field-label';
  label.textContent = '堆叠图片（垂直排列）';
  frag.appendChild(label);

  const listWrap = document.createElement('div');
  listWrap.style.display = 'flex';
  listWrap.style.flexDirection = 'column';
  listWrap.style.gap = '8px';
  frag.appendChild(listWrap);

  function renderStackItems() {
    listWrap.innerHTML = '';
    block.images.forEach((src, imgIdx) => {
      const row = document.createElement('div');
      row.style.cssText = 'display:flex;align-items:center;gap:8px;background:#fafafa;border:1px solid var(--border);border-radius:8px;padding:8px 10px;';

      const thumb = document.createElement('div');
      thumb.style.cssText = 'width:56px;height:42px;border-radius:5px;overflow:hidden;flex-shrink:0;background:#eee;display:flex;align-items:center;justify-content:center;';
      if (src) {
        const img = document.createElement('img');
        img.src = src; img.style.cssText = 'width:100%;height:100%;object-fit:cover;';
        thumb.appendChild(img);
      } else {
        thumb.style.fontSize = '10px'; thumb.style.color = 'var(--text-3)';
        thumb.textContent = '未选择';
      }

      const uploadLabel = document.createElement('label');
      uploadLabel.style.cssText = 'font-size:12px;color:var(--accent);cursor:pointer;font-weight:500;flex:1;';
      uploadLabel.textContent = src ? '重新上传' : '选择图片';
      const fileInput = document.createElement('input');
      fileInput.type = 'file'; fileInput.accept = 'image/*';
      fileInput.style.display = 'none';
      fileInput.addEventListener('change', () => {
        const file = fileInput.files[0];
        if (!file) return;
        const reader = new FileReader();
        reader.onload = e => {
          const base64Data = e.target.result.split(',')[1];
          const ext = file.name.split('.').pop();
          const fname = `p${currentId}-stack-${idx}-${imgIdx}-${Date.now()}.${ext}`;
          const targetPath = `assets/images/projects/${fname}`;
          pendingUploads[`block_${idx}_stack_${imgIdx}`] = { base64: base64Data, filename: fname, targetPath };
          block.images[imgIdx] = e.target.result;
          renderStackItems();
        };
        reader.readAsDataURL(file);
      });
      uploadLabel.appendChild(fileInput);

      const removeBtn = document.createElement('button');
      removeBtn.className = 'upload-preview-remove';
      removeBtn.textContent = '×';
      removeBtn.title = '删除';
      removeBtn.addEventListener('click', () => {
        block.images.splice(imgIdx, 1);
        delete pendingUploads[`block_${idx}_stack_${imgIdx}`];
        renderStackItems();
      });

      row.appendChild(thumb);
      row.appendChild(uploadLabel);
      row.appendChild(removeBtn);
      listWrap.appendChild(row);
    });

    const addBtn = document.createElement('button');
    addBtn.className = 'btn-secondary';
    addBtn.textContent = '+ 添加图片';
    addBtn.style.cssText = 'font-size:12px;padding:6px 12px;width:auto;';
    addBtn.addEventListener('click', () => { block.images.push(''); renderStackItems(); });
    listWrap.appendChild(addBtn);
  }

  renderStackItems();
  return frag;
}

// ── image-text ──
function buildImageTextField(block, idx) {
  const frag = document.createDocumentFragment();

  // Layout toggle
  const layoutGroup = document.createElement('div');
  layoutGroup.className = 'field-group';
  const layoutLabel = document.createElement('label');
  layoutLabel.className = 'field-label';
  layoutLabel.textContent = '图片位置';
  const toggleWrap = document.createElement('div');
  toggleWrap.className = 'layout-toggle';
  ['left','right'].forEach(v => {
    const btn = document.createElement('button');
    btn.className = 'layout-btn' + (block.layout === v ? ' active' : '');
    btn.textContent = v === 'left' ? '图片在左' : '图片在右';
    btn.addEventListener('click', () => {
      block.layout = v;
      toggleWrap.querySelectorAll('.layout-btn').forEach(b => b.classList.remove('active'));
      btn.classList.add('active');
    });
    toggleWrap.appendChild(btn);
  });
  layoutGroup.appendChild(layoutLabel);
  layoutGroup.appendChild(toggleWrap);
  frag.appendChild(layoutGroup);

  // Image upload
  const imgGroup = document.createElement('div');
  imgGroup.className = 'field-group';
  const imgLabel = document.createElement('label');
  imgLabel.className = 'field-label';
  imgLabel.textContent = '图片';
  imgGroup.appendChild(imgLabel);
  imgGroup.appendChild(buildSingleImageUpload(block, idx, 'image', 0));
  frag.appendChild(imgGroup);

  // Caption
  const capGroup = document.createElement('div');
  capGroup.className = 'field-group';
  const capLabel = document.createElement('label');
  capLabel.className = 'field-label';
  capLabel.textContent = '图片说明（可选）';
  const capInput = document.createElement('input');
  capInput.type = 'text';
  capInput.value = block.caption || '';
  capInput.placeholder = '图片说明';
  capInput.addEventListener('input', () => { block.caption = capInput.value; });
  capGroup.appendChild(capLabel);
  capGroup.appendChild(capInput);
  frag.appendChild(capGroup);

  // Text
  const textGroup = document.createElement('div');
  textGroup.className = 'field-group';
  const textLabel = document.createElement('label');
  textLabel.className = 'field-label';
  textLabel.textContent = '配文';
  const ta = document.createElement('textarea');
  ta.value = block.text || '';
  ta.placeholder = '配图说明文字，支持换行';
  ta.rows = 4;
  ta.addEventListener('input', () => { block.text = ta.value; });
  textGroup.appendChild(textLabel);
  textGroup.appendChild(ta);
  frag.appendChild(textGroup);

  return frag;
}

// ── video ──
function buildVideoFields(block, idx) {
  if (!block.videoType) block.videoType = 'embed';
  const frag = document.createDocumentFragment();

  // Type toggle
  const toggle = document.createElement('div');
  toggle.className = 'video-type-toggle';
  ['embed','upload'].forEach(v => {
    const btn = document.createElement('button');
    btn.className = 'video-type-btn' + (block.videoType === v ? ' active' : '');
    btn.textContent = v === 'embed' ? '粘贴链接（B站/YouTube）' : '上传视频文件';
    btn.addEventListener('click', () => {
      block.videoType = v;
      toggle.querySelectorAll('.video-type-btn').forEach(b => b.classList.remove('active'));
      btn.classList.add('active');
      fieldsWrap.innerHTML = '';
      fieldsWrap.appendChild(buildVideoTypeFields(block, idx));
    });
    toggle.appendChild(btn);
  });
  frag.appendChild(toggle);

  const fieldsWrap = document.createElement('div');
  fieldsWrap.appendChild(buildVideoTypeFields(block, idx));
  frag.appendChild(fieldsWrap);

  return frag;
}

function buildVideoTypeFields(block, idx) {
  const frag = document.createDocumentFragment();
  if (block.videoType === 'embed') {
    const group = document.createElement('div');
    group.className = 'field-group';
    const label = document.createElement('label');
    label.className = 'field-label';
    label.textContent = '视频链接（B站/YouTube）';
    const input = document.createElement('input');
    input.type = 'text';
    input.value = block.url || '';
    input.placeholder = 'https://www.bilibili.com/video/...';
    input.addEventListener('input', () => { block.url = input.value.trim(); delete block.src; });
    group.appendChild(label);
    group.appendChild(input);
    frag.appendChild(group);
  } else {
    const group = document.createElement('div');
    group.className = 'field-group';
    const label = document.createElement('label');
    label.className = 'field-label';
    label.textContent = '视频文件';
    const zone = document.createElement('div');
    zone.className = 'upload-zone';
    const fileInput = document.createElement('input');
    fileInput.type = 'file'; fileInput.accept = 'video/*';
    const zoneText = document.createElement('div');
    zoneText.className = 'upload-zone-text';
    if (block.src && !block.src.startsWith('data:')) {
      zoneText.innerHTML = `已选择：<span>${block.src.split('/').pop()}</span>（点击重新选择）`;
    } else if (pendingUploads[`block_${idx}_video`]) {
      zoneText.innerHTML = `已选择：<span>${pendingUploads[`block_${idx}_video`].filename}</span>（点击重新选择）`;
    } else {
      zoneText.innerHTML = '点击选择视频文件';
    }
    fileInput.addEventListener('change', () => {
      const file = fileInput.files[0];
      if (!file) return;
      const reader = new FileReader();
      reader.onload = e => {
        const base64Data = e.target.result.split(',')[1];
        const ext = file.name.split('.').pop();
        const fname = `p${currentId}-video-${idx}-${Date.now()}.${ext}`;
        const targetPath = `assets/videos/${fname}`;
        pendingUploads[`block_${idx}_video`] = { base64: base64Data, filename: fname, targetPath };
        block.src = targetPath;
        delete block.url;
        zoneText.innerHTML = `已选择：<span>${fname}</span>（点击重新选择）`;
      };
      reader.readAsDataURL(file);
    });
    zone.appendChild(fileInput);
    zone.appendChild(zoneText);
    group.appendChild(label);
    group.appendChild(zone);
    frag.appendChild(group);
  }
  return frag;
}

// ── Single image upload helper ──
// field = 'src' (image-single), 'image' (image-text)
function buildSingleImageUpload(block, blockIdx, field, imgIdx) {
  const zone = document.createElement('div');
  zone.className = 'upload-zone';
  const fileInput = document.createElement('input');
  fileInput.type = 'file'; fileInput.accept = 'image/*';

  const zoneText = document.createElement('div');
  zoneText.className = 'upload-zone-text';

  function updateZoneText() {
    const src = block[field];
    if (src && src.startsWith('data:')) {
      // pending
      zoneText.innerHTML = '已选择图片 <span>（点击重新上传）</span>';
      // show thumb
      let existingThumb = zone.querySelector('img');
      if (!existingThumb) {
        existingThumb = document.createElement('img');
        existingThumb.style.cssText = 'width:100%;max-height:120px;object-fit:cover;border-radius:6px;margin-top:8px;pointer-events:none;';
        zone.appendChild(existingThumb);
      }
      existingThumb.src = src;
    } else if (src) {
      zoneText.innerHTML = `当前图片：<span>${src.split('/').pop()}</span>（点击重新上传）`;
    } else {
      zoneText.innerHTML = '点击选择图片 <span>或拖入文件</span>';
    }
  }

  fileInput.addEventListener('change', () => {
    const file = fileInput.files[0];
    if (!file) return;
    const reader = new FileReader();
    reader.onload = e => {
      const base64Data = e.target.result.split(',')[1];
      const ext = file.name.split('.').pop();
      const fname = `p${currentId}-${field}-${blockIdx}-${Date.now()}.${ext}`;
      const targetPath = `assets/images/projects/${fname}`;
      pendingUploads[`block_${blockIdx}_${field}`] = { base64: base64Data, filename: fname, targetPath };
      block[field] = e.target.result; // data URL for preview
      updateZoneText();
    };
    reader.readAsDataURL(file);
  });

  zone.appendChild(fileInput);
  zone.appendChild(zoneText);
  updateZoneText();
  return zone;
}

// ─────────────────────────────────────────
// New block default templates
// ─────────────────────────────────────────
function newBlock(type) {
  const id = 'b' + Date.now();
  switch(type) {
    case 'heading':       return { id, type, level: 2, content: '' };
    case 'body':          return { id, type, content: '' };
    case 'quote':         return { id, type, content: '' };
    case 'image-single':  return { id, type, src: '', caption: '' };
    case 'image-carousel':return { id, type, images: [], captions: [] };
    case 'image-stack':   return { id, type, images: [] };
    case 'image-text':    return { id, type, layout: 'left', image: '', caption: '', text: '' };
    case 'video':         return { id, type, videoType: 'embed', url: '' };
    default: return { id, type };
  }
}

// ─────────────────────────────────────────
// Status
// ─────────────────────────────────────────
function setStatus(msg, cls) {
  const el = document.getElementById('status-msg');
  el.textContent = msg;
  el.className = cls;
}

// ─────────────────────────────────────────
// Collect current form data into currentData
// ─────────────────────────────────────────
function collectData() {
  currentData.title = document.getElementById('project-title-input').value.trim() || `项目 ${currentId}`;
  // blocks are mutated in place by event listeners
}

// ─────────────────────────────────────────
// Publish
// ─────────────────────────────────────────
async function publishProject() {
  if (!config.owner || !config.repo || !config.token) {
    openModal();
    return;
  }

  collectData();
  const btn = document.getElementById('btn-publish');
  btn.disabled = true;
  setStatus('准备发布…', '');

  try {
    // 1. Upload pending images/videos
    const uploadKeys = Object.keys(pendingUploads);
    for (let i = 0; i < uploadKeys.length; i++) {
      const key = uploadKeys[i];
      const up = pendingUploads[key];
      setStatus(`上传文件 ${i+1}/${uploadKeys.length}：${up.filename}`, '');
      await ghPut(up.targetPath, up.base64, `upload ${up.filename}`);
      // Replace data URL / preview with real path in blocks
      updateBlockImagePath(key, up.targetPath);
    }
    pendingUploads = {};

    // 2. Write project-N.json
    setStatus('写入项目数据…', '');
    const projectJson = JSON.stringify(currentData, null, 2);
    await ghPut(`data/project-${currentId}.json`, textToBase64(projectJson), `update project-${currentId}`);

    // 3. Update projects.json entry
    setStatus('更新项目列表…', '');
    const proj = projects.find(p => p.id === currentId);
    if (proj) {
      proj.title = currentData.title;
    } else {
      projects.push({ id: currentId, title: currentData.title, type: '', year: '', desc: '', thumb: '' });
    }
    const projectsJson = JSON.stringify(projects, null, 2);
    await ghPut('data/projects.json', textToBase64(projectsJson), `update projects.json`);

    setStatus('发布成功 ✓ 约 1-2 分钟后生效', 'ok');
  } catch(e) {
    setStatus('发布失败：' + e.message, 'err');
    console.error(e);
  } finally {
    btn.disabled = false;
  }
}

// Replace data URL in blocks after upload completes
function updateBlockImagePath(key, realPath) {
  // key patterns: block_{idx}_{field}, block_{idx}_carousel_{imgIdx}, block_{idx}_stack_{imgIdx}, block_{idx}_video
  const parts = key.split('_');
  const bIdx = parseInt(parts[1]);
  const block = currentData.blocks[bIdx];
  if (!block) return;

  if (parts[2] === 'carousel') {
    const imgIdx = parseInt(parts[3]);
    if (block.images) block.images[imgIdx] = realPath;
  } else if (parts[2] === 'stack') {
    const imgIdx = parseInt(parts[3]);
    if (block.images) block.images[imgIdx] = realPath;
  } else if (parts[2] === 'video') {
    block.src = realPath;
  } else {
    const field = parts[2]; // 'src', 'image'
    block[field] = realPath;
  }
}

// ─────────────────────────────────────────
// Event bindings
// ─────────────────────────────────────────
document.getElementById('btn-gh-settings').addEventListener('click', openModal);
document.getElementById('btn-modal-cancel').addEventListener('click', closeModal);
document.getElementById('btn-modal-save').addEventListener('click', saveConfig);
document.getElementById('modal-overlay').addEventListener('click', e => {
  if (e.target === document.getElementById('modal-overlay')) closeModal();
});

document.getElementById('btn-add-block-toggle').addEventListener('click', () => {
  document.getElementById('add-block-menu').classList.toggle('open');
});

document.querySelectorAll('.add-type-btn').forEach(btn => {
  btn.addEventListener('click', () => {
    const block = newBlock(btn.dataset.type);
    currentData.blocks.push(block);
    document.getElementById('add-block-menu').classList.remove('open');
    renderBlockList();
    // Scroll to last block
    const items = document.querySelectorAll('.block-item');
    if (items.length) items[items.length-1].scrollIntoView({ behavior: 'smooth', block: 'nearest' });
  });
});

document.getElementById('btn-preview').addEventListener('click', () => {
  if (!currentId) return;
  window.open(`project.html?id=${currentId}`, '_blank');
});

document.getElementById('btn-publish').addEventListener('click', publishProject);

// ─────────────────────────────────────────
// Init
// ─────────────────────────────────────────
loadConfig();
loadProjects();

// Auto-open GitHub settings if not configured
if (!config.owner || !config.repo || !config.token) {
  setTimeout(openModal, 300);
}
</script>
</body>
</html>
```

- [ ] **Step 2：浏览器打开验证骨架**

用浏览器打开 `file:///Users/admin/Desktop/作品集/admin.html`，确认：
- 顶部栏显示「作品集管理」+ GitHub 设置按钮 ✓
- 左侧显示 4 个项目（从 projects.json 加载，file:// 下可能 fallback 到静态列表）✓
- 右侧显示空状态「从左侧选择一个项目开始编辑」✓
- GitHub 配置弹窗自动打开（因未配置）✓
- 底部栏「预览」「发布」按钮可见 ✓

---

## Task 2：集成测试——块编辑器完整流程

**文件：**
- 修改：`/Users/admin/Desktop/作品集/admin.html`（如有 bug 修复）

> 说明：admin.html 是纯前端工具，无自动化测试框架。此 Task 通过浏览器手动验证所有核心功能。

- [ ] **Step 1：点击项目 1，验证加载**

在左侧点击「项目 1」，验证：
- 左侧高亮切换到项目 1 ✓
- 右侧出现标题输入框，内容为「AI 课堂 · 奇思妙问」✓
- 块列表展示 13 个块 ✓
- 每个块有类型徽章 + 上移/下移/删除按钮 ✓

- [ ] **Step 2：验证标题块编辑**

点击第一个「标题」块，检查编辑区：
- 显示「级别」下拉（H1/H2/H3）和「标题文字」输入框 ✓
- 修改内容后切换到别的项目再切回来，内容仍在（内存中）✓

- [ ] **Step 3：验证正文块**

检查「正文」块：
- 显示多行 textarea ✓
- 可输入换行 ✓

- [ ] **Step 4：验证引言块**

检查「引言」块：
- 显示单行输入框 ✓

- [ ] **Step 5：验证图片上传区域**

检查「单图」块：
- 显示上传区域（虚线边框）✓
- 选择一张本地图片后显示缩略图预览 ✓
- caption 输入框可编辑 ✓

检查「轮播图」块：
- 显示「+ 添加图片」按钮 ✓
- 点击后出现新图片行，可上传图片，可填 caption ✓
- × 按钮可删除图片行 ✓

检查「堆叠图」块：
- 类似轮播，但无 caption 输入框 ✓

检查「图文」块：
- 「图片在左/图片在右」切换按钮 ✓
- 图片上传区 + caption + 配文 textarea ✓

- [ ] **Step 6：验证视频块**

检查「视频」块：
- 默认显示「粘贴链接」选项 ✓
- 粘贴链接模式：可输入 B 站/YouTube URL ✓
- 切换到「上传视频文件」：显示文件上传区 ✓

- [ ] **Step 7：验证块排序和删除**

- 点击某块的「↑」上移，块列表重新渲染正确 ✓
- 点击「↓」下移同理 ✓
- 最顶块「↑」按钮显示 disabled ✓
- 最底块「↓」按钮显示 disabled ✓
- 点击「×」删除，块从列表消失 ✓

- [ ] **Step 8：验证添加新块**

点击「添加内容块」按钮，展开 8 种类型菜单 ✓
点击「正文」，列表末尾出现新正文块 ✓
再次点击「添加内容块」，菜单收起 ✓

- [ ] **Step 9：验证预览按钮**

点击「预览」：在新标签页打开 `project.html?id=1` ✓

---

## Task 3：发布流程验证（需真实 GitHub Token）

**文件：**
- 只涉及运行时逻辑，无文件修改（如发现 bug 则修改 admin.html）

> 说明：此 Task 依赖用户拥有 GitHub 仓库和 Personal Access Token。如无法测试，跳过并标记为「待用户验证」。

- [ ] **Step 1：配置 GitHub 信息**

点击「GitHub 设置」，填入：
- GitHub 用户名
- 仓库名（portfolio 或实际仓库名）
- Personal Access Token（需 `contents:write` 权限）

点击「保存」，弹窗关闭 ✓

- [ ] **Step 2：发布一次（不含图片上传）**

选择项目 1，修改标题，点击「发布」：
- 状态栏依次显示「准备发布…」→「写入项目数据…」→「更新项目列表…」→「发布成功 ✓」✓
- GitHub 仓库 `data/project-1.json` 文件内容更新 ✓

- [ ] **Step 3：发布含图片**

选择一张本地图片到某个块的图片区域，点击「发布」：
- 状态栏显示「上传文件 1/1：p1-img-…」→「写入项目数据…」→「发布成功 ✓」✓
- GitHub 仓库 `assets/images/projects/` 目录下出现对应文件 ✓
- `data/project-1.json` 中图片路径从 data URL 替换为 `assets/images/projects/xxx.jpg` ✓

---

## 自检（Spec Coverage）

| 设计文档要求 | 已覆盖 |
|---|---|
| 顶部栏 + GitHub 设置按钮 | ✅ Task 1 |
| 左侧边栏 4 个项目 | ✅ Task 1 |
| 项目名称编辑框 | ✅ Task 1 |
| 块列表 + 类型徽章 | ✅ Task 1 |
| 上移/下移/删除按钮 | ✅ Task 1 |
| 添加块菜单（8种）| ✅ Task 1 |
| heading 编辑器（H1/H2/H3 + 文本）| ✅ Task 1 |
| body 编辑器（textarea）| ✅ Task 1 |
| quote 编辑器（单行）| ✅ Task 1 |
| image-single 编辑器 + caption | ✅ Task 1 |
| image-carousel 多图上传 + caption | ✅ Task 1 |
| image-stack 多图上传 | ✅ Task 1 |
| image-text 布局切换 + 图片 + 配文 | ✅ Task 1 |
| video embed/upload 切换 | ✅ Task 1 |
| 图片 FileReader base64 | ✅ Task 1 |
| GitHub 配置存 localStorage | ✅ Task 1 |
| GitHub API PUT 上传图片 | ✅ Task 1 |
| GitHub API PUT 写 JSON | ✅ Task 1 |
| 发布进度状态显示 | ✅ Task 1 |
| 预览按钮（project.html?id=N）| ✅ Task 1 |
| 无密码保护 | ✅（直接访问无任何认证）|
| 视觉：Inter 字体 + #10b981 主色 | ✅ Task 1 |
