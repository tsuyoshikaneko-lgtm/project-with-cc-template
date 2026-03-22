# TI-PARALLEL: 並列実行可能と定義されたトラック間に隠れた依存関係がある

**ラベル:** `template`, `High`

---

## 背景

ワークフロー（`.claude/rules/workflow.md`）では、Screens 完了後に以下の2トラックを並列で進めると定義されています。

- **Track A**: Specs 導出（DB Schema → API Spec → Auth Spec → UI Spec）
- **Track B**: User Stories → Test Spec

## 問題

Track B に含まれる Test Spec は、Track A の成果物である API Spec や Auth Spec を参照します。具体的には、テスト項目にエンドポイントのパス・HTTPメソッド・レスポンス構造・認証要件などを記述する必要があります。

2つのトラックを並列で実行すると、**未完成の Specs を元に Test Spec が書かれてしまい**、後から不整合が発生します。

## PG1 での事例

- Test Spec に `GET daily_records` と記載されたが、API Spec では `GET /api/v1/trends` として定義されていた
- Test Spec に「auth-spec 具体化後に更新する」というプレースホルダが残ったまま設計完了となった

## 提案

User Stories の記述は Specs 導出と並列で進められますが、Test Spec は API Spec・Auth Spec の完成を待つ必要があるため、以下のように分離します。

```
Track A: Specs 導出（順次）— DB Schema → API Spec → Auth Spec → UI Spec
Track B: User Stories（Track A と並列可）
Track C: Test Spec（Track A 完了後に着手）← Track B から分離
```

この変更により、Test Spec は確定した API Spec・Auth Spec を参照して記述できるようになります。User Stories の並列実行には影響しません。
