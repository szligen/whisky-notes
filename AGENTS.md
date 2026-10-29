# AGENTS.md — 多 Agent 协作协议（whisky-notes）

> **本文件在仓库内、随 GitHub 同步**，是任何 Agent、任何机器上的唯一权威版本。
> 版本：**v3 / 2026-10-29**｜取代根目录旧版（旧版已改为指针，内容不再生效）
> 适用：`[WB]` WorkBuddy/阿枢、`[CX]` Codex、`[DSH]` DeepSeek Harness，以及后续加入的 Agent

**权威顺序**：本文件 → [`CHANGELOG.md`](CHANGELOG.md) → `README.md` → 根目录指针文件（无声望，仅供工具加载）。
`C:\CodexData\whisky-notes\handoff-index.md` 已于 2026-10-29 归档，**其内容不具约束力**，仅作历史参考。

---

## 0. 三十秒速读

1. **唯一可编辑工作树 = 本目录**（`repo/`，remote `szligen/whisky-notes`）。
2. **⛔ 永远不要手改 `whisky_notes.json`**：它由页面经 GitHub API 读写，手改必冲突。
3. **动手前**在 `CHANGELOG.md`「认领表」加一行；**收工后**把状态改 `done` 并追加「已完成」行（带 sha）。
4. **认领粒度是「区」（ZONE），不是文件。** 同区串行，跨区可并行 —— 这是三个 Agent 同时改 `index.html` 还不出事的关键。
5. **提交前缀必填**：`[WB]` / `[CX]` / `[DSH]`。git log 就是彼此的变更通知。
6. **推送前先 `git fetch`**：页面的自动保存提交随时会插队。

---

## 1. 目录与权威文件

| 路径 | 作用 | 谁能改 |
|---|---|---|
| `index.html` | 单文件应用（HTML/CSS/JS 内联） | 按 ZONE 认领 |
| `archive.css` | **唯一视觉令牌源 + 皮肤层**（最后加载，覆盖内联样式） | `Z7` |
| `label.css` | 酒标视图专用样式，按需懒加载 | `Z7` |
| `whisky_notes.json` | 线上数据（`version` / `notes` / `deleted`） | ⛔ 禁止手改 |
| `flavor_words.json` | 风味词表唯一事实源 | `Z4` |
| `distillery-regions.json` | 酒厂产区 / 配色 / 别名表 | `Z3` |
| `distillery-logos/` | Logo 资产 + `manifest.json` | `Z3` |
| `album/` | 品鉴图片（由页面上传，文件名 `<noteId>_<序号>.<ext>`） | 页面自动 |
| `AGENTS.md` / `CHANGELOG.md` | 本协议 / 协作台账 | `Z8` |
| `test_*.js` `shot_*.js` `repro_*.js` | 本机测试与截图脚本（被 `.gitignore` 排除，**不入库**） | 本机 |

> ⚠️ 已知缺口：回归测试脚本不入库 → 换一台机器/换一个 Agent 就没有回归网。见 §5.9。

---

## 2. Agent 注册表

**新增 Agent 必须先在表里登记**（改这张表属于 `Z8`），否则其提交无法被追溯。

| 代号 | 全称 | 运行环境 | 能力边界 | 提交前缀 |
|---|---|---|---|---|
| `WB` | WorkBuddy / 阿枢 | 本机 Windows 工作目录 | 页面交互、样式微调、数据录入 | `[WB]` |
| `CX` | Codex | Windows 沙箱 + bash，有图像处理工具链 | 代码重构、SVG/位图资产、脚本 | `[CX]` |
| `DSH` | DeepSeek Harness | Windows，可跑 Playwright（Chromium） | 全栈改动、回归测试、文档治理 | `[DSH]` |

命名规则：**3 个大写字母**，取自工具名首字母；首次出现必须在上表登记，并在提交说明里用一句全称解释（例如「`[DSH]` DeepSeek Harness」）。

---

## 3. 认领协议 v3

