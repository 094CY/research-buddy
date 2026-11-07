# 写作风格与格式规范

## 语气与立场

- **客观中立**：呈现事实和分析供参考，不替用户做商业决策
- **不确定性表达**：未经验证的信息用"官方宣称"、"第三方引用"等限定词
- **数据时效标注**：波动性数据（GitHub Stars、月活等）标注"截至调研时"

## 格式规范

### 加粗标记
在表格和正文中用 **加粗** 标记核心差异点和关键结论。

### 脚注补充
表格中需要额外说明的内容使用上标数字 + 脚注：
```markdown
| 产品 | 公式识别 |
|------|:---:|
| 产品A | 部分¹ |

> ¹ 仅支持简单数学公式OCR，非完整LaTeX输出
```

### 来源标注
每个产品小节开头标注信息来源：
```markdown
> 来源：[产品官网](URL)、[API文档](URL)、[GitHub](URL)
```

### 能力标注符号
| 符号 | 含义 |
|------|------|
| ✅ | 官方明确支持 |
| ❌ | 官方明确不支持或未提供 |
| 部分 | 有限支持，需脚注说明 |
| 未明确 | 官方未公开相关信息 |

---

## HTML 定稿框架规范

**⛔ 触发条件（硬性拦截）**：仅当用户明确说"生成汇报版"、"转HTML"、"最终定稿"、"出HTML"、"定稿"时，方可输出 HTML。在此之前，**禁止输出任何 `.html` 文件**，即使用户说"帮我做一份调研报告"也只输出 MD。

**⛔ 组件规范（硬性约束）**：
- **禁止自创组件**替代本规范定义的组件。若本规范未定义某种样式，使用最接近的已定义组件，不得新增自定义 CSS 类
- 多厂商评估章节**必须使用 `.vc` 厂商卡片组件**，禁止使用 `h4 + ul + p` 碎片堆叠，也禁止用任何自命名卡片（如 `.product-card`、`.vendor-item`）替代
- 色彩变量**必须使用**本规范定义的 CSS 变量，禁止另起一套变量体系

---

### 必备框架元素

#### 1. 全局基础样式

```css
:root {
  /* 色彩方案 — 根据报告主题选择主色调，以下为深蓝色系默认值 */
  --primary: #1e3a5f;        /* 主色（深）：侧边栏标题、h2、表头背景；随主题替换 */
  --accent: #2563eb;         /* 强调色：链接、active、左边框高亮；与主色同色系亮色 */
  --sidebar-bg: #f8f9fa;     /* 目录背景：固定浅灰，不随主题变 */
  --card-bg: #ffffff;        /* 卡片背景：固定白色 */
  --text-primary: #2d3748;   /* 正文颜色：固定深灰，不随主题变 */
  --text-light: #718096;     /* 次要文字：固定中灰 */
  --border: #e2e8f0;         /* 边框：固定浅灰 */
  --sidebar-width: 260px;    /* 目录宽度：固定 */
}

* { box-sizing: border-box; margin: 0; padding: 0; }
body {
  font-family: -apple-system, BlinkMacSystemFont, 'PingFang SC', 'Segoe UI', sans-serif;
  color: var(--text-primary);
  background: #f1f5f9;   /* 页面底色：浅蓝灰，与白色卡片形成层次 */
  font-size: 15px;
  line-height: 1.7;
}
p { margin-bottom: 12px; }
ul, ol { margin: 8px 0 12px 20px; }
li { margin-bottom: 4px; }
strong { color: var(--text-primary); }
```

#### 2. 布局架构（容器式，必须严格遵守）

**⚠️ 必读**：采用**容器式布局**，Hero 和所有卡片都在 `.content` 内，天然等宽对齐。

```css
/* ✅ 正确 */
#main { margin-left: var(--sidebar-width); }
.content { max-width: 980px; margin: 0 auto; padding: 36px 28px 80px; }
```

```html
<div id="main">
  <div class="content">
    <header class="hero">...</header>
    <div class="metrics-row">...</div>   <!-- 可选 -->
    <section class="card">...</section>
  </div>
</div>
```

```css
/* ❌ 错误 — 各元素各自设 margin-left，padding 差异导致宽度不一致 */
header.hero { margin-left: 260px; width: 960px; }
main { margin-left: 260px; width: 960px; }
```

