---
name: デバッグトリアージ
description: ログ/スタックトレース/再現手順から原因切り分けの型。原因不明バグ、CI失敗、パフォーマンス劣化時に使用。
related_agents:
  - code-debugger
---

# デバッグトリアージ Skill

問題を体系的に分析し、原因を特定するための手順を提供します。

> **関連エージェント**: このSkillは `code-debugger` エージェントが参照する手法ガイドです。
> Agentがデバッグ作業を実行し、このSkillの手法・フレームワークを適用します。

## トリアージプロセス

```
1. 問題の明確化
   ↓
2. 再現の最小化
   ↓
3. 仮説の立案
   ↓
4. 検証コマンド/観測
   ↓
5. 原因特定
   ↓
6. 暫定回避策/恒久対策
```

## 1. 問題の明確化

### 収集すべき情報

| 項目 | 内容 |
|-----|------|
| 現象 | 何が起きているか（エラーメッセージ、挙動） |
| 期待動作 | 本来どうなるべきか |
| 発生頻度 | 常に/時々/特定条件で |
| 発生開始時期 | いつから発生しているか |
| 変更履歴 | 直近のデプロイ/設定変更 |
| 環境 | 本番/ステージング/ローカル |

### スタックトレースの読み方

```
Error: User not found
    at UserService.getById (src/services/user-service.ts:42:11)  ← 直接の原因
    at UserController.show (src/controllers/user-controller.ts:28:25)
    at Router.handle (node_modules/express/lib/router/layer.js:95:5)
    ...
```

1. 最初の行: エラーメッセージ
2. 自分のコードの最上位行: 直接の原因箇所
3. 呼び出し元を遡って文脈を理解

## 2. 再現の最小化

### 再現手順の整理

```markdown
## 再現手順

### 前提条件
- 環境: [環境名]
- ユーザー状態: [ログイン済み/未ログイン]
- データ状態: [特定のデータが必要な場合]

### 手順
1. [操作1]
2. [操作2]
3. [操作3] ← ここでエラー発生

### 再現率
- [100% / 50% / 不定期]
```

### 最小再現の作成

```typescript
// 最小再現テスト
test('再現: ユーザー取得で404エラー', async () => {
  // 問題を再現する最小限のコード
  const service = new UserService(mockRepo);
  mockRepo.findById.mockResolvedValue(null);

  // この呼び出しで問題が再現
  await expect(service.getById('123')).rejects.toThrow('User not found');
});
```

## 3. 仮説の立案

### 仮説テンプレート

| # | 仮説 | 可能性 | 検証方法 |
|---|-----|-------|---------|
| 1 | [仮説1] | 高/中/低 | [検証方法] |
| 2 | [仮説2] | 高/中/低 | [検証方法] |
| 3 | [仮説3] | 高/中/低 | [検証方法] |

### よくある原因パターン

| カテゴリ | 原因例 |
|---------|-------|
| データ | null/undefined、型不一致、破損データ |
| 設定 | 環境変数未設定、設定ミス |
| 依存 | 外部サービス障害、タイムアウト |
| 競合 | 同時実行、デッドロック、レースコンディション |
| リソース | メモリ不足、ディスク不足、コネクション枯渇 |
| コード | ロジックバグ、例外未処理、境界値考慮漏れ |

## 4. 検証コマンド/観測

### ログ確認

```bash
# エラーログの抽出
grep -i error /var/log/app.log | tail -100

# 特定時間帯のログ
awk '/2024-01-15 10:00/,/2024-01-15 11:00/' /var/log/app.log

# JSON ログの解析
cat app.log | jq 'select(.level == "error")'
```

### リソース確認

```bash
# メモリ使用量
free -h
top -o %MEM

# ディスク使用量
df -h

# コネクション数
netstat -an | grep ESTABLISHED | wc -l
lsof -i -P -n | grep LISTEN
```

### DB 確認

```sql
-- スロークエリ
SELECT * FROM pg_stat_activity WHERE state = 'active';

-- ロック確認
SELECT * FROM pg_locks WHERE NOT granted;

-- テーブル状態
SELECT relname, n_live_tup, n_dead_tup FROM pg_stat_user_tables;
```

### アプリケーション確認

```bash
# Node.js ヒープダンプ
node --inspect app.js

# プロファイリング
node --prof app.js
node --prof-process isolate-*.log > processed.txt
```

## 5. 観測ポイント

### 追加すべきログ

```typescript
// 問題箇所の前後にログを追加
logger.debug('getUserById: start', { userId });

const user = await repository.findById(userId);

logger.debug('getUserById: repository result', {
  userId,
  found: !!user,
  user: user ? { id: user.id, name: user.name } : null
});
```

### メトリクス確認

- レスポンスタイム
- エラーレート
- スループット
- CPU/メモリ使用率

## 6. 暫定回避策

### 回避策の種類

| 種類 | 例 | リスク |
|-----|-----|-------|
| 機能無効化 | 問題機能をフラグでOFF | 機能が使えない |
| フォールバック | 代替処理に切り替え | 性能/機能低下 |
| データ修正 | 破損データを修復 | 副作用の可能性 |
| リトライ | 失敗時に再試行 | 遅延/負荷増 |
| 再起動 | プロセス/サービス再起動 | ダウンタイム |

## 出力形式

```markdown
# デバッグトリアージレポート

## 問題サマリー
- **現象**: [エラーメッセージ/挙動]
- **影響範囲**: [影響を受けるユーザー/機能]
- **重要度**: [Critical/High/Medium/Low]
- **発生頻度**: [常に/時々/特定条件]

## 再現手順
[最小再現手順]

## 調査結果

### 確認したログ/メトリクス
[確認した内容と結果]

### 仮説と検証結果
| 仮説 | 検証結果 |
|-----|---------|
| [仮説1] | [結果] |

## 原因
[特定された原因の説明]

## 対策

### 暫定回避策
- **方法**: [回避策の内容]
- **リスク**: [副作用]
- **実施手順**: [手順]

### 恒久対策
- **修正内容**: [必要な修正]
- **影響範囲**: [修正による影響]
- **テスト項目**: [確認すべき項目]

## 再発防止
- [ ] [監視/アラート追加]
- [ ] [テスト追加]
- [ ] [ドキュメント更新]
```
