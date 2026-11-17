# 美股長倉 Paper-Trading

> 狀態：籌備中，規則未全部確認。呢個係由 Perplexity 交接文件（2026-11-16）拆出嚟嘅 repo 結構，暫存喺 `~/workspace/goals/paper-trading/repo/`，開咗 GitHub 私人 repo 之後成個搬過去。
> 原文件全文存喺 `docs/交接文件/Perplexity交接_2026-11-16.md`，係唯一底本。

## 項目目的

建立一套美股長倉 paper-trading 流程，先測試：

1. 規則能否被清晰、重複地執行。
2. 訊號、事前計劃與事後回放是否一致。
3. 在一致執行下，策略的交易結果及問題所在。
4. 將來由使用者親自進行真實交易時，是否可以沿用同一套工作流程。

核心：事前保存交易計劃，事後只按當時已知的計劃及行情順序核算結果。

## 策略主幹

美股只做長倉，以 A 回檔企穩為主、B 雙日突破確認為例外；Day1 完成訊號，Day2 開市前保存交易計劃及固定股數，Day3 按分鐘行情回放。成交後以實際模擬成交價及事前 ATR14 計算初始止蝕與 +2R 目標。單筆計劃風險不得超過帳戶值 1.5%，不設合計止蝕風險硬上限或回撤自動停開門檻。

## 分工

| 角色 | 工作 |
| :-- | :-- |
| 使用者 | 決定及批准規則；將來親自交易 |
| Grok Bot（新 Bot） | 接手項目規劃、規則整理、待辦追蹤及後續流程落地；實際自動化能力須另行驗證 |
| Perplexity | Ad hoc 分析、研究、規則討論及個別問題檢視 |
| 舊投資 Bot | 凍結做顧問，只按舊有規則畀意見 |

## 結構

```text
paper-trading/
├── README.md
├── .gitignore
├── rules/                  # 只放已鎖定規則；以下四份係基線／草稿，狀態見各檔檔頭
│   ├── 美股研究及Paper_Trading系統需求與制定方針_v0.1.md  # v0.1 基線，凍結唔改
│   ├── 美股策略_v0.2_Strategy_v1_草稿.md                    # 草稿，未採納
│   ├── v0.2決定工作表_草稿.md
│   ├── 美股研究及Paper_Trading系統需求審閱附錄_v0.1-review.md  # 草稿
│   ├── 已鎖定規則.md        # 資金、風險、股數公式、ATR
│   ├── 選股框架.md
│   ├── 入場方式.md          # A／B
│   ├── 執行流程.md          # Day1／Day2／Day3
│   ├── 持倉管理.md
│   ├── 市寬框架.md
│   ├── 市寬狀態政策.md
│   └── 規則變更紀錄.md
├── state/                  # 狀態（程式讀寫）
│   ├── current_state.md
│   ├── market_state.json
│   ├── watchlist.csv
│   ├── positions.csv
│   ├── last_success.json
│   ├── trade_journal.csv
│   └── daily_plans/        # Day2 事前計劃
├── decisions/
│   ├── decisions-needed.md # 全部待定事項
│   └── decisions-log.md    # 已批決定紀錄
├── research/
│   ├── queue.md            # 問 Perplexity 嘅問題
│   └── answers/
├── scripts/                # Alpaca 數據、篩選、市寬、sector 程式
├── data/
│   ├── raw/                # 原始大數據，唔入 git
│   └── digests/            # 每日摘要（YYYY-MM-DD.md，一日一個檔，累積）
│       ├── weekly/         # 每週市寬回顧（YYYY-Www.md＋WEEKLY_TEMPLATE.md）
│       └── monthly/        # 每月市寬回顧（YYYY-MM.md＋MONTHLY_TEMPLATE.md）
├── docs/
│   ├── 文件使用原則.md
│   ├── 數據來源.md
│   ├── 實施次序.md
│   ├── 架構決定_草稿.md     # 草稿，未採納
│   ├── bots.md
│   ├── prompts/
│   │   └── 新Paper_Trade_Bot_Instruction_Prompt.md  # 草稿，等 Strategy v1 確認先用
│   └── 交接文件/
│       └── Perplexity交接_2026-11-16.md
└── logs/
    └── daily_replays/      # Day3 回放
```

## 尚未完成的核心

Trigger、例外成交處理、sector 起步限制、完整退出規則、選股量化定義、市寬狀態切換，以及資料工具驗證。

## 注意

- 已鎖定規則喺 `rules/`；未定事項一律喺 `decisions/decisions-needed.md`，唔可以當規則用。
- v0.1 需求文件、Strategy v1 草稿、v0.2 決定工作表、審閱附錄、架構決定草稿、新 bot instruction prompt 已經由 `../files/` 搬入 `rules/`／`docs/`／`docs/prompts/`（本地暫存，未推上 GitHub）。原本 `../files/` 嘅正本留低未刪，等推上 GitHub 確認無誤先至清理，唔好留兩個版本。
- 規則改動留版本及差異紀錄，唔好淨係覆寫（見 `rules/規則變更紀錄.md`）。
- 第一階段只做 paper trade，唔做實盤。
