# Obsidian 個人知識系統建置規劃書

> ⚠️ **本文件已被取代(SUPERSEDED)**:2026-07-08 PAIOS 架構定案後,獨立 vault 的前提不再成立。
> 請改用《Obsidian整合規劃書-PAIOS-v2.md》。本文件僅保留作為決策歷程參考,**請勿交付 Codex 執行**。

> 交付對象:Codex(或任何執行代理)。本文件為**獨立、可直接執行**的建置規格書。
> 使用者背景:品牌行銷科工作者,現有 GitHub 上的每日工作推進紀錄工具(PDCA 狀態追蹤),熟悉 Git/GitHub 流程。
> 版本:v1.0(2026-07-08)

---

## 第一部分:設計決策與理由(給使用者看)

### 1.1 對「十個核心概念」分享文的評估

那篇文章的觀念正確,但它是**概念地圖,不是建置順序**。針對你的情況(行銷工作者、每天有固定的工作推進紀錄習慣、熟 GitHub),我的取捨如下:

| 概念 | 評估 | 本規劃的做法 |
|------|------|------|
| Note(原子筆記) | ✅ 第一優先 | 以「一則筆記一個想法」為原則,模板內建引導 |
| Vault | ⚠️ 文章建議多 vault,**不建議你這樣做** | **單一 vault**。Link 只能在同一 vault 內生效,初期分多個 vault 會讓知識網路斷裂 |
| Link | ✅ 第一優先 | 模板與工作流程強制建立連結,少分類、多連結 |
| Import | ⏸️ 有需要再做 | 不在初期建置範圍;未來可用官方 importer |
| Sync | ✅ 用你已有的優勢 | **Git 同步**(obsidian-git 插件 + 私有 GitHub repo),零額外費用,且你已熟悉 |
| Plugin | ⚠️ 節制 | 只裝 **4 個**社群插件,全部服務於工作流程,不為裝而裝 |
| Workflow | ✅ 核心 | 以你現有的 **PDCA 每日推進習慣**為骨幹設計(見 1.3) |
| Customization | ⏸️ 最低限度 | 預設主題即可,只調必要設定;外觀是最不值得先投資的 |
| Clipping | ✅ 行銷工作剛需 | 官方 Obsidian Web Clipper(瀏覽器擴充),收集競品/案例/文案素材 |
| Publish | ⏸️ 半年後再說 | 筆記量與品質到位前不做出口;未來可走 GitHub Pages(你已會) |

**一句話總結:文章說的生命週期 Capture → Note → Link → Vault → Workflow → Publish 是對的,但你現在只需要建好前四環,Publish 留到系統長出內容之後。**

### 1.2 關鍵決策:vault 放哪裡

- 建議建立**獨立的私有 GitHub repo**(例如 `alinahsu0712/obsidian-vault`,設為 Private),不要和 MARKERING 這個工具 repo 混在一起。工作紀錄工具是「產品」,知識庫是「資產」,生命週期不同。
- 若暫時不想開新 repo,備案是在本 repo 內建 `vault/` 子資料夾(Obsidian 可以把子資料夾開成 vault),但長期仍建議分離。
- **本規劃書以獨立 repo 為準;Codex 若無法建新 repo,則在本 repo 的 `vault/` 目錄下執行同樣規格。**

### 1.3 為你量身的核心工作流程(每日 15 分鐘)

你已經有「每日工作推進 + PDCA 狀態」的習慣,Obsidian 的角色是把這個習慣**從追蹤層升級到知識層**:

```
Capture(隨手記) ──> 00_Inbox/
       │
Daily(每日筆記)──> 10_Daily/2026-07-08.md   ← 對應你現有的每日推進紀錄
       │                (含 PDCA 欄位)
Organize(每週整理)──> 專案筆記 20_Projects/、參考資料 30_Resources/
       │
Connect(建立連結)──> [[雙向連結]] + 每則筆記至少連 1 個既有筆記
       │
Review(每週回顧)──> 10_Daily/Weekly/ 週回顧模板彙整 PDCA
```

- **每天**:打開當日 Daily Note(自動生成),用 PDCA 欄位記錄推進;任何想法丟 Inbox。
- **每週五**:跑週回顧模板,把 Inbox 清空——升級成專案/資源筆記,或刪除。
- **行銷素材**:網頁、競品、文案範例用 Web Clipper 直接剪進 `30_Resources/Clippings/`。

---

## 第二部分:建置規格(給 Codex 執行)

