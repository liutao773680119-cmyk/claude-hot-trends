---
name: 热点
description: |
  使用 Chrome DevTools MCP（mcp__chrome-devtools__* 工具）抓取各平台热点、热搜、热榜、排行榜等结构化数据。支持 16+ 平台：微博、知乎、Bilibili、Twitter/X、HackerNews、V2EX、Reddit、雪球、小红书、BOSS直聘、YouTube、ProductHunt 等。
  核心优势：直接拦截页面 API 响应拿干净 JSON，适合 DOM 超大页面，无需 Playwright 扩展插件。
  触发词：查热搜、看热榜、热点情报、今日热点、微博热搜、知乎热榜、B站热门、雪球行情、热点抓取、cdp抓取、chrome抓数据，以及任何"帮我看看XX平台今天有什么热点/热门/趋势"的请求。
---

# 热点

通过 Chrome DevTools MCP 直接拦截页面 API 响应，获取结构化 JSON 热点数据。**无需 Playwright 扩展插件**，只需 Chrome 以调试模式运行。

## 前提：启动 Chrome 调试模式

```powershell
# 关掉普通 Chrome 后执行（用独立 profile 避免干扰）
Start-Process "C:\Program Files\Google\Chrome\Application\chrome.exe" -ArgumentList "--remote-debugging-port=9222 --user-data-dir=C:\ChromeDebug"
```

验证：调用 `mcp__chrome-devtools__list_pages`，有返回即连通。

---

## 核心工作原理

```
导航到目标页面
    ↓
等待页面加载（页面自动触发后端 API）
    ↓
list_network_requests → 找到 JSON/XHR 请求
    ↓
get_network_request  → 获取完整响应体
    ↓
解析 JSON → 格式化输出
```

相比 Playwright，核心优势是**直接拿后端 JSON，不解析 DOM**，数据更干净、速度更快。

---

## 各平台抓取方法

详细 API 端点和解析路径见 [references/api-patterns.md](references/api-patterns.md)。

### 通用流程

```
1. new_page 或 navigate_page → 目标 URL
2. wait_for → 等待关键文字出现（如"热搜"、"排行"）
3. list_network_requests → 过滤 xhr/fetch，找含 api/ajax/json 的请求
4. get_network_request → 拿响应体
5. 解析关键字段，整理输出
```

### 各平台快速示例

```
微博热搜   → https://weibo.com/hot/search        → wait_for["热搜"] → snapshot/DOM 直读
知乎热榜   → https://www.zhihu.com/hot           → list_network_requests 找 hot-lists
B站热门    → https://www.bilibili.com/v/popular/rank/all → 找 /ranking/v2
雪球行情   → https://xueqiu.com/S/SH000001       → 找 quotec.json
HackerNews → https://news.ycombinator.com        → evaluate_script 读 DOM（SSR）
V2EX 热门  → https://www.v2ex.com/?tab=hot       → evaluate_script 读 DOM（SSR）
Reddit     → https://www.reddit.com/r/hot.json   → 直接导航到 .json 端点
Twitter趋势→ https://x.com/explore/tabs/trending → 找 TrendsByCategory GraphQL
```

---

## 搜索操作

```
1. navigate_page → 目标网站
2. wait_for → 搜索框出现
3. click → 搜索框
4. type_text → 关键词
5. press_key → "Enter"
6. wait_for → 结果加载完成
7. list_network_requests → 拦截搜索 API 响应
```

---

## 当 API 拦截失败时的降级

部分页面用 SSR 或 WebSocket 推送，拦截不到 JSON，改用 JS 注入：

```
evaluate_script → document.querySelectorAll('选择器')
                  .map(el => ({title: el.textContent, href: el.href}))
```

或先截图确认状态：`take_screenshot`

---

## 输出格式规范

```
| # | 标题 | 链接 | 热度/指标 |
|---|------|------|---------|
| 1 | 标题文字 | [🔗](url) | 111万 |
```

规则：
- 英文标题附中文翻译，格式：`原标题 / 中文译名`
- 链接列：`[🔗](url)` 紧凑图标
- 大数字用万/亿（如 `1.2万`）
- 保持平台原始排序

---

## 与 opencli 的分工

| 场景 | 推荐 |
|------|------|
| opencli 支持的常规命令 | opencli（命令式，更稳定）|
| 需要拦截 JSON API 数据 | **热点 skill**（数据更干净）|
| DOM 超大页面（雪球、知乎详情页）| **热点 skill** |
| Playwright 扩展不可用时 | **热点 skill** |
| 发帖、点赞等写操作 | opencli 或 Playwright |

---

## 故障排查

**"Could not connect to Chrome"** → Chrome 未以调试模式启动，执行上方命令

**list_network_requests 返回空** → 页面未加载完，先 `wait_for` 再抓

**get_network_request 拿不到 body** → SSR 页面，改用 `evaluate_script` 读 DOM

**需要登录的平台** → C:\ChromeDebug 是独立 profile，需手动登录一次，之后 cookie 持久保留