#### 3. 左侧固定目录导航

```html
<nav id="sidebar">
  <div class="sidebar-title">目录导航</div>
  <ul>
    <li class="l1"><a href="#s1">一、章节标题</a></li>
    <li class="l2"><a href="#s1-1">1.1 小节</a></li>
    <li class="l3"><a href="#s1-1-1">子节（可选）</a></li>
  </ul>
</nav>
```

```css
#sidebar {
  position: fixed; top: 0; left: 0; width: var(--sidebar-width);
  height: 100vh; background: var(--sidebar-bg);
  border-right: 1px solid var(--border);
  overflow-y: auto; z-index: 100; padding: 20px 0;
}
#sidebar .sidebar-title {
  font-size: 0.75rem; font-weight: 700; color: #94a3b8;
  text-transform: uppercase; letter-spacing: 0.1em;
  padding: 0 18px 12px; border-bottom: 1px solid var(--border); margin-bottom: 10px;
}
#sidebar ul { list-style: none; padding: 0; }
#sidebar ul li a {
  display: block; padding: 5px 18px;
  color: var(--text-light); text-decoration: none; font-size: 0.82rem;
  transition: all 0.15s; border-left: 2px solid transparent;
}
#sidebar ul li a:hover { color: var(--accent); background: #e8f0fe; border-left-color: var(--accent); }
#sidebar ul li a.active { color: var(--accent); font-weight: 600; border-left-color: var(--accent); background: #eff6ff; }
/* 三级缩进 */
#sidebar ul li.l1 > a { font-size: 0.88rem; font-weight: 600; color: var(--text-primary); padding-top: 10px; }
#sidebar ul li.l2 > a { padding-left: 30px; }
#sidebar ul li.l3 > a { padding-left: 44px; font-size: 0.79rem; }
```

- `IntersectionObserver` 实现滚动自动高亮（JS见第12节）
- 移动端（<768px）隐藏，改为顶部按钮触发（见响应式）

#### 4. 顶部 Hero 渐变区

```html
<header class="hero">
  <h1>{报告主题}</h1>
  <p class="subtitle">{聚焦方向描述}</p>
  <div class="meta">调研时间：{年月} | 信息来源：各厂商官网、官方文档</div>
  <div class="tags">
    <span class="tag">技术演进</span>
    <span class="tag">国内厂商</span>
    <!-- 报告聚焦的3-8个关键词标签 -->
  </div>
</header>
```

```css
header.hero {
  /* 渐变方向固定 135deg；色值根据报告主题定制：
     - 专业/技术类：深蓝系 #1e3a5f → #1e40af → #1d4ed8
     - 商业/市场类：深紫系 #1e1b4b → #3730a3 → #4f46e5
     - 风险/安全类：深灰系 #1f2937 → #374151 → #4b5563
     原则：三色渐变，首色最深作为锚点，末色最鲜亮，整体保持深色以确保白色文字可读 */
  background: linear-gradient(135deg, {主色深} 0%, {主色中} 60%, {主色亮} 100%);
  border-radius: 14px; padding: 44px 40px; margin-bottom: 28px; color: white;
}
header.hero h1 { font-size: 2rem; font-weight: 800; margin-bottom: 10px; }
/* subtitle/meta/tag 颜色根据 Hero 背景主题色调整，保持在深色背景上可读即可 */
/* 参考：深蓝主题下 subtitle≈#bfdbfe（浅蓝），meta≈#94a3b8（灰），tag≈#e0f2fe（更浅） */
header.hero .subtitle { font-size: 1.05rem; color: /* 主题色浅化版，与背景形成层次 */ #bfdbfe; margin-bottom: 16px; }
header.hero .meta { font-size: 0.82rem; color: /* 比subtitle更浅/更灰 */ #94a3b8; }
header.hero .tags { margin-top: 16px; display: flex; gap: 8px; flex-wrap: wrap; }
header.hero .tag {
  background: rgba(255,255,255,0.15); border: 1px solid rgba(255,255,255,0.25);
  padding: 3px 12px; border-radius: 20px; font-size: 0.78rem;
  color: /* 最浅，接近白色 */ #e0f2fe;
}
```