### 3.1 认领粒度 = ZONE

不要写「我要改 index.html」——那是三个 Agent 撞车的根源。写「我要改 Z3」。

| ZONE | 范围 | 代表符号（改这些就是在动本区） |
|---|---|---|
| `Z1` 同步引擎 | 服务端读写、合并、本地持久化 | `ghRead` `fetchServerSnapshot` `putServerSnapshot` `runSync` `scheduleSync` `reconcileOnLoad` `mergeLocalWithServer` `mergeTombstones` `Store` `persistLocal` `loadLocal` `syncMeta` `dbRev` |
| `Z2` 数据与统计 | 筛选、排序、聚合、ID | `filters` `applyFiltersAndSort` `avgScore` `renderStats` `distSortCmp` `genId` |
| `Z3` 视图与卡片 | 四种视图渲染与分页 | `setView` `renderCards` `cardHTML` `labelCardHTML` `label*` `fitLabelTitles` `renderPagination` `loadLabelCss` `distilleryMeta` `distilleryLogo` |
| `Z4` 风味轮 | 词表、归类、轮盘 | `FV_*` `fvApply` `computeFlavor` `fvSeg` `renderWheelView` `fvClassify` `fvPushWords` |
| `Z5` 表单·图片·弹窗 | 新增/编辑/删除/上传/详情 | `openAdd` `openEdit` `populateForm` `saveNote` `confirmDelete` `compressImage` `uploadImageToGitHub` `nextImageSeq` `renderDetailContent` `openLightbox` |
| `Z6` 猜酒厂 | 提示算法与面板 | `GS_*` `gs*` |
| `Z7` 视觉与令牌 | 样式结构、皮肤、令牌 | `index.html` 的 `<style>` 结构层、`archive.css`、`label.css` |
| `Z8` 文档与发布 | 协议、台账、版本号、部署 | `AGENTS.md` `CHANGELOG.md` `README.md` `.gitignore`、`.version-badge` |

**共享区（不属于任何单一 ZONE，改之前必须标 `[共享]`）**：
`esc` `escQ` `toast` `setStatus` `refresh` `roGuard` `hasConfig` `isIB` `fmtDate` `getImageUrl` `repoRef` `updateSyncBadge`
—— 这些被所有区调用，改动等于全区受影响，必须单独声明并跑全量回归。

### 3.2 认领表格式

写在 `CHANGELOG.md` 第一节，Markdown 表格：

```
| 认领时间 | Agent | ZONE | 具体目标 | 状态 | TTL |
|---|---|---|---|---|---|
| 2026-10-29 14:17 | `[DSH]` | Z1 + Z7 | 图片序号修复 / 令牌收敛 | claimed | 2026-10-30 02:17 |
```

- 状态只有四种：`claimed`（在改）、`blocked`（被外部条件卡住，写明卡在哪）、`done`、`abandoned`。
- **TTL = 12 小时**，写绝对时间，不写「约」。
- **一个 Agent 同时最多 `claimed` 两个 ZONE**，且必须在同一行写清。

### 3.3 规则

1. **开工前 5 分钟内**：`git fetch` → 看认领表 → 看 `git log --oneline -10`。
2. **先声明，后动手**。改完才补声明 = 违规，发生一次就在台账「接管记录」里记一笔。
3. **同 ZONE 同一时间只能有一个 `claimed`。** 看到别人 `claimed` 且未过期：换区做别的事；如果非做不可，在认领表加备注 `⏳ 等 [CX] Z3` 并在群里说一声。
4. **过期接管**：TTL 到点视为 `abandoned`。接管者必须新增一行写 `接管自 [CX] 2026-10-29 14:17`，并把旧行状态改成 `abandoned`；同时在「接管记录」留一行说明原委。
5. **跨区并行是常态**：Z1 与 Z7 同时改没问题（不同函数、不同文件行）；Z3 与 Z5 同时改要小心 `cardHTML` 与 `openDetail` 的字段耦合。
6. **改完立刻改自己的行**为 `done`，并追加「已完成」条目。忘了改 = 下一个 Agent 会白等 12 小时。

