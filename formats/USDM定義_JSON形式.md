# USDM Canonical JSON Schema 定義書

## 1. 概要

本書は、USDM（Universal Specification Describing Manner）のCanonical JSON Schemaを定義する。
すべてのUSDM成果物（マークダウン、Excel、HTML等）は、本Canonical形式を経由して変換される。

スキーマファイル: `schema/v1/document.schema.json`

---

## 2. 文書構造

```
USDMDocument（文書）
├── Metadata（メタデータ） [0..1]
├── Category（カテゴリ） [1..*]
│   └── Requirement（要求） [0..*]
│       ├── {選択} SpecificationGroup（仕様グループ） [0..*]  ← 1階層パターン
│       └── {選択} RequirementGroup（要求グループ） [0..*]   ← 2階層パターン
│                  └── SubRequirement（下位要求） [0..*]
│                       └── SpecificationGroup（仕様グループ） [0..*]
│                            └── Specification（仕様） [0..*]
├── RequirementRelation（要求間関連） [0..*]
└── SpecificationGroupRelation（仕様グループ間関連） [0..*]
```

> **注**: 1つの要求は `specificationGroups`（1階層）と `requirementGroups`（2階層）の両方を同時に持つことが可能。

---

## 3. JSON Schema

正本となる JSON Schema（Draft-07）は **[`schema/v1/document.schema.json`](../schema/v1/document.schema.json)** に定義されている。本書は唯一の正本（Single Source of Truth）であるスキーマファイルを参照し、ここでは全文を再掲しない（二重管理によるドリフトを防ぐため）。

- **正本（機械可読）**: `schema/v1/document.schema.json`
- **スキーマURI（`$id`）**: `https://usdm-meta.dev/schema/v1/document.json`
- **バージョン**: `version` は `"1.1.0"` 固定（`const`）。スキーマフォルダ `schema/v1/` はメジャーバージョンを表し、後方互換のない変更でのみ繰り上げる。
- **検証例**: `npx ajv validate -s schema/v1/document.schema.json -d <document>.json`

各型・プロパティの意味は、本スキーマ内の `description` および以下の章を参照のこと。
- 文書構造の概観 → §2
- 8つの構成要素との対応 → §4
- ID体系 → §5 ／ 仕様ラベル → §6 ／ 分割軸と関連 → §7
- 具体的な記述例 → §8 ／ 設計方針 → §9

---

## 4. 8つの構成要素とスキーマの対応

| # | USDM構成要素 | Canonical JSONでの表現 |
|---|-------------|----------------------|
| 1 | キーワードラベル | `requirement.keywords[]` / `subRequirement.keywords[]` |
| 2 | 要求 | `requirement.requirement` / `subRequirement.requirement` |
| 3 | 理由 | `requirement.reason` / `specification.reason` |
| 4 | 説明 | `requirement.explanation` / `specification.explanation` |
| 5 | 仕様 | `specification.specification` |
| 6 | 仕様ラベル | `specification.verified` （`[boolean, boolean, boolean]`） |
| 7 | グループ | `requirementGroup.name` / `specificationGroup.name`（＜＞で囲む） |
| 8 | カテゴリ（＝上位要求グループ） | `category.name`（＜＞で囲む。上位要求のグループそのもの。「カテゴリ名」「要求グループ」どちらの呼称でも可。IDは持たない） |

---

## 5. ID体系

| 要素 | ID形式 | パターン | 例 |
|------|--------|----------|----|
| Category | （IDなし） | — | ＜機能要求＞（名前のみ） |
| Requirement | `RXX` | `^[A-Za-z][A-Za-z0-9_-]*$` | R01, R02, R11 |
| SubRequirement | `RXX-YY` | 同上 | R01-01, R01-02 |
| Specification | `SXX-YY-ZZ` | 任意文字列 | S01-01-01, spc11-01-01 |

> **注**: カテゴリはIDを持たず＜＞名のみで識別する（任意の内部IDは保持可だが表示しない）。要求・下位要求のIDの命名規約はプロジェクトごとに決定してよい。スキーマはパターン `^[A-Za-z][A-Za-z0-9_-]*$` を要求する（Specificationを除く）。

---

## 6. 仕様ラベル（verified）

USDMの「仕様ラベル」は □ マークで表現される。仕様であることを示すと同時に、ステータス管理にも使用する。

`verified` は `[boolean, boolean, boolean]` の3要素配列。

| 値 | 表示 | 意味 |
|----|------|------|
| `[false, false, false]` | □□□ | 未レビュー |
| `[true, false, false]` | ■□□ | レビュー済み |
| `[true, true, false]` | ■■□ | 実装済み |
| `[true, true, true]` | ■■■ | テスト済み |

---

## 7. 分割軸と関連

USDMメタモデルの「要求分割」「仕様分割」を、トップレベルの関連配列として表現する。

| 関連の種類 | プロパティ | 説明 |
|-----------|-----------|------|
| 要求間関連 | `requirementRelations[]` | 要求→要求の自己関連。`sourceId`, `targetId`, `divisionAxis` |
| 仕様グループ間関連 | `specificationGroupRelations[]` | 仕様グループ→仕様グループの自己関連 |

**分割軸（DivisionAxis）の値:**

| 値 | 説明 |
|----|------|
| `時系列分割` | 時間の流れに沿って分割 |
| `構成分割` | 構成要素ごとに分割 |
| `状態分割` | 状態ごとに分割 |
| `共通分割` | 共通する要素を分割 |

---

## 8. インスタンス例

### 8.1 2階層パターン（要求→要求グループ→下位要求→仕様）

