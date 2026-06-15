# USDM マークダウン形式 フォーマット定義書

本書は、USDM Canonical JSON Schema に対応するマークダウン表現を定義する。
記法は派生開発推進協議会（AFFORDD）の USDM 構成要素に準拠する。

---

## 1. Canonical JSONとマークダウンの対応

| Canonical JSON要素 | JSONフィールド | マークダウン表現 | 見出し |
|--------------------|----------------|------------------|--------|
| USDMDocument | `title` | `# {title}` | H1 |
| Category（＝上位要求グループ） | `name` | `## ＜{name}＞` | H2 |
| Requirement（上位要求） | `keywords`, `id` | `### {keywords}: 上位要求 {id}` | H3 |
| Requirement | `requirement` | `**要求**: {requirement}` | — |
| Requirement | `reason` | `**理由**: {reason}` | — |
| Requirement | `explanation` | `**説明**: {explanation}` | — |
| RequirementGroup（要求グループ） | `name` | `#### ＜{name}＞` | H4 |
| SubRequirement（下位要求） | `id` | `##### 下位要求 {id}` | H5 |
| SubRequirement | `requirement` | `**要求**: {requirement}` | — |
| SubRequirement | `reason` | `**理由**: {reason}` | — |
| SubRequirement | `explanation` | `**説明**: {explanation}` | — |
| SpecificationGroup（仕様グループ） | `name` | `###### ＜{name}＞` | H6 |
| Specification | `id`, `specification`, `verified` | `- {verified_icon} **{id}**: {specification}` | リスト |

> **カテゴリ（＝上位要求グループ）**: USDMでは最上位のグループは *上位要求のグループ* そのものであり、「カテゴリ名」と呼んでも「要求グループ」と呼んでも同じものを指す（どちらの呼称でもよい）。要求グループ・仕様グループと同様に全角山括弧 `＜＞` で囲んで表記し、ID は付与しない。
> **仕様ラベル**: `{verified_icon}`（□□□ 等）がUSDMの「仕様ラベル」に対応する。□ の存在が「これは仕様である」ことを示し、ステータス管理にも使用する。

---

## 2. 見出し階層ルール

見出しレベルは要素の種類ごとに固定する。山括弧 `＜＞` を使う「グループ系」は偶数レベル、要求は奇数レベルに対応する。

```
H1  # {title}                          ... 文書タイトル
H2  ## ＜カテゴリ名＞                    ... Category＝上位要求グループ（＜＞・ID無し。「カテゴリ名」「要求グループ」どちらの呼称でも可）
H3  ### {keywords}: 上位要求 {id}        ... Requirement（上位要求）
H4  #### ＜要求グループ名＞              ... RequirementGroup（2階層のみ）
H5  ##### 下位要求 {id}                 ... SubRequirement（2階層のみ）
H6  ###### ＜仕様グループ名＞            ... SpecificationGroup
-   - □□□ **{id}**: {仕様テキスト}      ... Specification（リスト）
```

- **1階層パターン**では要求グループ（H4）・下位要求（H5）が存在しないため、上位要求（H3）の直下に仕様グループ（H6）が続く（H4・H5はスキップ）。
- **2階層パターン**ではH1〜H6をすべて使用する。
- 見出しレベルが要素種別を一意に決めるため、レベルを崩してはならない。

---

## 3. 仕様ラベル（verified）の表現

`verified` は `[boolean, boolean, boolean]` の3要素配列。マークダウンでは □/■ で表示する。

| JSON `verified` | マークダウン表記 | 意味 |
|-----------------|------------------|------|
| `[false, false, false]` | □□□ | 未レビュー |
| `[true, false, false]` | ■□□ | レビュー済み |
| `[true, true, false]` | ■■□ | 実装済み |
| `[true, true, true]` | ■■■ | テスト済み |

---

## 4. 1階層パターンの表現

要求が `specificationGroups` を直接持つ場合（`requirementGroups` なし）。シンプルで下位要求への分割が不要な要求に使う。

```markdown
## ＜カテゴリ名＞

### キーワード: 上位要求 R01

**要求**: {requirement}
**理由**: {reason}
**説明**: {explanation}

###### ＜仕様グループ名＞
- □□□ **S01-01**: {specification}
- □□□ **S01-02**: {specification}
```

---

## 5. 2階層パターンの表現

要求が `requirementGroups` を持ち、その中の下位要求が `specificationGroups` を持つ場合。複雑な要求を下位要求に分割するときに使う。

```markdown
## ＜カテゴリ名＞

### キーワード: 上位要求 R01

**要求**: {requirement}
**理由**: {reason}
**説明**: {explanation}

#### ＜要求グループ名＞

##### 下位要求 R01-01

**要求**: {requirement}
**理由**: {reason}
**説明**: {explanation}

###### ＜仕様グループ名＞
- □□□ **S01-01-01**: {specification}
- □□□ **S01-01-02**: {specification}

###### ＜仕様グループ名＞
- □□□ **S01-01-03**: {specification}
```