### 3.4 撞车处理

| 场景 | 处理 |
|---|---|
| 目标区别人 `claimed` 未过期 | 换区；或只读地准备方案，等对方 `done` 后再改 |
| 发现工作树有未提交的他人改动 | `git diff` 判断，**不要** `git checkout --` 丢弃 |
| 同一区两人都改了 | 后提交者先 `git pull --rebase` **禁止**——用 `fetch` + `reset --mixed origin/main` 后重放自己的改动 |
| 共享区改动撞车 | 串行：一方提交后，另一方 `fetch` 再改；不许并行 |
| 不确定归属 | 不动，在台账里提问 |

---

## 4. 提交与推送

```bash
git add <具体文件>            # ⛔ 不要 git add -A
git commit -m "[DSH] Z1 图片序号改为最大序号+1，修复覆盖旧图"
```

- **前缀必填**，且与注册表一致；一句话说清「改了什么 + 为什么」。
- **ZONE 写进提交信息**，方便按区检索：`git log --grep="Z1"`。
- **一次提交只做一件事**；跨区改动拆成多个提交，或在信息里列全 `Z1+Z7`。
- **⛔ 不提交 `whisky_notes.json`**。提交前 `git status --short` 自查。
- **推送**（便携 Git 的 `helper-selector` 在无头环境会挂死，必须带参数）：
  ```bash
  git -c credential.helper= -c credential.helper=manager -c http.version=HTTP/1.1 push origin main
  ```
- **数据提交随时插队**（页面自动 PUT）→ 推送前固定流程：
  ```bash
  git fetch origin
  git rev-list --left-right --count HEAD...origin/main   # 必须 0 0 再推
  git reset --mixed origin/main && git checkout -- whisky_notes.json   # 落后时对齐
  ```
- **⛔ 禁止 `rebase` / `gc` / `repack`**（曾把 `.git` 弄坏）；对齐远端只用 `fetch` + `reset --mixed`。
- **版本号**：改 `index.html` 必须同时更新**两处**并保持一致——顶部 `<!-- VERSION: YYYY-MM-DD-HH:MM -->` 与页头 `.version-badge` 的 `vYYYY.MM.DD-HH:MM`。只改一处视为未完成。

---

## 5. 铁律

### 5.1 数据与同步（动之前必读）

- `dbRev` **只在 `persistLocal()` 里 +1**；任何新数据入口都必须走 `persistLocal()`。
- `runSync()` 必须 `const revAtStart = dbRev`；**只有 `dbRev === revAtStart`** 才允许 `db.notes = notesToPush` 并清 dirty / `pendingDeletes`。
- 收尾必须 `if (pendingAgain || syncMeta.dirty) scheduleSync()`。
- 读 GitHub contents **一律走 `ghRead()`**（`cache:'no-store'` + `_ts`）。**不要新写裸 `fetch()` 的 GET**：contents 接口 60 秒缓存会让「保存后回读」拿到旧内容。
- 合并裁决：同 id 比 `recTs()`，新的赢；平局 `tieWinner` —— **推送 `'local'`，采纳 `'server'`**。
- 删除墓碑 `db.deleted = {id: 删除时间}` 随文件同步；记录时间戳 ≤ 墓碑 → 判死；墓碑保留 90 天。
- 第二道防线 `localNewer`：`mergeLocalWithServer` 返回 `{notes, deleted, localNewer}`，`localNewer > 0` 时**拒绝反向采纳**该快照。
- `adoptServerSnapshot()` / `loadFromGitHub()` 已删除，**不要复活**。
- `updated_at` 不参与文本搜索（`JSON.stringify` replacer 剔除）。

### 5.2 图片（2026-10-29 新增规则）