#### 5. 关键指标行（Hero 下方，可选）

当报告有3-5个核心数字值得突出时，在 Hero 下方紧接着插入指标行（不放在 `.card` 内，作为独立行）。

```html
<div class="metrics-row">
  <div class="metric-card">
    <div class="metric-value">40k+</div>
    <div class="metric-label">{产品名} GitHub Stars<br>(截至调研时)</div>
  </div>
  <!-- 3-4个为宜，超过4个换行显示 -->
</div>
```

```css
.metrics-row { display: grid; grid-template-columns: repeat(4, 1fr); gap: 14px; margin: 18px 0; }
.metric-card {
  background: linear-gradient(135deg, #f0f9ff, #e0f2fe);
  border: 1px solid #bae6fd; border-radius: 10px; padding: 16px; text-align: center;
}
.metric-card .metric-value { font-size: 1.6rem; font-weight: 800; color: #0369a1; }
.metric-card .metric-label { font-size: 0.78rem; color: var(--text-light); margin-top: 4px; }
```

#### 6. 章节卡片化布局

```html
<section class="card" id="s1">
  <h2>一、章节标题</h2>
  <h3 id="s1-1">1.1 小节标题</h3>
  <h4>子节标题（可选）</h4>
  <!-- 章节内容 -->
</section>
```

```css
.card {
  background: var(--card-bg); border-radius: 12px;
  box-shadow: 0 1px 4px rgba(0,0,0,0.06); border: 1px solid var(--border);
  padding: 32px 36px; margin-bottom: 24px;
}
.card h2 { font-size: 1.3rem; font-weight: 700; color: var(--primary); margin-bottom: 18px; padding-bottom: 12px; border-bottom: 2px solid #e0e7ff; }
.card h3 { font-size: 1.05rem; font-weight: 700; color: #1e40af; margin: 22px 0 10px; }
.card h4 { font-size: 0.95rem; font-weight: 700; color: var(--text-primary); margin: 16px 0 8px; }
```

#### 7. 增强型表格

```html
<div class="table-wrap">
  <table class="data-table">
    <thead><tr><th>列一</th><th>列二</th></tr></thead>
    <tbody>
      <tr><td><strong>关键项</strong></td><td class="center">✅</td></tr>
    </tbody>
  </table>
</div>
```

```css
.table-wrap { overflow-x: auto; margin: 16px 0; }
.data-table { width: 100%; border-collapse: collapse; font-size: 0.83rem; }
.data-table thead tr { background: var(--primary); color: white; }
.data-table thead th { padding: 10px 14px; text-align: left; font-weight: 600; }
.data-table tbody tr:nth-child(even) { background: #f8fafc; }
.data-table tbody td { padding: 9px 14px; border-bottom: 1px solid #f0f4f8; vertical-align: top; }
.data-table .center { text-align: center; }
```

#### 8. 信息提示框（三种语义）

根据内容性质选择对应类型，不可混用：

```html
<!-- 蓝色：补充说明、背景知识、中性提示 -->
<div class="info-box">
  <strong>标题（可选）：</strong>说明内容...
</div>

<!-- 橙色：注意事项、已知局限、风险提示 -->
<div class="warn-box">
  <strong>注意：</strong>警示内容...
</div>

<!-- 绿色：核心结论、关键洞察、正面判断 -->
<div class="insight-box">
  <strong>核心结论：</strong>洞察内容...
</div>
```

```css
.info-box    { background: #eff6ff; border-left: 4px solid var(--accent); padding: 14px 18px; border-radius: 6px; margin: 16px 0; font-size: 0.88rem; }
.warn-box    { background: #fff7ed; border-left: 4px solid #f97316;       padding: 14px 18px; border-radius: 6px; margin: 16px 0; font-size: 0.88rem; }
.insight-box { background: #f0fdf4; border-left: 4px solid #22c55e;       padding: 14px 18px; border-radius: 6px; margin: 16px 0; font-size: 0.88rem; }
```

#### 9. 来源引用 `.source`

放在每个产品小节或章节开头，用于标注信息来源链接：

```html
<p class="source">来源：<a href="URL">产品官网</a> | <a href="URL">官方文档</a> | 核查时间：2026-05</p>
```