---

## 6. 強制2層パターンの表現

2階層パターンで、ある要求グループに属する下位要求が **1つだけ** の場合に適用する。下位要求の `requirement` は上位要求とほぼ同義になるため、下位要求の `reason`（理由）・`explanation`（説明）は **空白** とする。

```markdown
### キーワード: 上位要求 R01

**要求**: {requirement}
**理由**: {reason}
**説明**: {explanation}

#### ＜要求グループ名＞

##### 下位要求 R01-01

**要求**: {requirement}
**理由**:
**説明**:

###### ＜仕様グループ名＞
- □□□ **S01-01-01**: {specification}
```

> 強制2層は「2階層として整える」ための運用ルール。理由・説明を空白にすることで、上位要求との重複を避けつつ階層構造を保つ。

---

## 7. テンプレート

`templates/usdm-template.md` が本フォーマット定義に準拠した記入用テンプレートである。構造の全体像を以下に示す。

```markdown
# {title}

## ＜カテゴリ名＞

### キーワード: 上位要求 R01

**要求**: {requirement}
**理由**: {reason}
**説明**: {explanation}

#### ＜要求グループ名＞

##### 下位要求 R01-01

**要求**: {requirement}
**理由**: {reason}
**説明**: {explanation}

###### ＜仕様グループ名＞
- □□□ **S01-01-01**: {specification}
- □□□ **S01-01-02**: {specification}

###### ＜仕様グループ名＞
- □□□ **S01-01-03**: {specification}

##### 下位要求 R01-02

**要求**: {requirement}
**理由**: {reason}
**説明**: {explanation}

###### ＜仕様グループ名＞
- □□□ **S01-02-01**: {specification}

## ＜カテゴリ名＞

### キーワード: 上位要求 R02

**要求**: {requirement}
**理由**: {reason}
**説明**: {explanation}

###### ＜仕様グループ名＞
- □□□ **S02-01**: {specification}
```

---

## 8. 構造上の制約

USDM構成要素（AFFORDD）に基づく制約。

| 制約 | 内容 |
|------|------|
| 階層の深さ | 上位要求 → 下位要求 の **2階層まで**。下位要求はさらに要求グループを持てない。 |
| 強制2層 | 要求が2階層のときに、ある下位要求が1つのみの場合に適用（その下位要求の理由・説明は空白）。 |
| 仕様グループ・仕様 | それぞれ **1以上必須**。各下位要求（または1階層の上位要求）は最低1つの仕様グループを持ち、各仕様グループは最低1つの仕様を持つ。 |
| グループ名 | カテゴリ・要求グループ・仕様グループの名前は全角山括弧 `＜＞` で囲む。 |
| カテゴリ | ID を持たない。上位要求のグループ（＝「カテゴリ名」とも「要求グループ」とも呼べる同一概念）として `＜＞` 名のみで表す。 |

---

## 9. JSON → マークダウン変換の擬似コード

```
print "# {document.title}"

for each category in document.categories:
    print "## {category.name}"          // category.name は ＜＞ で囲まれている

    for each req in category.requirements:
        keywords = join(req.keywords, ", ")
        print "### {keywords}: 上位要求 {req.id}"
        print "**要求**: {req.requirement}"
        print "**理由**: {req.reason}"
        print "**説明**: {req.explanation}"

        // 1階層パターン: specificationGroups を直接出力（H4・H5はスキップ）
        if req.specificationGroups:
            for each sg in req.specificationGroups:
                print "###### {sg.name}"
                for each spec in sg.specifications:
                    print "- {verified_icon(spec.verified)} **{spec.id}**: {spec.specification}"

        // 2階層パターン: requirementGroups → subRequirements → specificationGroups
        if req.requirementGroups:
            for each rg in req.requirementGroups:
                print "#### {rg.name}"
                for each sub in rg.subRequirements:
                    print "##### 下位要求 {sub.id}"
                    print "**要求**: {sub.requirement}"
                    print "**理由**: {sub.reason}"         // 強制2層では空文字
                    print "**説明**: {sub.explanation}"     // 強制2層では空文字
                    for each sg in sub.specificationGroups:
                        print "###### {sg.name}"
                        for each spec in sg.specifications:
                            print "- {verified_icon(spec.verified)} **{spec.id}**: {spec.specification}"

function verified_icon(verified):
    count = count_true(verified)
    if count == 0: return "□□□"
    if count == 1: return "■□□"
    if count == 2: return "■■□"
    if count == 3: return "■■■"
```

### パース時の注意

- 上位要求の見出し `### {keywords}: 上位要求 {id}` は、`: ` で分割した後、右側から先頭の固定ラベル `上位要求 ` を除いて `id` を得る。
- 下位要求の見出し `##### 下位要求 {id}` は、先頭の固定ラベル `下位要求 ` を除いて `id` を得る。
- 「上位要求」「下位要求」は表示用の固定ラベルであり、Canonical JSON には保存しない。
