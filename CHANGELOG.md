# CHANGELOG — 多 Agent 协作台账（whisky-notes）

> **本文件随仓库同步，是所有 Agent 之间唯一的协调通道。** 规则见 [`AGENTS.md`](AGENTS.md)。
> 历史：2026-10-29 从根目录 `C:\CodexData\whisky-notes\CHANGELOG.md` 迁入仓库，此前条目原样保留在文末。
> 前缀：`[WB]` WorkBuddy/阿枢 · `[CX]` Codex · `[DSH]` DeepSeek Harness

---

## 一、认领表（进行中）

> 格式：`| 认领时间 | Agent | ZONE | 具体目标 | 状态 | TTL |`
> 状态只允许：`claimed` / `blocked` / `done` / `abandoned`。
> **TTL = 12 小时**：超时视为 abandoned，他人可接管，但必须新增一行写明「接管自 …」。

| 认领时间 | Agent | ZONE | 具体目标 | 状态 | TTL |
|---|---|---|---|---|---|
| 2026-10-29 14:17 | `[DSH]` | Z8 + Z7 + Z1 | P0 修复：①图片序号覆盖（Z1）②三 Agent 协作协议迁入仓库（Z8）③视觉令牌单一来源（Z7） | claimed | 2026-10-30 02:17 |

---

## 二、已完成

> 每条必须带 commit sha 与 ZONE，方便对方 `git show` 复核。

### 2026-10-29

- `[DSH]` `Z8` — 协作协议迁入仓库：新增 `repo/AGENTS.md`（v3，含 Agent 注册表 / ZONE 分区 / 认领 TTL / 共享区）与 `repo/CHANGELOG.md`（本文件）；根目录旧版改为指针，避免"协议只在本地、第三台机器读不到"。
- `[DSH]` `Z7` — 视觉令牌单一来源：全部颜色/圆角/阴影令牌移入 `archive.css`，`index.html` 内联样式不再定义令牌，硬编码色（页头底、统计强调色、Logo 底）改为令牌；删除死代码 `archiveCardHTMLPrototype`。
- `[DSH]` `Z1` — 图片文件名序号改为「已有文件最大序号 +1」（新增 `nextImageSeq()`），修复"给已有图片的记录再加图会覆盖原图"的丢数据缺陷。

### 2026-10-13 ~ 2026-10-21（迁移自根目录旧 CHANGELOG）

- `[CX]` `3006180` — 将 Ardnamurchan 环形字标矢量化并渲染 240×240 PNG；新增中文名「艾德麦康」映射；版本 `v2026.10.21-14:57`
- `[CX]` `114f091` — Convalmore 老字标轮廓追踪为 SVG 路径并渲染；新增「康法摩尔」映射；版本 `v2026.10.21-14:54`
- `[CX]` `d576fbe` — IMPERIAL 字形轮廓追踪重建 SVG 并渲染；新增「帝国」映射；版本 `v2026.10.21-10:03`
- `[CX]` `8e04934` — Dallas Dhu 厂房图形与字标制作 240×240 PNG；新增「达拉斯杜」映射；版本 `v2026.10.21-09:42`
- `[CX]` `d06920f` — SPEY 字标按规范名 Speyside 制作；新增「诗贝」映射；版本 `v2026.10.21-09:37`
- `[CX]` `3df94d8` — Allt-a-Bhainne 尖角字标高倍超采样；新增「欧特班」映射；版本 `v2026.10.21-09:32`
- `[CX]` `1df1e02` — Glentauchers 低像素字标改 8 倍超采样 + Lanczos 抗锯齿；版本 `v2026.10.21-09:29`
- `[CX]` `8ec776b` — 替换 Glentauchers 字标并统一 240×240；新增「格兰陶切丝」映射；版本 `v2026.10.21-09:24`
- `[CX]` `c694281` — Rosebank Logo 紧裁居中并加入资产清单；新增「罗斯班克」映射；版本 `v2026.10.21-09:20`
- `[CX]` `900c790` — Glen Keith Logo 紧裁居中，统一 240×240；删除旧 JPG；版本 `v2026.10.21-09:12`
- `[CX]` `57bd440` — Lindores / Penderyn / Shindo / Glen Mhor 四张 Logo 统一规格；清理反色逻辑；版本 `v2026.10.20-17:50`
- `[CX]` `53af9e4` — Glen Mhor 老酒标提取建筑线稿与字样，制作透明居中 Logo；版本 `v2026.10.20-17:35`
- `[CX]` `ae5617a` — 清除 Shindo 蜂窝背景并重制透明底 Logo；分类保持 `OTHER · JAPAN`
- `[CX]` `318b1ab` / `3d29670` — Lindores 反色移入 img 元素；补 Penderyn 官方白色 SVG；Penderyn 归 `other`；版本 `v2026.10.20-17:09`
- `[CX]` `422b10d` — 添加 Lindores Abbey 官方 SVG 与资产清单；用 CSS 滤镜适配浅色 Logo 框
- `[CX]` `1bc4e8e` — 纠正视图职责：恢复酒标视图，新设计（真实 Logo / 产区色 / 酒龄 / 三项评分）迁到普通卡片视图
- `[CX]` `27c2359` — 落地「暖纸档案」整页换肤与产区配色卡片；上传 119 个去框 Logo
- `[CX]` `fea8d99` — 新增 `distillery-regions.json`，覆盖 184 个酒厂字段值（产区/国家/类型/别名/SMWS 编号/配色）
- `[WB]` `0297d04` — 酒标样式设为默认视图（`viewMode='label'`，懒加载 `label.css`）；HEAD 阶段 916 条记录
- `[WB]` `7105364` — 酒标卡去掉 `INDEPENDENT BOTTLER` 标记行，IB 顶部只留装瓶商名
- `[WB]` `92703df` — 新增酒标视图 H1 老酒标复刻卡，样式拆独立 `label.css`
- `[WB]` `2026-10-13 16:10` — 建立交接包：拷贝 `repo/ outputs/ shots/`，`repo/` 装 git（remote `szligen/whisky-notes`），17 个受控文件
- `[WB]` `2026-10-13 16:27` — 建立 `AGENTS.md` 双 Agent 协同协议与共享 `CHANGELOG.md`
- `[WB]` `2026-10-13 16:45` — 旧工作目录改名 `whisky-notes.archive`，作为本地备份快照保留
- `[WB]` `2026-10-13 16:50` — `AGENTS.md` 增补「属主与 safe.directory」环境差异说明

---

## 三、接管记录

> 接管过期认领时在此登记，写明原 Agent、原时间、接管理由。

（暂无）
