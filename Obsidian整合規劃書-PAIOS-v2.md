# Obsidian × PAIOS 整合規劃書 v2.0

> 交付對象:Codex(Builder)。審查對象:ChatGPT(Architecture Review Board)。
> 本文件**取代** v1.0(《Obsidian建置規劃書.md》)。v1.0 假設從零建立獨立 vault;在 PAIOS 架構已定案的前提下,該假設不再成立。
> 版本:v2.0(2026-07-08)
> **本文件不是架構文件。** 它不新增架構層、不新增治理規則、不建立 Agent。若本文件任何內容與 Workspace 正式文件衝突,**一律以 Workspace 正式文件為準**,Codex 應記錄偏差而非修改架構。

---

## 第一部分:定位決策(給 Owner 與 Review Board)

### 1.1 Obsidian 在 PAIOS 中是什麼

**Obsidian = Owner 對 PAIOS Workspace 的人機介面(Human Interface Layer)。**

- **Vault 就是 PAIOS Workspace repo 本身**(Open folder as vault)。不另建 vault、不另建 repo、不複製任何文件。
- Obsidian 只是「閱讀與書寫這個 Git 工作區的一種方式」。Codex 用 CLI 讀寫同一批 .md,Owner 用 Obsidian 讀寫同一批 .md。移除 Obsidian,PAIOS 完好無損。
- MARKERING repo(每日推進 HTML 工具)維持獨立的產品 repo,不納入 vault。

### 1.2 Obsidian 明確**不是**什麼

| 不是 | 理由(對應 What Must NOT Happen) |
|------|------|
| 不是第二個知識治理系統 | 不得建立平行 governance |
| 不是 Agent 的產生地 | Agent 只能經 Agent Factory 產生;vault 內的模板不得包含「建立 Agent 規格」的模板 |
| 不是 Source of Truth 的複本 | 正式文件只有一份;筆記**連結**正式文件,永不複製其內容(One Rule, One Home) |
| 不是自動化平台 | Governance Before Automation:除 Git 備份外,不安裝任何會自動寫入內容的插件 |

### 1.3 與 PAIOS 八原則的逐條對照

| PAIOS 原則 | Obsidian 端的落實方式 |
|------|------|
| Model Agnostic | 純 .md + YAML frontmatter;**改用標準 Markdown 連結**(v1.0 用 wikilink,v2.0 修正)——任何模型、任何工具都能解析,無 Obsidian 專屬語法鎖定 |
| Text First | 全部內容為純文字;附件僅限必要圖片,集中一個資料夾 |
| Git First | obsidian-git 插件同步;`.obsidian/` 中的裝置狀態檔 gitignore,設定檔進版控 |
| Folder Convention First | **Codex 執行前必須先讀 Workspace 的資料夾規範文件**,Obsidian 相關內容全部放入規範允許的位置;本文件的資料夾名稱僅為預設值 |
| Governance Before Automation | 插件白名單制(僅 4 個,見 2.5),Dataview 僅做唯讀彙整,不做任何自動改寫 |
| One Rule, One Home | 筆記引用規則時只放連結;模板內建提醒「Link, don't fork」 |
| Evolution Through Evidence | **這是 Obsidian 的核心價值**:Owner 的每日/每週筆記就是 Production Evidence Log(見 1.4) |
| Architecture Before Agents | 本建置是工具設定,零 Agent、零自動化決策 |

### 1.4 為什麼現在做這件事是「順著架構」而非「新需求」

Fable 的最終判斷指出:多項風險(North Star、Approval Fatigue、Governance enforcement)都應**等待 Production Evidence 再處理**。但 Evidence 需要有地方被持續記錄。

Obsidian 的角色就是 **Evidence 的採集介面**:

- Owner 每日筆記記錄:今天 PAIOS/Codex 實際怎麼被使用、哪裡卡住、批准了幾次、哪次批准覺得多餘(→ 直接餵養 Approval Fatigue 的未來評估)。
- 每週回顧彙整證據,累積到足夠份量時,才觸發正式的 Architecture Review。
- 這讓「Evolution Through Evidence」從口號變成有固定入口的流程,而**不需要新增任何治理層**。

同時,Owner 的品牌行銷本業筆記(每日 PDCA 推進、行銷素材剪藏)也放在 Workspace 的 Owner 區,因為長期目標是 Marketing Agent Operating System——未來 SEO/Compliance/Creative 等 Agent 經 Factory 產生後,讀取的領域知識正是這批筆記。**現在累積的行銷筆記,就是未來 Agent 的語料資產。**

