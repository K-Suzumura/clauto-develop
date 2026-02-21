---
name: git-commit
description: Create commits following conventions
user-invocable: true
allowed-tools: Bash
---

# /git:commit - 規約準拠コミット

日本語・規約に準拠したコミットを作成してください。

## 実行内容

1. 変更差分を確認
2. 変更内容を要約
3. コミットメッセージを生成
4. ユーザー確認後にコミット

## コミットメッセージ形式

```
<type>: <subject>

<body>

<footer>
```

### type 一覧
| type | 説明 |
|------|------|
| feat | 新機能 |
| fix | バグ修正 |
| docs | ドキュメント |
| style | コードスタイル（動作に影響なし） |
| refactor | リファクタリング |
| test | テスト |
| chore | ビルド・補助ツール |

### 例
```
feat: ユーザー認証機能を追加

- JWTトークンによる認証を実装
- リフレッシュトークン機能を追加
- ログアウト処理を実装

Refs: F-001
```

## 実行フロー

### 1. 変更確認
```bash
git status
git diff --staged
git diff
```

### 2. 変更のステージング
- ステージングされていない変更がある場合、対象を確認
- 関連する変更をまとめてステージング

### 3. コミットメッセージ生成
- 変更内容から自動生成
- type を自動判定
- 関連する機能ID/タスクIDがあれば footer に追加

### 4. ユーザー確認
```
生成したコミットメッセージ:
---
feat: ユーザー認証機能を追加

- JWTトークンによる認証を実装
- リフレッシュトークン機能を追加

Refs: F-001
---

1. このままコミット
2. メッセージを編集
3. キャンセル
```

### 5. コミット実行
```bash
git add [files]
git commit -m "..."
```

## 出力形式

```markdown
# コミット完了

- **コミットハッシュ**: abc1234
- **type**: feat
- **subject**: ユーザー認証機能を追加

## 変更ファイル
- `src/auth/login.ext` (追加)
- `src/auth/logout.ext` (追加)
- `src/auth/token.ext` (追加)

## 次のステップ
- 追加の変更がある場合: 続けて `/git:commit`
- プッシュする場合: `git push` または `/git:pr`
```

## 注意事項

- 1コミットには関連する変更のみを含める
- コミットメッセージは日本語で記述
- 機密情報（.env等）がステージングされていないか確認