```json
{
  "version": "1.1.0",
  "title": "○○システム 要求仕様書",
  "metadata": {
    "author": "作成者名",
    "created": "2026-03-27T00:00:00Z",
    "modified": "2026-03-27T00:00:00Z"
  },
  "categories": [
    {
      "name": "＜走行制御＞",
      "requirements": [
        {
          "id": "R01",
          "requirement": "システムは、ドライバーの指示に従い設定速度を維持した走行を実現する。",
          "reason": "高速道路での長距離運転時にドライバーの疲労を軽減するため。",
          "explanation": "アクセルペダルを踏まなくても車両が自動的にスロットル開度を調整して一定速度を保つ。",
          "keywords": ["定速走行制御"],
          "requirementGroups": [
            {
              "name": "＜速度制御＞",
              "subRequirements": [
                {
                  "id": "R01-01",
                  "requirement": "ドライバーからの定速走行開始・終了の指示を認識する。",
                  "reason": "ドライバーの意図に応じて適切にクルーズコントロールを作動・解除するため。",
                  "specificationGroups": [
                    {
                      "name": "＜指示の認識＞",
                      "specifications": [
                        {
                          "id": "S01-01-01",
                          "specification": "クルーズコントロールスイッチのSET信号がONになった場合、定速走行モードを開始する。",
                          "verified": [false, false, false]
                        },
                        {
                          "id": "S01-01-02",
                          "specification": "CANCEL信号がONになった場合、定速走行モードを終了する。",
                          "verified": [false, false, false]
                        }
                      ]
                    },
                    {
                      "name": "＜作動条件の判定＞",
                      "specifications": [
                        {
                          "id": "S01-01-03",
                          "specification": "車速が30km/h以上180km/h以下の場合にのみ、定速走行モードを開始可能とする。",
                          "verified": [false, false, false]
                        }
                      ]
                    }
                  ]
                },
                {
                  "id": "R01-02",
                  "requirement": "目標速度を維持するためスロットル開度を調整する。",
                  "reason": "車速を目標速度に追従させるためエンジン出力を制御する必要がある。",
                  "specificationGroups": [
                    {
                      "name": "＜スロットル開度の算出＞",
                      "specifications": [
                        {
                          "id": "S01-02-01",
                          "specification": "目標速度と現在車速の偏差を100msごとに算出する。",
                          "verified": [false, false, false]
                        }
                      ]
                    },
                    {
                      "name": "＜エンジンへの指示＞",
                      "specifications": [
                        {
                          "id": "S01-02-02",
                          "specification": "算出したスロットル開度指令値をCAN通信でエンジンECUに送信する。",
                          "verified": [false, false, false]
                        }
                      ]
                    }
                  ]
                }
              ]
            }
          ]
        }
      ]
    }
  ]
}
```

### 8.2 1階層パターン（要求→仕様グループ→仕様）

```json
{
  "id": "R02",
  "requirement": "システムは、ユーザー操作に対する応答を規定時間内に完了する。",
  "reason": "ユーザーエクスペリエンスを確保するため。",
  "keywords": ["応答時間"],
  "specificationGroups": [
    {
      "name": "＜画面表示の応答＞",
      "specifications": [
        {
          "id": "S02-01",
          "specification": "ボタン操作から画面表示更新までの時間は200ms以内とする。",
          "verified": [false, false, false]
        }
      ]
    },
    {
      "name": "＜ローディング表示の開始＞",
      "specifications": [
        {
          "id": "S02-02",
          "specification": "データ読み込み中は100ms以内にローディングインジケータを表示する。",
          "verified": [false, false, false]
        }
      ]
    }
  ]
}
```

### 8.3 仕様に理由・説明を持つ例

```json
{
  "id": "S03-01-01",
  "specification": "パスワードは8文字以上とし、英大文字・英小文字・数字・記号のうち3種類以上を含むこと。",
  "reason": "不正アクセスを防止するため、十分な強度のパスワードを求める。",
  "explanation": "記号は !@#$%^&*() を使用可能とする。",
  "verified": [true, false, false]
}
```

### 8.4 分割軸の関連を持つ例

```json
{
  "version": "1.1.0",
  "title": "サンプル",
  "categories": [{ "name": "＜機能＞", "requirements": [] }],
  "requirementRelations": [
    {
      "sourceId": "R01",
      "targetId": "R02",
      "divisionAxis": "時系列分割"
    }
  ],
  "specificationGroupRelations": [
    {
      "sourceName": "＜画面レイアウト＞",
      "targetName": "＜操作手順＞",
      "divisionAxis": "構成分割"
    }
  ]
}
```

---

## 9. 設計方針

| 方針 | 内容 |
|------|------|
| スキーマURI | `https://usdm-meta.dev/schema/v1/document.json` |
| バージョン | `"1.1.0"`（`const`で固定。フォルダ `v1` はメジャーバージョン据え置き） |
| プロパティ名 | 英語（日本語表示はビュー側の責務） |
| ID制約 | `^[A-Za-z][A-Za-z0-9_-]*$`（英字始まり。Category・Specificationを除く） |
| カテゴリ | IDを持たず＜＞名のみ。上位要求のグループそのもの（「カテゴリ名」「要求グループ」どちらの呼称でも可） |
| グループ名 | `^＜.+＞$`（全角山括弧。カテゴリ・要求グループ・仕様グループ共通） |
| 仕様ラベル | `verified: [boolean, boolean, boolean]`（3要素固定） |
| 階層パターン | `specificationGroups` / `requirementGroups` の選択（同時保持も可）。深さは上位要求→下位要求の2階層まで |
| 強制2層 | 要求グループ内の下位要求が1つのみの場合、その下位要求の理由・説明を空白にする |
| 仕様グループ・仕様 | それぞれ1以上必須（`minItems: 1`） |
| 分割軸 | `DivisionAxis` enum（4値） |
| 追加プロパティ | `additionalProperties: false`（全定義で厳格） |
