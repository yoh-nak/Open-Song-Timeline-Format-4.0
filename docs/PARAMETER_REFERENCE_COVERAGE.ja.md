# OSTF 4.0 — パラメーターリファレンス網羅監査

> English: [`PARAMETER_REFERENCE_COVERAGE.md`](PARAMETER_REFERENCE_COVERAGE.md)

監査対象：

- `schema/ostf-4.0.0.schema.json`
- `docs/PARAMETER_REFERENCE.ja.md`

## 結果

- Schema `$defs`: **91**
- Parameter Referenceの`$defs`見出し: **91**
- `$defs`見出しCoverage: **91 / 91**
- Schema内の重複を除いたproperty名: **207**
- Reference本文に名称が登場するproperty名: **207 / 207**

判定：**PASS**

`PARAMETER_REFERENCE.ja.md`はRootに加え、Schemaの全`$defs`を1対1で列挙しています。

## `$defs`未掲載

- なし

## property名未掲載

- なし

## 注意

この監査は「Schemaに存在する構造名がReferenceに掲載されているか」のCoverage確認です。

Schemaだけでは表現できない、

- ID参照整合
- MPE Zone意味
- same-tick順序
- targetNoteId
- Dependency/current/stale
- Compatibility Playback
- Migration / downgrade loss

等の意味規則は`FORMAT_SPECIFICATION.ja.md`と`COMPLETE_MANUAL.ja.md`を正本とします。
