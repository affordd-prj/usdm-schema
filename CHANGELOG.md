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
  - JSON Schema 定義（`schema/v1/document.schema.json`）
- **標準化文書（11本）**
  - プロジェクト憲章、計画書、WGメンバー募集、技術仕様書、レビュープロセス、ガバナンス、適合性ガイド、移行ガイド、広報計画、リリース成果物定義、文書索引
- **付録**
  - 用語集、USDM入門、JSON Schema入門
- **テンプレート**
  - レビューコメントテンプレート、適合宣言テンプレート、USDM文書テンプレート（Markdown/JSON）
- **フォーマット定義**
  - USDM JSON形式、Markdown形式、表無し形式
- **参考資料**
  - プロジェクト構想書、プロジェクト報告書（Markdown/PDF/PPTX）、USDM記述例（メール検索1階層・2階層）
- **コミュニティファイル**
  - README.md、LICENSE（CC BY 4.0）、NOTICE、CONTRIBUTING.md、CODE_OF_CONDUCT.md、`.github/` テンプレート

### Note

- 本リリースは**標準化ドキュメントおよび規定スキーマ**のみを対象とする。
- リファレンス実装（TypeScript製ツールキット：Importer/Exporter/Validator）は v1.1 以降、別リポジトリ `affordd-prj/usdm-meta` にて公開予定。

[Unreleased]: https://github.com/affordd-prj/usdm-schema/compare/v1.1.0...HEAD
[1.1.0]: https://github.com/affordd-prj/usdm-schema/compare/v1.0.0...v1.1.0
[1.0.0]: https://github.com/affordd-prj/usdm-schema/releases/tag/v1.0.0
