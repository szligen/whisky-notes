# Whisky Notes

> 一个纯静态的威士忌品饮笔记应用：**用 GitHub 仓库当数据库**，零后端、零构建、零依赖。
>
> A single-file static web app for keeping whisky tasting notes — the repository itself is the database.

**在线示例**：<https://szligen.github.io/whisky-notes/>

---

## 这是什么

- **两个文件就能跑**：`index.html`（应用）+ `whisky_notes.json`（数据）。
- **数据在你自己的仓库里**：页面通过 GitHub Contents API 直接读写那份 JSON，每次保存就是一次 commit。
- **没有服务器、没有数据库、没有 `npm install`**：部署就是 `git push`。
- **手机、电脑共用同一份数据**：打开页面自动对账合并，离线也能看和记。

适合的场景：想把品饮记录真正**攥在自己手里**，又不想为一个自用工具去维护后端的人。

---

## 功能

| 功能 | 说明 |
|---|---|
| 四种视图 | 酒标（老酒标复刻）／网格卡片（酒厂 Logo + 产区配色）／列表／风味轮 |
| 结构化记录 | 酒款、酒厂、装瓶商、ABV、酒龄、蒸馏年份、Whiskybase 编号、三项评分（入口 / 气韵 / 喉感） |
| 风味轮 | 从闻香与余韵文本里自动识别风味词，聚合成轮盘；词表可人工校准、导出 |
| 猜酒厂 | 依据你写的风味词，提示风格最接近的几家酒厂（只给线索，不编造概率） |
| 统计与筛选 | 值得回购、桶强占比、独立装瓶占比、平均酒龄；按酒厂／年份／酒龄／评分／日期筛选 |
| 图片 | 最多 5 张，浏览器端压缩（最长边 1920）后存进仓库的 `album/` |
| 暗色主题 | 亮/暗两套令牌，跟随手动切换 |
| 只读分享 | 没有 Token 的访客自动进入只读模式：能看能搜，所有写入口关闭 |
| 多设备同步 | 记录级 `updated_at` 裁决 + 删除墓碑，避免旧设备把删除的记录"复活" |

---

## 它是怎么工作的

```
浏览器（单文件应用）
   │
   ├─ 打开：读本地快照 → 拉远端 → 对账合并（不是整份覆盖）
   ├─ 修改：先写本地存储 → 12 秒防抖 → 整份 JSON PUT 回仓库
   └─ 冲突：同一条记录比 updated_at，新的赢；删除写进墓碑表 deleted{}
        │
        ▼
GitHub Contents API  ──►  whisky_notes.json（+ album/ 图片）
```

几个刻意的设计：

- **本地优先**：任何改动先落浏览器存储（localStorage，超配额自动降级 IndexedDB），网络失败不丢数据。
- **对账式加载**：启动时把本地与远端合并，而不是"服务器覆盖本地"——这是它敢离线用的原因。
- **绕过读缓存**：Contents API 有 60 秒缓存，所有读请求都强制 `no-store` 加时间戳，否则会出现"保存成功后又变回去"。
- **访客只读**：无 Token 时读同源静态 JSON，完全不触碰写接口，可以安全地把站点发给朋友。

---

## 部署你自己的副本

1. **新建仓库**（公开或私有都行，见下方「隐私」）。
2. 把下面这些放到仓库根目录：
   `index.html`、`archive.css`、`label.css`、`flavor_words.json`、`distillery-regions.json`、
   `distillery-logos/`、`album/`、`whisky_notes.json`（可以是空壳 `{"version":"1.0","notes":[]}`）。
3. **Settings → Pages** → Source 选 `main` / `/ (root)` → Save。
4. 打开站点 → 右上角 **设置** → 填 `owner/repo`、分支、以及一个 GitHub Token。
5. 点「保存并测试连接」，看到 `✓ 已连接` 即可开始记录。

> Pages 的 `index.html` 有约 10 分钟缓存。刚推送完看不到变化 = 正常，`Ctrl+Shift+R` 强刷并以页头版本号自证。

**Token 建议**：用 fine-grained token，只授予该仓库的 **Contents: Read and write**，并设置过期时间。不要用带 `repo` 全量权限的经典 token。

---

## 数据格式

`whisky_notes.json` 顶层三个键：

```json
{
  "version": "1.0",
  "notes": [ ... ],
  "deleted": { "记录id": 删除时间戳 }
}
```

`deleted` 是删除墓碑，用于防止长期未打开的旧设备把已删记录同步回来。

单条记录：

