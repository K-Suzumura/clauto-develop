# /git:branch - ブランチ作成

仕様に基づいたブランチを作成してください。

## 使用方法

```
/git:branch
/git:branch [機能ID]
/git:branch F-001
```

## 実行内容

1. 現在のブランチ状態を確認
2. `docs/spec.md` または `docs/plan.md` から機能/タスク情報を取得
3. ブランチ名を提案
4. ユーザー確認後にブランチを作成

## ブランチ命名規則

### プレフィックス
| プレフィックス | 用途 |
|--------------|------|
| `feature/` | 新機能 |
| `fix/` | バグ修正 |
| `refactor/` | リファクタリング |
| `docs/` | ドキュメント |
| `test/` | テスト追加 |
| `chore/` | その他（設定変更等） |

### 命名フォーマット
```
{prefix}/{issue-id}-{short-description}
```

### 例
- `feature/F-001-user-authentication`
- `fix/BUG-123-login-error`
- `refactor/cleanup-api-client`

## 実行フロー

### 1. 現状確認
```bash
git status
git branch
```

### 2. ブランチ名提案
- 機能ID が指定された場合: その機能に基づいて命名
- 指定されない場合: 最近の作業内容から推測して提案

### 3. ユーザー確認
```
提案するブランチ名: feature/F-001-user-authentication

このブランチ名でよいですか？
1. はい、作成する
2. 別の名前を指定する
3. キャンセル
```

### 4. ブランチ作成
```bash
git checkout -b {branch-name}
```

## 出力形式

```markdown
# ブランチ作成完了

- **ブランチ名**: feature/F-001-user-authentication
- **ベースブランチ**: main
- **関連機能**: F-001 ユーザー認証

## 次のステップ
1. `/impl:run T-001` で実装開始
2. 実装完了後 `/git:commit` でコミット
```

## 注意事項

- 未コミットの変更がある場合は警告
- ベースブランチが最新か確認を促す
- 既存ブランチと重複する場合はエラー
