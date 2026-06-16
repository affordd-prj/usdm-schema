# Changelog

本リポジトリの変更履歴。[Keep a Changelog](https://keepachangelog.com/ja/1.1.0/) 形式に準拠し、バージョニングは [Semantic Versioning 2.0.0](https://semver.org/lang/ja/) に従う。

## [Unreleased]

## [1.1.0] - 2026-06-16

### Changed — AFFORDDスライド準拠への整合（後方互換のないスキーマ調整を含むがメジャー据え置き）

- 派生開発推進協議会「USDMの構成要素」スライドを真として、規定スキーマ・フォーマット定義・テンプレートを整合
  - カテゴリを `＜カテゴリ名＞`（ID無し・山括弧）に変更。上位要求のグループそのものであり「カテゴリ名」「要求グループ」どちらの呼称でも可
  - 見出し階層を固定（H1文書 / H2カテゴリ / H3上位要求 / H4要求グループ / H5下位要求 / H6仕様グループ）。要求グループを見出し化
  - 強制2層ルールを追加（要求グループ内の下位要求が1つのみ→理由・説明は空白）
  - 仕様グループ・仕様を1以上必須化（`minItems: 1`）
  - `Category.id` 必須を撤廃、`SubRequirement.reason` を任意化
- `version` を `1.1.0` に更新（スキーマフォルダ `schema/v1/` はメジャーバージョン据え置き）

## [1.0.0] - 2026-05-XX

### Added — 初版リリース

- **規定スキーマ**
  - Canonical JSON Schema 定義（`schema/v1/document.schema.json`）
- **フォーマット定義**
  - Canonical JSON 形式 定義書（`formats/USDM定義_JSON形式.md`）
  - マークダウン形式 定義書（`formats/USDM定義_マークダウン形式.md`）
- **テンプレート**
  - USDM 文書テンプレート（`templates/usdm-template.json` / `templates/usdm-template.md`）
- **リポジトリ基本ファイル**
  - README.md、LICENSE（CC BY 4.0）

### Note

- 本リポジトリは USDM の**規定スキーマ・フォーマット定義・テンプレート（成果物）**のみを公開対象とする。標準化の過程で作成したプロジェクトドキュメント（憲章・計画・ガバナンス等）は公開しない。
- リファレンス実装（TypeScript製ツールキット：Importer/Exporter/Validator）は v1.1 以降、別リポジトリ `affordd-prj/usdm-meta` にて公開予定。

[Unreleased]: https://github.com/affordd-prj/usdm-schema/compare/v1.1.0...HEAD
[1.1.0]: https://github.com/affordd-prj/usdm-schema/compare/v1.0.0...v1.1.0
[1.0.0]: https://github.com/affordd-prj/usdm-schema/releases/tag/v1.0.0
