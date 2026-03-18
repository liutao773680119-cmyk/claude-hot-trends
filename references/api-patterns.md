# 各平台 API 拦截模式

## 微博

| 功能 | 导航 URL | 策略 | 解析路径 |
|------|---------|------|---------|
| 热搜 | `https://weibo.com/hot/search` | DOM snapshot | `link` 元素文本 + 热度数字 |
| 首页 Feed | `https://weibo.com` | list_network_requests | 找 `unread_follow` / `statuses` → `.data.statuses[].text` |
| 搜索 | `https://s.weibo.com/weibo?q=关键词` | DOM（SSR） | evaluate_script |

---

## 知乎

| 功能 | 导航 URL | API 关键词 | 解析路径 |
|------|---------|-----------|---------|
| 热榜 | `https://www.zhihu.com/hot` | `hot-lists/total` | `data[].target.title`, `.excerpt`, `.metrics.hot_value` |
| 搜索 | `https://www.zhihu.com/search?q=关键词` | `search_v3` | `data[].object.title`, `.excerpt` |
| 问题详情 | `https://www.zhihu.com/question/ID` | `answers` | `data[].content`, `.author.name` |

**注意**：知乎热榜 DOM 超大，强烈推荐拦截 API 而非 DOM 解析。

---

## Bilibili

| 功能 | 导航 URL | API 关键词 | 解析路径 |
|------|---------|-----------|---------|
| 热门排行 | `https://www.bilibili.com/v/popular/rank/all` | `/ranking/v2` | `data.list[].title`, `.stat.view`, `.stat.danmaku`, `.bvid` |
| 搜索 | `https://search.bilibili.com/all?keyword=关键词` | `search/type` | `data.result[].title`, `.play` |
| 历史记录 | `https://www.bilibili.com/account/history` | `history/cursor` | `data.list[].title` |

**链接**：`https://www.bilibili.com/video/{bvid}`

---

## Twitter / X

| 功能 | 导航 URL | API 关键词 | 解析路径 |
|------|---------|-----------|---------|
| 热搜趋势 | `https://x.com/explore/tabs/trending` | `TrendsByCategory` | `trends[].name`, `.tweetVolume` |
| 时间线 | `https://x.com/home` | `HomeTimeline` | `tweets[].full_text`, `.user.screen_name` |
| 搜索 | `https://x.com/search?q=关键词` | `SearchTimeline` | `tweets[].full_text` |

**注意**：X 使用 GraphQL，请求 URL 含 `graphql/`。

---

## HackerNews

| 功能 | 导航 URL | 策略 | evaluate_script |
|------|---------|------|----------------|
| 热门 | `https://news.ycombinator.com` | DOM（SSR） | 见下方 |

```js
Array.from(document.querySelectorAll('.athing')).slice(0, 20).map(el => ({
  rank: el.querySelector('.rank')?.textContent,
  title: el.querySelector('.titleline a')?.textContent,
  url: el.querySelector('.titleline a')?.href,
  points: el.nextElementSibling?.querySelector('.score')?.textContent
}))
```

---

## V2EX

| 功能 | 导航 URL | 策略 |
|------|---------|------|
| 热门 | `https://www.v2ex.com/?tab=hot` | DOM（SSR） |

```js
Array.from(document.querySelectorAll('.item_hot_topic_title a')).map(a => ({
  title: a.textContent.trim(), url: a.href
}))
```

---

## Reddit

| 功能 | 导航 URL | 策略 | 解析路径 |
|------|---------|------|---------|
| 热门 | `https://www.reddit.com/r/hot.json` | 直接访问 JSON | `data.children[].data.title`, `.score`, `.permalink` |
| 搜索 | `https://www.reddit.com/search.json?q=关键词` | JSON 直连 | 同上 |

**技巧**：直接导航到 `.json` 端点，无需拦截。

---

## 雪球

| 功能 | 导航 URL | API 关键词 | 解析路径 |
|------|---------|-----------|---------|
| 个股/指数 | `https://xueqiu.com/S/SH600519` | `quotec.json` | `data[].current`, `.percent`, `.volume`, `.name` |
| 自选股 | `https://xueqiu.com/portfolio` | `portfolio/stocks` | `data[].stock.symbol`, `.percent` |
| 热门股票 | `https://xueqiu.com/` | `stock_hot_rank` | `data[].code`, `.name`, `.percent` |

**注意**：雪球 DOM 极大，**强烈推荐**拦截 API。

---

## 小红书

| 功能 | 导航 URL | API 关键词 | 解析路径 |
|------|---------|-----------|---------|
| 首页 Feed | `https://www.xiaohongshu.com` | `homefeed` | `data.items[].note_card.display_title` |
| 搜索 | 搜索框 + Enter | `search/notes` | `data.items[].note_card.title`, `.liked_count` |

**注意**：小红书风控较强，优先使用 xiaohongshu MCP，本 skill 作备选。

---

## BOSS直聘

| 功能 | 导航 URL | API 关键词 | 解析路径 |
|------|---------|-----------|---------|
| 职位搜索 | `https://www.zhipin.com/web/geek/job?query=AI` | `job_list` | `zpData.jobList[].jobName`, `.brandName`, `.salaryDesc` |

---

## YouTube

| 功能 | 导航 URL | 策略 |
|------|---------|------|
| 搜索 | `https://www.youtube.com/results?search_query=关键词` | `window.ytInitialData` JS 变量 |

```js
const data = window.ytInitialData;
const items = data?.contents?.twoColumnSearchResultsRenderer
  ?.primaryContents?.sectionListRenderer?.contents?.[0]
  ?.itemSectionRenderer?.contents;
items?.filter(i => i.videoRenderer).map(i => ({
  title: i.videoRenderer.title.runs[0].text,
  views: i.videoRenderer.viewCountText?.simpleText,
  channel: i.videoRenderer.ownerText?.runs?.[0]?.text
}))
```

---

## ProductHunt

| 功能 | 导航 URL | 策略 |
|------|---------|------|
| 今日热门 | `https://www.producthunt.com` | DOM → `[data-test^="post-name-"]` |
