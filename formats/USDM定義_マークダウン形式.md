# USDM マークダウン形式 フォーマット定義書

本書は、USDM Canonical JSON Schema に対応するマークダウン表現を定義する。

---

## 1. Canonical JSONとマークダウンの対応

| Canonical JSON要素 | JSONフィールド | マークダウン表現 |
|--------------------|----------------|------------------|
| USDMDocument | `title` | `# {title}` (H1) |
| Category | `id`, `name` | `## [{id}] {name}` (H2) |
| Requirement | `id`, `keywords`, `requirement` | `### {keywords}: {id}` (H3) |
| Requirement | `requirement` | `**要求**: {requirement}` |
| Requirement | `reason` | `**理由**: {reason}` |
| Requirement | `explanation` | `**説明**: {explanation}` |
| RequirementGroup | `name` | `#### {name}` (H4) |
| SubRequirement | `id` | `#### {id}` (H4) |
| SubRequirement | `requirement` | `**要求**: {requirement}` |
| SubRequirement | `reason` | `**理由**: {reason}` |
| SubRequirement | `explanation` | `**説明**: {explanation}` |
| SpecificationGroup | `name` | `##### {name}` (H5) |
| Specification | `id`, `specification`, `verified` | `- {verified_icon} **{id}**: {specification}` |

> **仕様ラベル**: `{verified_icon}`（□□□ 等）がUSDMの「仕様ラベル」に対応する。□ の存在が「これは仕様である」ことを示し、ステータス管理にも使用する。

---

## 2. 仕様ラベル（verified）の表現

`verified` は `[boolean, boolean, boolean]` の3要素配列。マークダウンでは □/■ で表示する。

| JSON `verified` | マークダウン表記 | 意味 |
|-----------------|------------------|------|
| `[false, false, false]` | □□□ | 未レビュー |
| `[true, false, false]` | ■□□ | レビュー済み |
| `[true, true, false]` | ■■□ | 実装済み |
| `[true, true, true]` | ■■■ | テスト済み |

---

## 3. 1階層パターンの表現

要求が `specificationGroups` を直接持つ場合（`requirementGroups` なし）。

```markdown
### キーワード: R01

**要求**: {requirement}
**理由**: {reason}
**説明**: {explanation}

##### ＜仕様グループ名＞
- □□□ **S01-01**: {specification}
- □□□ **S01-02**: {specification}
```

---

## 4. 2階層パターンの表現

要求が `requirementGroups` を持ち、その中の下位要求が `specificationGroups` を持つ場合。

```markdown
### キーワード: R01

**要求**: {requirement}
**理由**: {reason}
**説明**: {explanation}

#### ＜要求グループ名＞

#### R01-01

**要求**: {requirement}
**理由**: {reason}
**説明**: {explanation}

##### ＜仕様グループ名＞
- □□□ **S01-01-01**: {specification}
- □□□ **S01-01-02**: {specification}

##### ＜仕様グループ名＞
- □□□ **S01-01-03**: {specification}
```

---

## 5. テンプレート

```markdown
# {title}

## [C01] カテゴリ名

### キーワード: R01

**要求**: {requirement}
**理由**: {reason}
**説明**: {explanation}

#### ＜要求グループ＞

#### R01-01

**要求**: {requirement}
**理由**: {reason}
**説明**: {explanation}

##### ＜仕様グループ＞
- □□□ **S01-01-01**: {specification}
- □□□ **S01-01-02**: {specification}

##### ＜仕様グループ＞
- □□□ **S01-01-03**: {specification}

#### R01-02

**要求**: {requirement}
**理由**: {reason}
**説明**: {explanation}

##### ＜仕様グループ＞
- □□□ **S01-02-01**: {specification}

## [C02] カテゴリ名

### キーワード: R02

**要求**: {requirement}
**理由**: {reason}
**説明**: {explanation}

##### ＜仕様グループ＞
- □□□ **S02-01**: {specification}
```

---

## 6. JSON → マークダウン変換の擬似コード

```
print "# {document.title}"

for each category in document.categories:
    print "## [{category.id}] {category.name}"

    for each req in category.requirements:
        keywords = join(req.keywords, ", ")
        print "### {keywords}: {req.id}"
        print "**要求**: {req.requirement}"
        print "**理由**: {req.reason}"
        print "**説明**: {req.explanation}"

        // 1階層パターン: specificationGroups を直接出力
        if req.specificationGroups:
            for each sg in req.specificationGroups:
                print "##### {sg.name}"
                for each spec in sg.specifications:
                    print "- {verified_icon(spec.verified)} **{spec.id}**: {spec.specification}"

        // 2階層パターン: requirementGroups → subRequirements → specificationGroups
        if req.requirementGroups:
            for each rg in req.requirementGroups:
                print "#### {rg.name}"
                for each sub in rg.subRequirements:
                    print "#### {sub.id}"
                    print "**要求**: {sub.requirement}"
                    print "**理由**: {sub.reason}"
                    print "**説明**: {sub.explanation}"
                    for each sg in sub.specificationGroups:
                        print "##### {sg.name}"
                        for each spec in sg.specifications:
                            print "- {verified_icon(spec.verified)} **{spec.id}**: {spec.specification}"

function verified_icon(verified):
    count = count_true(verified)
    if count == 0: return "□□□"
    if count == 1: return "■□□"
    if count == 2: return "■■□"
    if count == 3: return "■■■"
```