```json
{
  "id": "20250604-001",
  "date": "2026-06-04T20:30:00",
  "whisky_name": "Longmorn 朗摩 31年 3R 三河",
  "distillery": "Longmorn",
  "bottler": "Three Rivers Tokyo (3R)",
  "abv": 54.1,
  "vintage_age": 31,
  "distillation_year": 1975,
  "wb_id": 6348,
  "nose": "黄色水果，黄桃，黄色糖果汽水",
  "palate_score": 3.9,
  "body_score": 4.0,
  "finish_score": 3.7,
  "aftertaste": "桃，奶油，脂粉",
  "notes": "",
  "images": ["20250604-001_01.jpg"],
  "updated_at": "2026-06-04T12:30:00.000Z"
}
```

| 字段 | 类型 | 说明 |
|---|---|---|
| `id` | string | 唯一标识，同时是图片文件名的前缀 |
| `date` | string | 品鉴时间；新增时预填当前时间 |
| `whisky_name` | string | 酒款名称（必填，≤100 字） |
| `distillery` | string | 酒厂（≤80 字），用于产区配色与 Logo |
| `bottler` | string | 装瓶商；`OB` 表示官方装瓶 |
| `abv` | number | 酒精度 % |
| `vintage_age` | number | 酒龄（年，可为 0.5 步进） |
| `distillation_year` | number | 蒸馏年份 |
| `wb_id` | number | Whiskybase 编号 |
| `nose` / `aftertaste` | string | 闻香 / 余韵（≤500 字），风味轮与猜酒厂的唯一数据来源 |
| `notes` | string | 备注（≤1000 字） |
| `palate_score` / `body_score` / `finish_score` | number | 入口 / 气韵 / 喉感，0–5，步进 0.1 |
| `images` | string[] | `album/` 下的文件名，命名为 `<id>_<序号>.<ext>` |
| `updated_at` | string | 同步裁决用的时间戳，不参与文本搜索 |

---

## 目录结构

```
index.html                应用本体：结构 + 布局样式 + 逻辑
archive.css               视觉皮肤层，也是全站唯一的设计令牌（颜色/圆角/阴影）来源
label.css                 酒标视图样式，按需懒加载
whisky_notes.json         数据（由页面读写，不建议手改）
flavor_words.json         风味词表：类目 / 词 / 排除词 / 别名
distillery-regions.json   酒厂产区、配色、别名、SMWS 编号
distillery-logos/         酒厂 Logo（240×240 PNG）+ manifest.json
album/                    品鉴照片
favicon.ico / favicon.svg 站点图标
```

工程上的两条约定（改样式时有用）：

1. **视觉令牌只写在 `archive.css`**，`index.html` 的内联样式只负责结构与布局。新增颜色必须同时给亮色与暗色两套值。
2. **酒标视图的样式不要合并进主样式表**，它按视图懒加载，改动成本才低。

---

## 本地运行

```bash
# 在仓库根目录
python -m http.server 8899 --bind 127.0.0.1
# 打开 http://127.0.0.1:8899/
```

直接双击 `index.html`（`file://`）也能打开，但访客模式读同源 JSON 会失败，建议走本地 HTTP。

数据全部经 GitHub API，全新浏览器里没有 Token 也没有本地快照，所以首次打开是空的——这是预期行为，不是 bug。要灌测试数据，可在控制台注入：

```js
fetch('whisky_notes.json').then(r => r.json()).then(d => { db.notes = d.notes; refresh(); })
```

---

## 已知限制

- **单文件上限**：GitHub Contents API 对单文件有 1 MB 限制。按每条记录约 400 字节估算，**约 2500–2600 条**后需要换存储方案。
- **整份上传**：每次保存都会下载并重传整份 JSON。记录量大时，移动网络下的同步会变慢。
- **无字段级合并**：两台设备同时改同一条记录，只有时间戳较晚的那次会保留。
- **图片不参与合并**：两台设备同时给同一条记录传图，文件名可能撞车。

---

## 隐私

- **数据文件的可见性 = 仓库的可见性**。公开仓库意味着访问者能看到全部品饮记录；想私密就建私有仓库。
- **Token 只保存在你自己的浏览器 localStorage**，不会写进仓库、不会上传到任何第三方。
- 站点本身是纯静态的，没有任何分析脚本或第三方请求（除 `raw.githubusercontent.com` 的图片与数据）。

---

## 商标与图片来源

文中出现的酒厂名称与标识均为各自权利人的商标，此处仅用于**识别酒款**，不表示任何关联或授权。
Logo 整理自公开资料，若有权利人不希望被使用，开 issue 告知即可移除。

## 许可

本仓库未附开源许可证（默认保留所有权利）。示例代码可以阅读和参考；如需复用，请先联系作者。
