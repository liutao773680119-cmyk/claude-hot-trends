# 🔥 热点 Skill — Claude Code 多平台热点抓取

> 使用 Chrome DevTools MCP 直接拦截页面 API 响应，一句话获取 16+ 平台热点数据。

## ✨ 功能特点

- **无需 Playwright 扩展**，只需 Chrome 以调试模式运行
- **直接拦截 XHR/fetch JSON**，数据比 DOM 解析更干净更快
- **支持 16+ 平台**：微博、知乎、B站、Twitter/X、HackerNews、V2EX、Reddit、雪球、小红书、BOSS直聘、YouTube、ProductHunt 等
- **自然语言触发**：说"查微博热搜"即可，无需记命令

## 🚀 快速安装

```bash
npx skills add liutao773680119-cmyk/claude-skill-热点
```

或手动复制 `SKILL.md` 到：
- **全局**：`~/.claude/skills/热点/SKILL.md`
- **项目内**：`.claude/skills/热点/SKILL.md`

## 📋 前提条件

Chrome 需以调试模式启动（一次性设置）：

```powershell
Start-Process "C:\Program Files\Google\Chrome\Application\chrome.exe" `
  -ArgumentList "--remote-debugging-port=9222 --user-data-dir=C:\ChromeDebug"
```

同时需要在 Claude Code 中安装 chrome-devtools MCP：

```bash
claude mcp add chrome-devtools --scope user -- npx -y chrome-devtools-mcp@latest --browserUrl http://localhost:9222
```

## 💬 触发示例

```
查微博热搜前10
看知乎热榜
B站今日热门
X上今天有什么热点
雪球股票行情
HackerNews 热帖
帮我看看 Reddit 今天有什么热门
```

## 📦 支持平台

| 平台 | 类型 | 抓取方式 |
|------|------|---------|
| 微博 | 社交 | DOM snapshot |
| 知乎 | 问答 | API 拦截 |
| Bilibili | 视频 | API 拦截 |
| Twitter/X | 社交 | GraphQL 拦截 |
| HackerNews | 科技 | JS 注入 |
| V2EX | 社区 | JS 注入 |
| Reddit | 综合 | JSON 直连 |
| 雪球 | 财经 | API 拦截 |
| 小红书 | 社交 | API 拦截 |
| BOSS直聘 | 招聘 | API 拦截 |
| YouTube | 视频 | JS 变量读取 |
| ProductHunt | 科技 | DOM |

## 🛠️ 与 opencli 的分工

| 场景 | 推荐工具 |
|------|---------|
| 常规命令操作 | opencli |
| 拦截 JSON API 数据 | **热点 skill** |
| DOM 超大页面 | **热点 skill** |
| Playwright 不可用时 | **热点 skill** |
| 发帖、点赞等写操作 | opencli / Playwright |

## 📁 文件结构

```
.
├── SKILL.md                  # skill 主体
├── references/
│   └── api-patterns.md       # 各平台 API 拦截模式详细说明
└── README.md
```

## 📄 License

MIT © [liutao773680119-cmyk](https://github.com/liutao773680119-cmyk)

---

## ☕ 打赏支持

如果这个 skill 对你有帮助，欢迎打赏支持后续维护！

<!-- 在此处放置微信/支付宝收款码 -->
<!-- ![打赏码](./assets/donate.png) -->

> 有问题欢迎提 [Issue](https://github.com/liutao773680119-cmyk/claude-skill-热点/issues)
