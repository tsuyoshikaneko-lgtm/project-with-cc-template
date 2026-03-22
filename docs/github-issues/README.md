# テンプレート改善 Issue 一覧

PG1（メンタル拡張 Oura ダッシュボード）の企画→設計を通じて発見された、テンプレート自体に起因する構造的問題6件。

## Issue 一覧

GitHub に Issue を立てる際は、各ファイルの `# タイトル` 以下をコピーして貼り付けてください。
ID コードは GitHub Issue の番号とは無関係です。相互参照用の識別子として使用しています。

| ID | タイトル | 深刻度 | PG1 での再発回数 |
|---|---|:---:|:---:|
| TI-REVIEW | ワークフローにレビューゲートが組み込まれていない | Critical | — |
| TI-DEPS | ドキュメント変更時の影響範囲追跡の仕組みがない | Critical | 3回 |
| TI-ABSTRACT | PRD の設計原則が抽象的なまま下流に渡る | Critical | 3回 |
| TI-CITATION | LLM が生成した文献引用の正確性を検証する仕組みがない | High | 2回 |
| TI-PARALLEL | 並列実行可能と定義されたトラック間に隠れた依存関係がある | High | 1回 |
| TI-UILIB | UI Spec テンプレートに外部ライブラリ選定セクションがない | High | 1回 |

## 推奨する起票順序

Issue 間の相互参照（「関連 Issue」セクション）が自然につながるよう、以下の順で起票することを推奨します。

```
1. TI-REVIEW  ← 他の Issue が参照するため最初に起票
2. TI-DEPS
3. TI-ABSTRACT
4. TI-CITATION
5. TI-PARALLEL
6. TI-UILIB
```

## 対応の優先順位

```
              TI-REVIEW（レビューゲート）
              ← 最初に導入する。他の全 Issue の早期発見に効く
                   │
        ┌──────────┼──────────┐
        │          │          │
    TI-DEPS   TI-ABSTRACT  TI-CITATION
   (依存追跡)  (抽象→具体)  (文献検証)
    3回再発     3回再発      2回再発
        │          │
   TI-PARALLEL  TI-UILIB
  (並列トラック) (UI技術選定)
```

TI-REVIEW を先に入れれば、他の Issue の問題は「レビューで捕まる」状態になります。
その上で TI-DEPS → TI-ABSTRACT → TI-CITATION の順にワークフローを強化します。
TI-PARALLEL と TI-UILIB はテンプレート構造の修正のみで対応可能です。

## 共通ラベル

すべての Issue に以下のラベルを付与してください。

- `template`
- 深刻度に応じて `Critical` または `High`
