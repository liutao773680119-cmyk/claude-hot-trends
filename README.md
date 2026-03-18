# 这是一款用Claude code用一句话获取各大平台热点和新闻的skill，支持小红书/微博/知乎/B站/X等16+平台

> 使用 Chrome DevTools MCP 直接拦截页面 API 响应，一句话获取 16+ 平台热点数据。

## ✨ 功能特点

- **无需 Playwright 扩展**，只需 Chrome 以调试模式运行
- **直接拦截 XHR/fetch JSON**，数据比 DOM 解析更干净更快
- **支持 16+ 平台**：微博、知乎、B站、Twitter/X、HackerNews、V2EX、Reddit、雪球、小红书、BOSS直聘、YouTube、ProductHunt 等
- **自然语言触发**：说"查微博热搜"即可，无需记命令

## 🚀 快速安装

```bash
npx skills add liutao773680119-cmyk/claude-hot-trends
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

## 🎬 真实对话示例

### 示例1：查 Twitter/X 今日热点前10并翻译

**用户：** 使用热点技能查X上今日热点前10，并翻译

**Claude：**

| # | 话题 | 中文译名 | 分类 | 关联话题 |
|---|------|----------|------|----------|
| 1 | Venezuela | 委内瑞拉 | 体育 | 与 Aaron Judge、Bryce Harper 同时热议，MLB 开幕季相关 |
| 2 | Whitlock | 惠特洛克 | 体育 | 与 Red Sox（红袜队）关联 |
| 3 | NC State | 北卡罗来纳州立大学 | 体育 | 与 #MarcusIsLucky、Charles Barkley 同热，NCAA 相关 |
| 4 | World Series | 世界大赛 | 体育 | MLB 总冠军赛话题 |
| 5 | Yankees | 纽约洋基队 | 体育 | MLB 经典豪门球队持续热议 |
| ... | ... | ... | ... | ... |

> 附带 Today's News 板块：OpenAI 推出 GPT-5.4 Mini/Nano（5,716 帖）

---

### 示例2：查微博热搜前10

**用户：** 用热点技能搜今天微博的热点前10

**Claude：**

| # | 话题 | 热度 | 简评 |
|---|------|------|------|
| 1 | **男二以下 AI演员** | 110万 | AI进军影视圈，男二以下角色将被替代，引发行业热议 |
| 2 | **50岁女职工被强制退休获赔94万** | 80万 | 劳动权益话题，女性退休年龄歧视 |
| 3 | **春暖花开各地文旅持续升温** | 63万 | 春季旅游复苏 |
| 4 | **婴儿倒挂近1分钟爸爸全程玩手机** | 40万 | 儿童安全事件 |
| 5 | **周杰伦新专辑太阳之子** | 33万 | 新专辑消息引爆粉丝圈 |
| ... | ... | ... | ... |

---

### 示例3：深度分析热点新闻

**用户：** 分析下X上 OpenAI 推出 GPT-5.4 Mini 和 Nano 轻量模型这个新闻

**Claude 分析输出（节选）：**

> **@Sxsyer（科技趋势）观点：** 今天最关键的不是"更便宜"，而是"**可并行**"。GPT-5.4 mini/nano 把子 Agent 成本打到可商用区间，传统行业第一次能把"1人做10人活"跑成日常流程。

| 维度 | 结论 |
|------|------|
| 战略意图 | 构建「大脑规划 + mini 并行执行」的 Agent 架构 |
| 竞争意义 | 直接对标 Claude Haiku、Gemini Flash，价格大幅下探 |
| 开发者影响 | 同等预算可跑 3x+ 任务，很多应用的基座模型会换掉 |

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

<img src="./assets/donate.png" width="300" alt="支付宝收款码">

> 有问题欢迎提 [Issue](https://github.com/liutao773680119-cmyk/claude-skill-热点/issues)