### 1.5 執行時機(不擴張 Stage 0)

- 本建置**不修改 Roadmap、不插隊**。若 Stage 0 的範圍本來就含 Workspace 骨架建置,則本文件作為其中的一個子任務隨行;若否,排在 Stage 0 完成後作為獨立小任務。
- 由 Codex 依 Roadmap 判斷排程;本文件自包含,隨時可執行。
- 交付 Codex 前,建議先由 ChatGPT(Review Board)用第四部分的合規清單審一次——這符合既定的 Review Process。

---

## 第二部分:建置規格(給 Codex 執行)

### 2.0 執行前置(必做,不可跳過)

1. 讀取 Workspace 正式文件中的**資料夾規範(Folder Convention)**與 Governance 文件。
2. 將本規格的預設路徑映射到既有規範。**規範優先於本文件。**
3. 在 Owner 區建立一份《Obsidian整合對照表.md》,記錄:本文件預設路徑 → 實際採用路徑,以及任何因規範而做的偏差。
4. 若發現本文件任何要求與 Governance 衝突:**停止該項,記錄於對照表,不得自行修改架構或 Governance。**

### 2.1 Owner 區資料夾(預設值,以 Folder Convention 映射為準)

在 Workspace 規範允許的 Owner/人工區域下建立:

```
owner/                        # 名稱依 Folder Convention 調整
├── inbox/                    # 快速捕捉,未分類
├── journal/                  # 每日筆記(PDCA + PAIOS 使用證據)
│   └── weekly/               # 週回顧(證據彙整)
├── decisions/                # Owner 決策紀錄(僅 Owner 層級的決策;架構決策仍在正式文件)
├── marketing/                # 品牌行銷本業知識
│   ├── clippings/            # Web Clipper 剪藏目的地
│   └── projects/             # 行銷專案筆記
├── evidence/                 # 累積成形的 Production Evidence(從 journal 升級而來)
└── _meta/
    ├── templates/            # 模板(見 2.3)
    ├── attachments/          # 附件統一存放
    └── Obsidian整合對照表.md
```

原則:**Obsidian 只在 Owner 區新增內容;不在架構文件區、Agent Factory 區、Runtime 區新增任何檔案**(唯一例外:`.obsidian/` 設定與 `.gitignore` 追加,見 2.2、2.4)。

### 2.2 `.gitignore` 追加(不覆蓋既有內容,只追加)

```gitignore
# Obsidian per-device state (config files ARE committed)
.obsidian/workspace.json
.obsidian/workspace-mobile.json
.obsidian/cache
.trash/
.DS_Store
```

### 2.3 模板(4 個,放 `owner/_meta/templates/`)

所有模板共同規則,寫在每個模板開頭的註解:`<!-- Link, don't fork:引用正式文件時只放連結,不複製內容 -->`

**模板 1:`每日筆記.md`**

```markdown
---
date: {{date}}
type: daily
---
<!-- Link, don't fork -->

# {{date}}

## 今日焦點(最多 3 件)
- [ ]

## 本業 PDCA 推進
| 事項 | 階段 (P/D/C/A) | 進度與備註 |
|------|----------------|------------|

## PAIOS 使用證據(Evolution Through Evidence)
- 今天用了哪個流程/Agent:
- 卡住或覺得多餘的地方(含批准疲勞):
- 值得記錄的產出:

## 隨手記
-
```

**模板 2:`週回顧.md`**

```markdown
---
week: {{date:YYYY-[W]ww}}
type: weekly
---

# 週回顧 {{date:YYYY-[W]ww}}

## 本業 PDCA 總結
- 完成:
- 卡住:
- 下週 Plan:

## PAIOS 證據彙整
> 從本週 journal 摘出可能影響架構的證據;累積足夠時升級為 evidence/ 下的正式證據筆記。
-

## Inbox 清理
- [ ] inbox/ 已清空(升級成筆記 / 併入 / 刪除)
```

**模板 3:`決策紀錄.md`**

```markdown
---
type: decision
date: {{date}}
scope: owner        # 僅限 Owner 層級;架構決策不用此模板,走正式 Review Process
---

# 決策:

## 背景
## 選項與取捨
## 決定與理由
## 相關文件(連結,不複製)
```

**模板 4:`剪藏.md`**