- 文件名 = `<noteId>_<两位序号>.<ext>`，同一文件重复写入会**静默覆盖**。
- **序号必须用 `nextImageSeq(noteId, existingImages)` 取「已有最大序号 +1」**。
  ⛔ 禁止用「本次待传文件下标」当序号 —— 旧实现就是这样，给已有 `_01` 的记录再加图会把原图冲掉。
- 上传后合并图片列表要去重；`images` 变更必须补盖 `updated_at`。

### 5.3 文档里不许写会过期的死数据

**这是旧版协议反复失效的根因**：它把 HEAD、记录数、版本号写进正文，一天后就全错。

- ❌ 不写：`HEAD = 0297d04`、`共 916 条`、`当前版本 v2026.10.13`
- ✅ 写：`git log -1`、页面页头 `.version-badge` 与 `#header-count`、或一条取值命令
- 本协议只允许出现**稳定事实**（仓库地址、分区、约束、命令）与**带日期的实测值**（如「2026-10 实测 ≈400 字节/条」）。

### 5.4 只读访客

无 Token = 访客，读同源 `./whisky_notes.json`，写入口全部停用（`roGuard` + `applyReadOnlyUI`）。
**改任何写入口相关代码，必须跑 `test_readonly_guest.js`。**

### 5.5 风味词表

- 唯一事实源 `flavor_words.json`；数量、类目名**以文件为准**，本文不写死。
- 加载 `fvCacheLoad()` → `fvFetchWords()`；写回 `fvCacheSave()` → `fvPushWords()`。
- 类目配色**不写死**：`fvBuildColors()` 现算，取色走 `fvColor()`（灰兜底）。
- 改类目名要同时改 4 个字段（`categories` / `words` 值 / `alias` 值 / `touched` 值）+ 刷新 `updated` + 同步 `FV_LEGACY_COLORS` 的键。
- JSON 必须 1 空格缩进：`json.dump(..., ensure_ascii=False, indent=1)`。文件 404 = 正常空状态。

### 5.6 界面与视觉令牌（v3 重写）

- **`archive.css` 是唯一令牌来源**（颜色 / 圆角 / 阴影 / 字体栈），最后加载、覆盖内联。
  `index.html` 的 `<style>` **只写结构与布局，不得再定义 `--bg` / `--accent` 等颜色令牌**。
- 新增令牌必须在 `archive.css` 的 `:root` 与 `[data-theme="dark"]` **两处都给值**，缺暗色值 = 暗色下退回继承色。
- **不许硬编码颜色**写组件；页头/统计/Logo 底这类特例用语义令牌（`--header-bg` `--stat-accent` `--logo-bg`）。
- 控件高度以现有实现为准：页头按钮 **34px**、筛选行 **32px**、日期输入 **30px**、移动端主按钮 **38px**；同一行内必须等高，**不要靠 padding 撑高**。
- 圆角一律 `var(--radius)` / `var(--radius-sm)`，不要写死 px（换皮肤时会裂）。
- 工具栏底色用 `var(--bg)`，**不要用 `var(--surface)`**（会浮起成白斑）——唯一例外由皮肤层统一配置。
- 酒标视图样式在 `label.css`（作用域 `#cards-grid.label-view`，懒加载）；**新卡片样式做独立视图 + 独立 CSS，不要动 `label.css`**。
- 项目**没有**通用 `.hidden`（只有 `.modal-overlay.hidden`），已补 `.hidden{display:none!important}`，不要重复定义。
- 分页/筛选等重建 `innerHTML` 的控件用**容器事件委托**；记录名含 `\xa0`，前缀匹配前先归一化。
- `data-page-node-id` 是外部可视化编辑器的残留属性，**不要模仿、不要批量清理中夹带业务改动**。

### 5.7 路径规范（跨程序传参）

给 node / PowerShell / cmd / curl.exe 等**原生程序**传路径，一律 `C:/Users/...` 或 `C:\\...`。
严禁把 `/c/...`、`/tmp/...` 写进原生程序参数（会在当前盘符根下造出 `C:\c`、`C:\tmp`）。bash 内部之间用 POSIX 路径没问题——问题只出在跨过边界的那一刻。

