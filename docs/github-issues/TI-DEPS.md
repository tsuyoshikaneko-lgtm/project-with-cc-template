# TI-DEPS: ドキュメント変更時の影響範囲追跡の仕組みがない

**ラベル:** `template`, `Critical`

---

## 背景

このテンプレートでは、企画（reqs）→ 設計（specs）→ 実装という流れでプロダクト開発を進めます。各ドキュメントは上流から下流への依存関係を持っており、たとえば PRD の変更は概念モデル・DB Schema・API Spec など複数の下流ドキュメントに影響を及ぼします。

## 問題

現在のワークフロー（`.claude/rules/workflow.md`）には「reqs → specs → 実装の順で進める」というルールがありますが、**上流ドキュメントを変更した際に、どの下流ドキュメントを確認・更新すべきか**が明示されていません。

この結果、上流の変更が下流に伝播せず、ドキュメント間で整合性が壊れるという問題が繰り返し発生します。

## PG1 での再現事例（3回）

PG1（メンタル拡張 Oura ダッシュボード）の設計過程で、同じパターンの問題が3回発生しました。

| 上流で行った変更 | 更新が漏れた下流 |
|---|---|
| PRD でメンタルスコアの4軸を変更（不安→エネルギー等） | pg1.md が旧軸名のまま残った |
| PRD に「睡眠ステージ比率」を機能として明記 | DB Schema・API Spec・Test Spec のいずれにも `sleep_stages` が定義されなかった |
| Auth Spec に「メールアドレス＋パスワードによる認証」を記載 | users テーブルに `email` カラムも `password_hash` カラムも存在しなかった |

また、impl-plan が「api-spec v0.2」を参照したまま api-spec が v0.4 に更新されるという、バージョン参照の陳腐化も発生しました。

## 提案

### 1. ドキュメント依存マップを workflow.md に追加する

上流ドキュメントを変更した際に確認すべき下流ドキュメントの一覧を明示します。

| 更新したドキュメント | 確認が必要なドキュメント |
|---|---|
| prd.md | pg{N}.md, conceptual-model.md, user-stories.md |
| conceptual-model.md / product-model.json | db-schema.md, api-spec.md, ui-spec.md |
| api-spec.md | test-spec.md, impl-plan.md, ui-spec.md |
| auth-spec.md | db-schema.md, test-spec.md |
| user-stories.md | test-spec.md |

### 2. Specs 導出後にフィールド照合チェックを必須ステップとして追加する

機械的に検証可能なチェック項目を定義し、Specs 導出の完了条件に含めます。

- PRD 機能概要の全データ項目 → DB Schema のカラムとして存在するか
- DB Schema の全カラム → API Spec のレスポンスフィールドとして存在するか
- API Spec の全エンドポイント → Test Spec のテスト項目として存在するか
- Auth Spec の認証要件 → DB Schema のテーブル・カラムに反映されているか

## 関連 Issue

- 「ワークフローにレビューゲートが組み込まれていない（TI-REVIEW）」が導入されれば、この種の漏れはレビュー時に検出できるようになります。本 Issue はそれに加えて、**レビュー前に自己チェックできる仕組み**を提供するものです。
