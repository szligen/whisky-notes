# whisky-notes

Personal whisky tasting notes manager — **纯静态 HTML，通过 GitHub API 直接管理数据**。

## 文件

| 文件 | 用途 |
|---|---|
| `index.html` | 单文件应用（所有 HTML/CSS/JS 集成在一个文件中） |
| `whisky_notes.json` | 数据文件（通过 GitHub Contents API 读写） |

## 使用方式

### 1. 创建 GitHub 仓库

```bash
gh repo create whisky-notes --public
```

把 `index.html` 和 `whisky_notes.json` 推到仓库根目录。

### 2. 开启 GitHub Pages

仓库 Settings → Pages → Source 选 **main 分支（root）** → Save

访问地址：`https://szligen.github.io/whisky-notes/`

### 3. 配置连接

1. 打开页面后点击右上角 **⚙️ 设置**
2. 填入 **PAT**（[点此生成](https://github.com/settings/tokens/new?scopes=repo&description=whisky-notes)，只需勾 `repo` 权限）
3. 仓库名填 `szligen/whisky-notes`，分支填 `main`
4. 点「保存并测试连接」

### 4. 正常使用

之后每次打开页面：
- 自动从 GitHub 加载最新 `whisky_notes.json`
- 添加/编辑/删除记录 → **自动同步回 GitHub**（自动 commit）
- 完全不需要 git 命令

## 安全说明

| 内容 | 存储位置 | 会提交到 GitHub？ |
|---|---|---|
| PAT Token | 浏览器 localStorage | ❌ 永远不会 |
| 品酒数据 | whisky_notes.json | ✅ |
| 代码 | index.html | ✅ |

## 数据结构

每条记录：

```json
{
  "id": "unique_string",
  "date": "YYYY-MM-DD",
  "whisky_name": "Longmorn 朗摩 31年 3R 三河",
  "distillery": "Longmorn",
  "bottler": "Three Rivers Tokyo (3R)",
  "region": "Speyside",
  "abv": 54.1,
  "age": 31,
  "distillation_year": 1975,
  "bottling_year": null,
  "cask_type": "",
  "cask_number": "",
  "wb_id": 6348,
  "nose": "黄色水果，黄桃，黄色糖果汽水",
  "palate_score": 3.9,
  "body_score": 4.0,
  "finish_score": 3.7,
  "overall_score": null,
  "aftertaste": "桃，奶油，脂粉",
  "tags": ["雪莉", "高龄"],
  "notes": ""
}
```