> 執行前提:在目標 repo 根目錄(即 vault 根目錄)操作。所有檔案以 UTF-8 編碼建立。完成後 commit 並 push。

### 2.1 建立資料夾結構

建立以下目錄樹(空目錄請放入 `.gitkeep`):

```
vault-root/
├── 00_Inbox/                  # 未分類的快速捕捉
├── 10_Daily/                  # 每日筆記(自動生成於此)
│   └── Weekly/                # 週回顧
├── 20_Projects/               # 進行中的專案(行銷活動、企劃案)
│   └── _範例專案/
├── 30_Resources/              # 參考資料、長期知識
│   ├── Clippings/             # Web Clipper 剪藏目的地
│   ├── 行銷知識/
│   └── 人物與廠商/
├── 40_Archive/                # 結案專案與過時資料
├── 90_Meta/
│   ├── Templates/             # 模板(見 2.3)
│   └── Attachments/           # 圖片與附件統一存放
└── .obsidian/                 # 設定檔(見 2.4)
```

命名規則(寫入 `90_Meta/命名規則.md`):
- 日記:`YYYY-MM-DD.md`(由 Daily Notes 自動命名)
- 專案筆記:`專案名稱-主題.md`,每個專案一個資料夾,內含 `_專案總覽.md`
- 一般筆記:直接用**能當句子讀的標題**(例:`社群貼文的最佳發文時段.md`),不加日期前綴、不加編號
- 附件:一律進 `90_Meta/Attachments/`

### 2.2 建立 `.gitignore`

```gitignore
# Obsidian 工作區狀態(每台裝置不同,不同步)
.obsidian/workspace.json
.obsidian/workspace-mobile.json
.obsidian/cache
.trash/
.DS_Store
```

### 2.3 建立模板(共 4 個,放在 `90_Meta/Templates/`)

**檔案 1:`90_Meta/Templates/每日筆記.md`**

```markdown
---
date: {{date}}
type: daily
---

# {{date}} 工作推進

## 今日焦點(最多 3 件)
- [ ]
- [ ]
- [ ]

## PDCA 推進紀錄
| 事項 | 階段 (P/D/C/A) | 進度與備註 |
|------|----------------|------------|
|      |                |            |

## 隨手記(想法、觀察、待整理)
-

## 連結
- 相關專案:[[]]
- 昨日:[[{{date-1d}}]]
```

**檔案 2:`90_Meta/Templates/週回顧.md`**

```markdown
---
week: {{date:YYYY-[W]ww}}
type: weekly
---

# 週回顧 {{date:YYYY-[W]ww}}

## 本週 PDCA 總結
- 完成(Act 收尾):
- 卡住(需 Check 的):
- 下週 Plan:

## Inbox 清理
> 打開 00_Inbox,每一則:升級成筆記 / 併入現有筆記 / 刪除。清完打勾:
- [ ] Inbox 已清空

## 本週最有價值的一則筆記
[[]]
```

**檔案 3:`90_Meta/Templates/專案筆記.md`**

```markdown
---
type: project
status: active
start: {{date}}
---

# 專案:

## 目標(一句話)

## 關鍵決策紀錄
| 日期 | 決策 | 原因 |
|------|------|------|

## 相關筆記
- [[]]

## 待辦
- [ ]
```

**檔案 4:`90_Meta/Templates/文獻剪藏.md`**

```markdown
---
type: clipping
source:
captured: {{date}}
---

# (標題)

## 為什麼存這篇(必填,一句話)

## 重點摘錄

## 我的想法 / 可以用在哪
-

## 連結
[[]]
```

### 2.4 建立 `.obsidian/` 設定檔

**`.obsidian/app.json`**
```json
{
  "attachmentFolderPath": "90_Meta/Attachments",
  "newFileLocation": "folder",
  "newFileFolderPath": "00_Inbox",
  "alwaysUpdateLinks": true,
  "useMarkdownLinks": false,
  "showUnsupportedFiles": true,
  "promptDelete": true
}
```

**`.obsidian/core-plugins.json`**(啟用核心插件;若目標 Obsidian 版本用物件格式,則將下列項目設為 true)
```json
[
  "file-explorer",
  "global-search",
  "switcher",
  "graph",
  "backlink",
  "outgoing-link",
  "tag-pane",
  "daily-notes",
  "templates",
  "note-composer",
  "command-palette",
  "outline",
  "word-count",
  "file-recovery",
  "canvas"
]
```