```css
.source { font-size: 0.78rem; color: var(--text-light); margin-bottom: 14px; }
.source a { color: var(--accent); text-decoration: none; }
```

#### 10. 时间轴组件（技术演进、版本历史专用）

用于展示时间序列内容（如技术代际演进、产品版本历史），替代普通有序列表：

```html
<div class="timeline">
  <div class="timeline-item">
    <div class="t-date">第一代 · 2022-2023</div>
    <div class="t-title">Naive RAG</div>
    <div class="t-desc">三步走：文档切块 → 向量检索 → 提示注入生成。</div>
  </div>
  <div class="timeline-item">
    <div class="t-date">第二代 · 2023-2024</div>
    <div class="t-title">Advanced RAG</div>
    <div class="t-desc">引入查询改写、Rerank、HyDE等优化策略。</div>
  </div>
</div>
```

```css
.timeline { border-left: 3px solid #bfdbfe; padding-left: 24px; margin: 16px 0; }
.timeline-item { position: relative; margin-bottom: 18px; }
.timeline-item::before {
  content: ''; position: absolute; left: -31px; top: 6px;
  width: 12px; height: 12px; border-radius: 50%;
  background: var(--accent); border: 2px solid white; box-shadow: 0 0 0 2px var(--accent);
}
.timeline-item .t-date  { font-size: 0.78rem; color: var(--accent); font-weight: 700; margin-bottom: 3px; }
.timeline-item .t-title { font-weight: 600; font-size: 0.9rem; }
.timeline-item .t-desc  { font-size: 0.83rem; color: var(--text-light); margin-top: 3px; }
```

#### 11. 状态徽章（趋势判断、置信度、结论标注专用）

用于表格中标注判断结论的置信度或状态（如趋势判断表的"高/中/否"列）：

```html
<span class="badge badge-green">高</span>
<span class="badge badge-blue">中</span>
<span class="badge badge-yellow">待定</span>
<span class="badge badge-red">否</span>
```

```css
.badge { display: inline-block; padding: 2px 8px; border-radius: 10px; font-size: 0.75rem; font-weight: 600; }
.badge-green  { background: #dcfce7; color: #166534; }
.badge-blue   { background: #dbeafe; color: #1e40af; }
.badge-yellow { background: #fef9c3; color: #92400e; }
.badge-red    { background: #fee2e2; color: #991b1b; }
```

#### 12. 厂商评估卡片组件（多厂商逐一评估专用）

当某章节需要**逐一评估多个厂商**时，禁止使用 `h4 + ul + p` 碎片堆叠，必须使用 `.vc` 卡片组件。

```html
<div class="vendor-grid">
  <div class="vc" id="s2-1">
    <div class="vc-head">
      <span class="vc-flag">🇨🇳</span>
      <span class="vc-name">厂商名称</span>
      <span class="vc-badge badge-top">核心定位标签</span>
      <span class="vc-stars">★★★★★</span>
    </div>
    <div class="vc-body">
      <p class="source">来源：<a href="URL">官网</a> | 核查时间：2026-05</p>
      <table class="vc-table">
        <tr><td>维度一</td><td>能力描述</td></tr>
        <tr><td>维度二</td><td>能力描述</td></tr>
      </table>
    </div>
    <div class="vc-footer">
      <div class="vc-pros"><strong>核心优势</strong><ul><li>…</li></ul></div>
      <div class="vc-cons"><strong>核心局限</strong><ul><li>…</li></ul></div>
    </div>
  </div>
</div>
```