```markdown
---
type: clipping
source:
captured: {{date}}
---

# (標題)

## 為什麼存(一句話,必填)
## 重點摘錄
## 可以用在哪(專案/未來 Agent 領域)
```

### 2.4 `.obsidian/` 設定檔(建立於 Workspace 根目錄)

**`app.json`** — 注意 `useMarkdownLinks: true`(Model Agnostic,與 v1.0 不同):
```json
{
  "attachmentFolderPath": "owner/_meta/attachments",
  "newFileLocation": "folder",
  "newFileFolderPath": "owner/inbox",
  "alwaysUpdateLinks": true,
  "useMarkdownLinks": true,
  "newLinkFormat": "relative",
  "promptDelete": true
}
```

**`daily-notes.json`**
```json
{
  "folder": "owner/journal",
  "format": "YYYY-MM-DD",
  "template": "owner/_meta/templates/每日筆記",
  "autorun": true
}
```

**`templates.json`**
```json
{ "folder": "owner/_meta/templates" }
```

**`core-plugins.json`**(啟用:file-explorer, global-search, switcher, graph, backlink, outgoing-link, daily-notes, templates, command-palette, outline, word-count, file-recovery, canvas;格式依 Obsidian 版本)

**`community-plugins.json`**
```json
["obsidian-git", "calendar", "dataview", "templater-obsidian"]
```

> 社群插件本體需由 Owner 在 Obsidian 內安裝(市場下載),Codex 只建清單檔。

### 2.5 插件白名單與禁令

| 插件 | 用途 | 合規依據 |
|------|------|------|
| Obsidian Git | 自動 commit/push(建議 15 分鐘) | Git First |
| Calendar | 點日期開 journal | 支援每日證據採集 |
| Dataview | **唯讀**彙整(進行中專案、待清 inbox、證據清單) | 不寫入,不違反 Governance Before Automation |
| Templater | 模板日期運算 | 純模板展開 |

**禁令**:不安裝任何 AI 插件、自動改寫插件、第三方同步插件;新增插件視同流程變更,先過 Review 再裝。

### 2.6 首頁(`owner/首頁.md`)

含三個 Dataview 唯讀區塊:①進行中行銷專案(`type: project` 且 `status: active`)②inbox 待清單 ③`evidence/` 證據清單;以及指向 Workspace 正式文件入口(PAIOS Constitution、Roadmap)的**連結**。

### 2.7 驗收標準

- [ ] 已先讀 Folder Convention 並產出《Obsidian整合對照表.md》
- [ ] 所有新增檔案僅位於 Owner 區、`.obsidian/`、`.gitignore`
- [ ] 未修改任何架構文件、Factory 文件、Governance 文件
- [ ] 4 模板 + 設定檔 + 首頁建立完成,UTF-8,frontmatter 合法
- [ ] 連結全部為標準 Markdown 相對路徑連結
- [ ] Commit 訊息:`Add Obsidian interface layer for Owner (per Obsidian x PAIOS plan v2.0)`,已 push

---

## 第三部分:Owner 後續步驟(非 Codex 範圍)

1. Obsidian → Open folder as vault → 指向 Workspace repo → 安裝 4 個白名單插件 + 瀏覽器 Web Clipper(剪藏目的地設 `owner/marketing/clippings/`)。
2. 第 1–2 週只做:每日 journal(PDCA + PAIOS 證據兩區都填)、想法進 inbox。
3. 每週五:週回顧 + 清 inbox。
4. 證據累積約一個月後,把成形的觀察升級到 `evidence/`;這批筆記就是未來觸發 Architecture Review 的依據。

---

## 第四部分:合規自檢清單(供 ChatGPT Review Board 使用)

- [ ] 是否新增了任何架構層或治理規則?(應為否)
- [ ] 是否繞過 Agent Factory 建立了 Agent 或 Agent 模板?(應為否)
- [ ] 是否複製了正式文件內容到筆記區?(應為否;只允許連結)
- [ ] 是否引入會自動寫入內容的自動化?(應為否;僅 Git 備份)
- [ ] 是否把 Chat 內容當成 Source of Truth?(應為否;本文件自身也服從 Workspace 文件)
- [ ] 是否擴張了 Stage 0 範圍或打亂 Roadmap 順序?(應為否;排程權在 Roadmap)
- [ ] 所有檔案是否為純文字 .md、標準 Markdown 連結、可被任何模型讀取?(應為是)