**`.obsidian/daily-notes.json`**
```json
{
  "folder": "10_Daily",
  "format": "YYYY-MM-DD",
  "template": "90_Meta/Templates/每日筆記",
  "autorun": true
}
```

**`.obsidian/templates.json`**
```json
{
  "folder": "90_Meta/Templates"
}
```

**`.obsidian/community-plugins.json`**
```json
[
  "obsidian-git",
  "calendar",
  "dataview",
  "templater-obsidian"
]
```

> 注意:社群插件的**本體無法由 Codex 離線安裝**(需 Obsidian 內建市場下載)。Codex 只需建立上述清單檔;使用者第一次開啟 vault 時,到 Settings → Community plugins → 開啟並逐一安裝這 4 個插件即可,清單會自動對應。

### 2.5 社群插件用途說明(寫入 `90_Meta/插件說明.md`)

| 插件 | 用途 | 為什麼是你需要的 |
|------|------|------|
| Obsidian Git | 自動 commit/push 到私有 repo | 你的同步方案,免費且你已熟悉 GitHub;建議設定每 15 分鐘自動備份 |
| Calendar | 側欄月曆,點日期開日記 | 配合你的每日推進紀錄習慣 |
| Dataview | 用查詢語法彙整筆記 | 自動列出所有 `status: active` 的專案、彙整本週 PDCA,類似你 HTML 工具裡的總覽頁 |
| Templater | 進階模板(日期運算等) | 支援模板裡的 `{{date-1d}}` 昨日連結等動態欄位 |

**明確不裝的**:主題美化類、看板類、AI 類插件——三個月後系統穩定再評估。

### 2.6 建立首頁與導覽

**檔案:`首頁.md`(vault 根目錄)**

```markdown
# 🏠 首頁

## 每日入口
- 今天的筆記:點左側月曆的今天
- [[90_Meta/命名規則|命名規則]]|[[90_Meta/插件說明|插件說明]]

## 進行中專案
```dataview
TABLE status AS 狀態, start AS 開始日
FROM "20_Projects"
WHERE type = "project" AND status = "active"
```

## 待整理(Inbox)
```dataview
LIST FROM "00_Inbox"
```
```

### 2.7 建立範例內容(讓使用者開箱即懂)

1. `00_Inbox/歡迎-先讀我.md`:說明三步驟上手(①今天先寫一則 Daily Note ②有想法丟 Inbox ③週五清 Inbox),並提醒安裝 4 個插件與 Web Clipper 瀏覽器擴充。
2. `20_Projects/_範例專案/_專案總覽.md`:用專案模板填一個假想行銷活動示範。
3. `30_Resources/行銷知識/範例-原子筆記示範.md`:示範一則原子筆記(一個想法 + 至少一個 `[[連結]]`)。

### 2.8 驗收標準(Codex 完成後自檢)

- [ ] 目錄樹與 2.1 完全一致,空目錄含 `.gitkeep`
- [ ] 4 個模板、`.gitignore`、5 個 `.obsidian/*.json`、首頁、3 個範例、命名規則、插件說明皆已建立
- [ ] 所有 `.md` 檔為 UTF-8,frontmatter 格式正確(`---` 包夾)
- [ ] 所有內部連結 `[[...]]` 指向的檔案存在,或為模板中的示意空連結
- [ ] `git status` 乾淨:已 commit(訊息:`Initialize Obsidian vault structure and templates`)並 push

---

## 第三部分:使用者的後續步驟(不由 Codex 執行)

1. **第 1 天**:在電腦安裝 Obsidian → Open folder as vault 指向 repo → 進 Community plugins 安裝清單中的 4 個插件 → 安裝瀏覽器的 Obsidian Web Clipper。
2. **第 1 週**:只做兩件事——每天寫 Daily Note、想法丟 Inbox。不要整理、不要研究插件。
3. **第 2 週起**:啟動週五回顧,開始把 Inbox 升級成有連結的筆記。
4. **第 1 個月**:設定 Obsidian Git 自動備份;手機端(可選)裝 Obsidian App + 同一 repo。
5. **第 3 個月**:回頭評估——筆記超過 100 則且持續在寫,再考慮 Publish(建議 GitHub Pages + Quartz,免費且與你現有技能相容)。

## 附:三個月成功指標

- 連續 60 天以上有 Daily Note
- Inbox 平均每週清空一次
- 至少 5 個專案筆記透過 `[[連結]]` 連到資源筆記
- Graph view 打開時不再是一堆孤島