```css
.vendor-grid { display: flex; flex-direction: column; gap: 18px; margin: 18px 0; }
.vc { background: #fff; border: 1px solid var(--border); border-radius: 10px; overflow: hidden; }
.vc-head { display: flex; align-items: center; gap: 12px; padding: 12px 18px; background: linear-gradient(90deg,#f8fafc,#f1f5f9); border-bottom: 1px solid var(--border); }
.vc-head .vc-flag  { font-size: 1.2rem; }
.vc-head .vc-name  { font-size: 1rem; font-weight: 700; }
.vc-head .vc-stars { color: #f59e0b; margin-left: auto; font-size: 0.9rem; }
.vc-badge { font-size: .71rem; font-weight: 600; padding: 3px 9px; border-radius: 12px; }
.badge-top  { background: #dcfce7; color: #166534; }   /* 绿：领导者/最强 */
.badge-good { background: #dbeafe; color: #1e40af; }   /* 蓝：优秀/推荐 */
.badge-mid  { background: #fef9c3; color: #92400e; }   /* 黄：中等/特定场景 */
.badge-eco  { background: #f3e8ff; color: #6b21a8; }   /* 紫：生态/平台型 */
.vc-body { padding: 14px 18px; }
.vc-table { width: 100%; border-collapse: collapse; font-size: 0.81rem; }
.vc-table tr { border-bottom: 1px solid #f1f5f9; }
.vc-table tr:last-child { border-bottom: none; }
.vc-table td:first-child { width: 110px; color: var(--text-light); font-weight: 600; padding: 5px 8px 5px 0; }
.vc-footer { display: grid; grid-template-columns: 1fr 1fr; border-top: 1px solid #f1f5f9; }
.vc-pros { background: #f0fdf4; padding: 12px 16px; }
.vc-cons { background: #fff7ed; padding: 12px 16px; }
.vc-pros strong { color: #15803d; font-size: 0.82rem; }
.vc-cons strong { color: #c2410c; font-size: 0.82rem; }
.vc-pros ul, .vc-cons ul { margin: 6px 0 0 14px; font-size: 0.8rem; }
```

**设计原则：**
- `.vc-table` 的维度行标签根据场景定制（RAG重Chunk/溯源/集成；数据清洗重批量/成本/私有化）
- 优势/局限双栏绿橙对比强，比单段落局限更易识别
- 星级评分（`★`）在头部展示，不在卡片末尾重复
- 厂商卡片的 `id` 属性与侧边栏锚点一一对应

#### 13. 响应式设计

```css
@media (max-width: 768px) {
  #sidebar { display: none; }          /* 移动端隐藏侧边栏 */
  #main { margin-left: 0; }
  .content { padding: 16px; }
  .metrics-row { grid-template-columns: repeat(2, 1fr); }  /* 指标行2列 */
  .vc-footer { grid-template-columns: 1fr; }               /* 优劣势单列 */
  header.hero { padding: 24px 20px; }
  header.hero h1 { font-size: 1.4rem; }
}
```

> 移动端汉堡菜单可按需添加，不强制要求（调研报告主要在桌面端阅读）。

#### 14. 交互 JS（滚动高亮 + 平滑跳转）

```html
<script>
// 滚动时自动高亮当前章节
const sections = document.querySelectorAll('[id]');
const sidebarLinks = document.querySelectorAll('#sidebar a');
const observer = new IntersectionObserver((entries) => {
  entries.forEach(entry => {
    if (entry.isIntersecting) {
      const id = entry.target.id;
      sidebarLinks.forEach(link => {
        link.classList.toggle('active', link.getAttribute('href') === '#' + id);
      });
    }
  });
}, { rootMargin: '-80px 0px -60% 0px', threshold: 0 });
sections.forEach(s => observer.observe(s));

// 点击目录平滑滚动
sidebarLinks.forEach(link => {
  link.addEventListener('click', e => {
    e.preventDefault();
    const target = document.querySelector(link.getAttribute('href'));
    if (target) target.scrollIntoView({ behavior: 'smooth', block: 'start' });
  });
});
</script>
```

---

## 组件选用决策树

| 内容类型 | 使用组件 |
|---------|---------|
| 时间序列（技术演进/版本历史） | `.timeline` |
| 多厂商逐一评估 | `.vendor-grid > .vc` |
| 横向能力对比矩阵 | `.data-table`（`div.table-wrap` 包裹） |
| 核心数字指标（3-5个） | `.metrics-row > .metric-card` |
| 关键结论/洞察 | `.insight-box`（绿） |
| 注意事项/风险/局限 | `.warn-box`（橙） |
| 补充说明/背景知识 | `.info-box`（蓝） |
| 趋势置信度/状态标注 | `.badge-*` 内联徽章 |
| 来源/核查时间标注 | `.source`（段落前） |
| 报告关键词标签 | `header.hero .tags > .tag` |

---

## 实际案例参考

> 详细的表格模板和 MD 填写实例统一维护在 `report-template.md` 中，此处不重复。
