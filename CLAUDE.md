# FFXIV Collection TC - 開發指南

## 專案結構

```
Collections/Website/
├── data/
│   └── collections_data.json    # 主要資料檔（所有收藏品資料）
├── js/
│   ├── app.js                   # 主應用程式
│   ├── components.js            # UI 元件
│   └── filters.js               # 篩選器定義與邏輯
├── css/                         # 樣式檔案
├── tools/                       # 開發工具（被 .gitignore 忽略）
│   ├── csv_cn/                  # 簡體中文 datamining CSV
│   ├── csv_tc/                  # 繁體中文 datamining CSV
│   └── validate_json.py         # JSON 驗證腳本
└── index.html                   # 主頁面
```

## 資料結構

### collections_data.json

```json
{
  "ExportedAt": "...",
  "Collections": [
    {
      "CollectionName": "Minions",  // Mounts, Minions, Emotes, etc.
      "OrderKey": 2,
      "Items": [
        {
          "Id": 511,
          "Name": "中角羊",
          "Description": "...",
          "PatchAdded": 7,
          "DisplayPatch": "7.0",
          "IconId": 4911,
          "IconUrl": "https://xivapi.com/i/004000/004911.png",
          "Sources": [
            {
              "Name": "森林探索委託31級",
              "Type": "Venture",
              "Categories": ["Venture"],
              "IsLocatable": false,
              "IconId": 65049,
              "IconUrl": "https://xivapi.com/i/065000/065049.png"
            }
          ]
        }
      ]
    }
  ]
}
```

### Source 類型 (filters.js SOURCE_CATEGORIES)

| Type | 繁中名稱 | IconId |
|------|----------|--------|
| Gil | 金幣 | 65002 |
| Scrips | 工票 | 65028 |
| MGP | 金碟幣 | 65025 |
| PvP | PvP | 61806 |
| Duty | 副本 | 60414 |
| Quest | 任務 | 61419 |
| Event | 活動 | 61757 |
| Tomestones | 神典石 | 65086 |
| DeepDungeon | 深層迷宮 | 61824 |
| BeastTribes | 蠻族 | 65016 |
| MogStation | 商城 | 61831 |
| Achievement | 成就 | 6 |
| AchievementCertificate | 成就幣 | 65059 |
| CompanySeals | 軍票 | 65005 |
| IslandSanctuary | 無人島 | 65096 |
| HuntSeals | 狩獵 | 65034 |
| TreasureHunts | 挖寶 | 115 |
| Crafting | 製作 | 62202 |
| Voyages | 遠航探索 | 65035 |
| Venture | 雇員探索 | 65049 |

## 常用操作

### 新增篩選分類

1. 編輯 `js/filters.js` 的 `SOURCE_CATEGORIES`
2. 添加新分類：`TypeName: { name: '繁中名稱', iconId: XXXXX }`

### 修改 collections_data.json

**重要：使用 Python 腳本修改 JSON，避免編碼問題**

錯誤的做法：直接用文字編輯器編輯大型 JSON（容易破壞中文引號）

正確的做法：
```python
import json

with open('data/collections_data.json', 'r', encoding='utf-8') as f:
    data = json.load(f)

# 修改資料...

with open('data/collections_data.json', 'w', encoding='utf-8') as f:
    json.dump(data, f, ensure_ascii=False, indent=2)
```

### 驗證 JSON 格式

```bash
cd tools
python validate_json.py
```

驗證內容：
- JSON 語法正確性
- 頂層結構 (ExportedAt, Collections)
- 各 Collection 的 Items 結構
- Sources 陣列格式

## 外部資料來源

### ffxivcollect.com API

```
https://ffxivcollect.com/api/minions
https://ffxivcollect.com/api/mounts
```

Source Type ID 對照：
- 17 = Venture (雇員探索)

### XIVAPI

圖示 URL 格式：
```
https://xivapi.com/i/{folder}/{icon}.png
folder = floor(iconId / 1000) * 1000，補零到6位
icon = iconId，補零到6位

例：iconId = 65049
folder = 065000
icon = 065049
URL = https://xivapi.com/i/065000/065049.png
```

### Datamining CSV

- `tools/csv_tc/` - 繁體中文版本
- `tools/csv_cn/` - 簡體中文版本（用於對照）

常用檔案：
- `RetainerTaskRandom.csv` - 雇員探索任務
- `Companion.csv` - 寵物資料

## Git 提交規範

提交訊息格式：
```
feat: 添加新功能
fix: 修正錯誤
data: 資料更新/翻譯
```

不要在提交訊息中提及 AI 工具。

## 注意事項

1. **JSON 編碼**：collections_data.json 包含特殊中文引號（""），直接文字編輯容易破壞格式
2. **tools 目錄**：被 .gitignore 忽略，不會提交到 git
3. **驗證流程**：修改 JSON 後務必執行 `validate_json.py` 驗證
