# AFFORDD USDM Canonical JSON Schema

[![License: CC BY 4.0](https://img.shields.io/badge/License-CC_BY_4.0-lightgrey.svg)](https://creativecommons.org/licenses/by/4.0/)
[![Version](https://img.shields.io/badge/Version-1.0.0-blue.svg)](#)
[![Status](https://img.shields.io/badge/Status-Draft-orange.svg)](#)

USDM（Universal Specification Describing Manner）の要求仕様データを、Excelに依存せずに**正規化されたJSON形式**で扱うための標準スキーマと、そのフォーマット定義・テンプレートを提供する。派生開発推進協議会（AFFORDD）による公開規格。

---

## English Summary

USDM (Universal Specification Describing Manner) is a Japanese-origin requirements specification technique that hierarchically describes "requirements" and "specifications". In current practice, Excel is the de facto standard format, which limits diff management, automation, and tool integration.

This repository, maintained by the **Association For FuRther DevelopmenD (AFFORDD)**, provides:

- the **canonical JSON Schema** for USDM documents,
- **format definitions** describing how the canonical JSON maps to JSON / Markdown representations, and
- **authoring templates** to start writing USDM specifications.

The canonical schema enables a **single source of truth** in JSON, **schema-validated** documents, **Git-friendly** diff management, and **N+N** import/export paths instead of N×N format conversions.

All artifacts are licensed under **CC BY 4.0**.

---

## このリポジトリで提供するもの

| 種別 | 成果物 | 場所 |
|-----|--------|------|
| **規定** | JSON Schema 定義 | [`schema/v1/document.schema.json`](./schema/v1/document.schema.json) |
| 規定 | Canonical JSON 形式 定義書 | [`formats/USDM定義_JSON形式.md`](./formats/USDM定義_JSON形式.md) |
| 規定 | マークダウン形式 定義書 | [`formats/USDM定義_マークダウン形式.md`](./formats/USDM定義_マークダウン形式.md) |
| テンプレート | USDM テンプレート（JSON） | [`templates/usdm-template.json`](./templates/usdm-template.json) |
| テンプレート | USDM テンプレート（Markdown） | [`templates/usdm-template.md`](./templates/usdm-template.md) |

---

## なぜCanonical JSONか

```
                     ┌─────────────┐
                     │  Canonical   │
                     │    JSON      │  ← 正本（Single Source of Truth）
                     └──────┬──────┘
                            │
          ┌─────────┬───────┼───────┬──────────┐
          ▼         ▼       ▼       ▼          ▼
       Excel    Markdown   HTML  Legacy JSON  YAML
       (従来形式) (ドキュメント) (Webビュー) (後方互換) (将来対応)
```

- **差分が取れる**：JSONはテキストなのでGitで意味のある差分が取れる
- **検証可能**：JSON Schema により構造の正しさを機械的にチェックできる
- **変換が爆発しない**：各フォーマットは Canonical を経由するので N+N の変換ロジックで済む
- **ツール非依存**：Excelが手元になくても、Markdownエディタやテキストエディタで仕様を編集できる

USDMドキュメントは「文書 → カテゴリ → 要求 → 仕様グループ／要求グループ → 仕様」という階層で構成される。詳細な構造定義は [`formats/USDM定義_JSON形式.md`](./formats/USDM定義_JSON形式.md) を参照。

---

## クイックスタート

### 1. 自分のUSDM文書を検証してみる

```bash
# JSON Schema 検証ツール ajv-cli を使う
npx ajv validate -s schema/v1/document.schema.json -d your-document.json
```

### 2. テンプレートから書き始める

```bash
# JSON で書く場合
cp templates/usdm-template.json my-spec.json

# Markdown で書く場合
cp templates/usdm-template.md my-spec.md
```

### 3. フォーマットを読み解く

- [`formats/USDM定義_JSON形式.md`](./formats/USDM定義_JSON形式.md) — Canonical JSON の文書構造と各要素の定義
- [`formats/USDM定義_マークダウン形式.md`](./formats/USDM定義_マークダウン形式.md) — Canonical JSON とマークダウン表現の対応

---

## 改定方針

本リポジトリは現行の **v1.0.0**（規定スキーマ + フォーマット定義 + テンプレート）を基準とする。

スキーマに不備や改善点が見つかった場合に改定する。後方互換性を壊さない修正はマイナー／パッチ、構造を変える破壊的変更はメジャーバージョンを更新する（semver）。

---

## ライセンス

本リポジトリの全ファイルは **[CC BY 4.0](https://creativecommons.org/licenses/by/4.0/)** に基づき公開されている。詳細は [`LICENSE`](./LICENSE) を参照。

> Copyright (c) 2026 Kenichi Saito, 派生開発推進協議会 (AFFORDD)

---

## 関連リンク

- 派生開発推進協議会 (AFFORDD): https://affordd.jp/
- USDM技法の原典: 清水吉男『要求を仕様化する技術・表現する技術』

---

*This work is part of the AFFORDD standardization initiative for derivative software development.*