### 5.8 容量与性能边界（2026-10 实测）

- 每条记录约 **400 字节**；`whisky_notes.json` 走 GitHub Contents API，**单文件上限 1MB** → 约 **2600 条**触顶，届时 PUT 会返回 422 并表现为「同步失败」，不会提示体积原因。
- 每次同步 = 下载整份 JSON（约 400KB）+ 上传整份 base64（约 500KB）；移动网络下不要缩短 12 秒防抖。
- 超过约 2600 条必须换存储（独立数据仓库 / Cloudflare D1 / 分片），这不是优化项而是阻断项。

### 5.9 测试

本地起服务（在 `repo/` 下）：`python -m http.server 8899 --bind 127.0.0.1`，用 Playwright（Chromium）打开。
页面数据全靠 GitHub API，**必须手动注入**：

1. `fetch('whisky_notes.json')` → 赋给 `db.notes` → `refresh()`
2. 词表同理注入，否则 `FV_READY` 恒为 false：`fetch('flavor_words.json')` → `fvApply(d)` → `fvInit(true)`
3. 改同步逻辑还要 `page.route('**/api.github.com/**')` mock 掉 API（PUT 可故意延迟复现时序 bug）

`page.evaluate` 里顶层 `let db` 不挂 `window`，但词法全局可直接用 `db`；`function` 声明才挂 `window`。

**必跑回归**（脚本在本机 `repo/` 下，未入库）：
`test_smoke_real.js` · `test_readonly_guest.js` · `test_sync_regress.js` · `test_multidevice.js`
注意默认视图是 `label`，酒标卡无编辑按钮，计数前要先 `setView('grid')`。

> ⚠️ 缺口：这些脚本被 `.gitignore` 排除，**第三个 Agent 在另一台机器上跑不了**。建议后续入库到 `tests/` 并接 CI（属于 `Z8`，需单独认领）。

### 5.10 部署与缓存

GitHub Pages 的 `index.html` 是 `max-age=600`：推送后最多 10 分钟用户仍拿旧代码。
每次交付都要提醒用户 **Ctrl+Shift+R**，并以页头 `.version-badge` 自证版本。

---

## 6. 当前状态（只写稳定事实）

| 项 | 值 |
|---|---|
| 仓库 / 分支 | `szligen/whisky-notes` / `main`（Pages 部署） |
| 默认视图 | `label`（酒标），`setView()` 切换 grid / list / wheel / label |
| 存储 | GitHub Contents API 单文件；本地 localStorage + IndexedDB 兜底 |
| 数据量 / 版本号 | 以 `#header-count` 与 `.version-badge` 为准（**不在此写死**） |
| 未完成 | ①回归测试未入库、无 CI；②`whisky-notes-cloudflare/` 是半成品（`src/` 为空），未并入本仓库；③无字段级合并 |

---

## 7. 已知未覆盖边界

- 两台设备在时钟偏移范围内同时改同一条 → 晚的赢，早的被覆盖（无字段级合并）。
- 两台设备同时给同一记录传图 → 序号可能撞车（`nextImageSeq` 只能看到本机已有的文件名）。
- 墓碑判定依赖设备时钟；时钟严重漂移时会误杀删后重建的记录。
- 页面 10 分钟静态缓存期间，两个 Agent 可能在不同代码版本上测试。

---

## 8. 交接检查单（新 Agent 上手机 5 步）

1. `cd repo && git fetch && git log --oneline -10` —— 看最近谁动了哪块。
2. 读本文 §3 的 ZONE 表，挑一个**没人 claimed** 的区。
3. 在 `CHANGELOG.md` 认领表加一行（含绝对 TTL 时间）。
4. 动手；本地起 8899 服务 + Playwright 自测；改动样式必须对比亮/暗两套主题。
5. `git add <文件>` → 带前缀提交 → 推送 → 认领改 `done` → 追加「已完成」（带 sha）。
