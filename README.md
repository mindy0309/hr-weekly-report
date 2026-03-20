# HR周报 — GitHub Pages 部署指南

## 一、首次部署（10分钟）

### 第1步：创建 GitHub 仓库

1. 打开 https://github.com/new
2. 仓库名填：`hr-weekly`（或任意名称）
3. **选择 Private**（私有仓库，防止外部访问）
4. 点击 Create repository

### 第2步：上传文件

把 `docs/` 文件夹整体上传到仓库。上传后仓库结构应该是：

```
hr-weekly/
└── docs/
    ├── index.html              ← 周报页面
    └── issues/
        ├── index.json          ← 期刊索引
        └── 2026-W12.json       ← 第一期内容（demo）
```

上传方法（选一种）：

**方法A：网页上传**
- 进入仓库页面，点击 Add file → Upload files
- 把整个 `docs` 文件夹拖进去
- 点击 Commit changes

**方法B：命令行**
```bash
cd hr-weekly-site
git init
git add .
git commit -m "第01期周报"
git branch -M main
git remote add origin https://github.com/你的用户名/hr-weekly.git
git push -u origin main
```

### 第3步：开启 GitHub Pages

1. 进入仓库 → Settings → Pages
2. Source 选择：Deploy from a branch
3. Branch 选择：main，文件夹选择：/docs
4. 点击 Save
5. 等待1-2分钟，页面顶部会出现链接：
   `https://你的用户名.github.io/hr-weekly/`

### 第4步：分享给团队

把上面的链接发给HR团队，他们收藏后每周打开就能看到最新一期。

> ⚠️ 注意：Private 仓库的 GitHub Pages 需要 GitHub Pro/Team/Enterprise 账号。
> 如果用的是免费版 GitHub，仓库需要设为 Public，但URL不会被搜索引擎收录（除非主动分享）。
> 如果对信息安全有顾虑，建议使用 GitHub Team（$4/人/月）或部署到公司内网。

---

## 二、每周更新流程

每周只需要做两件事：**新增一个 JSON 文件** + **更新索引**。

### 第1步：准备新一期内容

运行 `hr-weekly-tool`（数据采集+生成工具）后，将输出转成 JSON 格式。

JSON 文件格式参考 `docs/issues/2026-W12.json`，核心结构：

```json
{
  "id": "2026-W13",
  "weekRange": "2026.3.23 — 3.27",
  "publishDate": "2026-03-27",
  "sections": {
    "talent": [ { "title": "...", "body": "...", "source": "...", "date": "...", "hrTake": "..." } ],
    "org":    [ ... ],
    "industry": [ ... ]
  },
  "deepDive": {
    "title": "...",
    "content": "段落之间用\\n\\n分隔，**加粗**用双星号",
    "insights": ["启发1", "启发2", "启发3"]
  },
  "reads": [
    { "cat": "talent", "catColor": "#16a085", "title": "...", "source": "...", "date": "..." }
  ]
}
```

### 第2步：上传新文件

1. 把新的 JSON 文件（如 `2026-W13.json`）放入 `docs/issues/` 目录
2. 更新 `docs/issues/index.json`，在数组**开头**添加新期号：

```json
["2026-W13", "2026-W12"]
```

3. 提交到 GitHub（网页上传或 git push）
4. 等待1-2分钟，页面自动更新

### 完整操作命令（每周执行）

```bash
# 1. 生成新一期内容
cd hr-weekly-tool
python run.py

# 2. 把生成结果转为 JSON（手动或脚本）
# 3. 复制到网站目录
cp output/2026-W13.json ../hr-weekly-site/docs/issues/

# 4. 更新索引（把新期号加到最前面）
cd ../hr-weekly-site
# 手动编辑 docs/issues/index.json

# 5. 推送
git add .
git commit -m "第13期周报 2026-W13"
git push
```

---

## 三、文件说明

```
docs/
├── index.html              # 周报页面（不需要改动）
│                            #   - 自动加载 issues/index.json
│                            #   - 自动渲染最新一期
│                            #   - 支持下拉切换历史期数
└── issues/
    ├── index.json           # 期刊索引，格式：["2026-W13", "2026-W12"]
    │                        #   最新的放最前面
    ├── 2026-W12.json        # 第12周（demo期）
    └── 2026-W13.json        # 第13周（新一期）
```

每周只需要：新增1个JSON + 改1行index.json + git push。页面、样式、交互逻辑都不需要动。

---

## 四、安全提醒

- 周报内容仅基于公开信息，不含公司内部机密
- 建议使用 Private 仓库（需 GitHub 付费版）
- 如果用 Public 仓库，链接不会被搜索引擎主动收录，但理论上可被访问
- 周报页面已标注"内部资料，请勿外传"
- 不要在 JSON 中包含任何内部战略、客户名称、未发布产品信息
