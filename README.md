# skill-web-search

> OpenClaw Skill：网页正文提取与内容抓取

永久免费、支持微信公众号的网页内容提取工具，4 级降级策略自动选择最优方案。

## 功能特性

- 🌐 **支持所有主流平台**：微信公众号、知乎、CSDN、GitHub、小红书、Medium 等
- 📄 **干净 Markdown 输出**：保留标题层级、超链接、图片 URL、列表代码块
- 🔄 **4 级降级策略**：Jina → Scrapling → Browser → web_fetch，失败自动切换
- 🔓 **无 API 限制**：Scrapling 方案完全免费，无需注册
- 🐚 **零配置**：安装依赖后直接使用，无需 API Key

## 支持的网站

| 网站 | 推荐方案 | 备注 |
|------|---------|------|
| 普通网页（GitHub/博客/新闻） | Jina | 最快最干净 |
| 微信公众号 | Scrapling | 唯一稳定方案 |
| 知乎、掘金、CSDN、小红书 | Scrapling | Jina 经常失败 |
| 需要 JS 渲染的页面 | Browser | 最终保底 |
| 静态页面 | web_fetch | 最低优先级 |

## 安装

### 1. 安装 Python 依赖

```bash
pip3 install scrapling html2text playwright --break-system-packages
playwright install chromium
```

### 2. 复制 Skill 文件

将 `SKILL.md` 和 `scripts/fetch.py` 复制到 OpenClaw skills 目录：

```
~/.openclaw/workspace/skills/skill-web-search/
├── SKILL.md
└── scripts/
    └── fetch.py
```

### 3. 重启 OpenClaw

Skill 会自动加载，无需额外配置。

## 使用方式

### 自动模式（推荐）

直接发送链接并说明需求：

```
帮我读取这篇文章：https://mp.weixin.qq.com/s/xxx
总结这个链接的内容：https://juejin.cn/post/xxx
提取网页内容：https://example.com/article
```

Skill 会自动选择最优提取方案。

### 触发词

- "读取"、"总结"、"提取"、"抓取"、"分析" + URL
- "帮我看看这篇文章"
- "这个链接的内容"
- 直接发送 URL 请求读取

## 提取策略详解

### 策略 1：Jina Reader（首选）

```bash
web_fetch("https://r.jina.ai/<url>", maxChars=30000)
```

- 速度：~1.5s
- 优点：格式最干净，速度最快
- 限制：200次/天免费配额；微信公众号 403

### 策略 2：Scrapling + html2text（主力）

```bash
python3 scripts/fetch.py <url> 30000
```

- 速度：~5-15s
- 优点：无 API 限制，能读微信公众号
- 依赖：scrapling、html2text、playwright

### 策略 3：Browser Reading Mode（最终手段）

```bash
agent-browser open "<url>"
agent-browser wait --load networkidle
agent-browser get text "#js_content" --json
```

- 速度：~15-30s
- 优点：成功率最高，能读任何 JS 渲染页面
- 适用：策略 2 失败后的最终尝试

### 策略 4：web_fetch 直接抓（兜底）

```bash
web_fetch(url, maxChars=30000)
```

- 返回原始 HTML，含广告/导航
- 最低优先级

## 项目结构

```
skill-web-search/
├── SKILL.md           # OpenClaw Skill 指令
├── README.md          # 本文档
└── scripts/
    └── fetch.py       # Scrapling + html2text 提取脚本
```

## 技术栈

- **Jina Reader** — 网页转 Markdown API（免费配额）
- **Scrapling** — 绕过反爬的网页抓取库
- **html2text** — HTML 转 Markdown
- **Playwright** — 浏览器自动化

## License

MIT
