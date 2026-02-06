---
name: セキュリティベースライン
description: 最低限のセキュリティレビューを毎回抜けなく実施。外部I/O、認証認可、ファイル/ネットワーク、依存追加時に使用。
related_agents:
  - code-reviewer
---

# セキュリティベースライン Skill

コードのセキュリティを体系的にチェックします。

> **関連エージェント**: `code-reviewer` がセキュリティレビュー時にこのSkillの基準を適用します。

## セキュリティチェックリスト

### 1. 入力検証

#### チェック項目
- [ ] すべての外部入力にバリデーションがあるか
- [ ] 入力長の制限があるか
- [ ] 文字種の制限があるか
- [ ] 数値範囲のチェックがあるか
- [ ] 必須/任意の判定があるか

#### 典型的な攻撃パターン

| 攻撃 | 対策 |
|-----|------|
| SQLインジェクション | プリペアドステートメント |
| XSS | 出力エスケープ、CSP |
| コマンドインジェクション | 引数のサニタイズ |
| パストラバーサル | パスの正規化・検証 |
| SSRF | URL のホワイトリスト |

### 2. SQL インジェクション

```typescript
// ❌ 危険
const query = `SELECT * FROM users WHERE id = '${userId}'`;

// ✅ 安全
const query = 'SELECT * FROM users WHERE id = $1';
await db.query(query, [userId]);

// ✅ ORM 使用
await userRepository.findById(userId);
```

### 3. XSS（クロスサイトスクリプティング）

```typescript
// ❌ 危険
element.innerHTML = userInput;

// ✅ 安全
element.textContent = userInput;

// ✅ React の場合（自動エスケープ）
<div>{userInput}</div>

// ❌ 危険（React での生 HTML）
<div dangerouslySetInnerHTML={{ __html: userInput }} />
```

### 4. SSRF（サーバーサイドリクエストフォージェリ）

```typescript
// ❌ 危険
const response = await fetch(userProvidedUrl);

// ✅ 安全
const allowedHosts = ['api.example.com', 'cdn.example.com'];
const url = new URL(userProvidedUrl);
if (!allowedHosts.includes(url.host)) {
  throw new Error('Invalid host');
}
const response = await fetch(url.toString());
```

### 5. 認証・認可

#### チェック項目
- [ ] 認証が必要なエンドポイントに認証チェックがあるか
- [ ] 認可チェックがあるか（自分のリソースのみアクセス可能）
- [ ] トークンの検証が適切か
- [ ] セッション管理が適切か

```typescript
// 認可チェックの例
async function getUser(requesterId: string, targetUserId: string) {
  // 自分以外のユーザー情報を取得しようとしていないか
  if (requesterId !== targetUserId && !isAdmin(requesterId)) {
    throw new ForbiddenError('Access denied');
  }
  return await userRepository.findById(targetUserId);
}
```

### 6. 機密情報の取り扱い

#### ハードコード禁止

```typescript
// ❌ 危険
const apiKey = 'sk-1234567890abcdef';
const dbPassword = 'password123';

// ✅ 安全
const apiKey = process.env.API_KEY;
const dbPassword = process.env.DB_PASSWORD;
```

#### 検出パターン

```regex
# 検出すべきパターン
password\s*=\s*['"][^'"]+['"]
api[_-]?key\s*=\s*['"][^'"]+['"]
secret\s*=\s*['"][^'"]+['"]
token\s*=\s*['"][^'"]+['"]
```

### 7. PII（個人識別情報）

#### 取り扱い注意項目

| 項目 | 注意点 |
|-----|-------|
| 氏名 | 必要最小限の保持 |
| メールアドレス | 暗号化保存を検討 |
| 電話番号 | マスキング表示 |
| 住所 | アクセス制限 |
| クレジットカード | PCI DSS 準拠 |

#### チェック項目
- [ ] PII がログに出力されていないか
- [ ] PII が平文でDB保存されていないか
- [ ] PII へのアクセスが制限されているか
- [ ] PII の保持期間が定義されているか

### 8. ログのセキュリティ

```typescript
// ❌ 危険
logger.info('Login attempt', { email, password });
logger.debug('API request', { headers }); // Authorization含む

// ✅ 安全
logger.info('Login attempt', { email });
logger.debug('API request', {
  headers: { ...headers, authorization: '[REDACTED]' }
});
```

#### ログに含めてはいけない情報
- パスワード
- トークン/APIキー
- クレジットカード番号
- 個人識別情報（マスキングなし）

### 9. 依存パッケージ

```bash
# npm の脆弱性チェック
npm audit

# yarn の脆弱性チェック
yarn audit

# Snyk による詳細チェック
snyk test
```

#### チェック項目
- [ ] 既知の脆弱性がないか
- [ ] 定期的に更新されているか
- [ ] 必要最小限の依存か

### 10. ファイル操作

```typescript
// ❌ 危険（パストラバーサル）
const filePath = path.join(baseDir, userInput);
fs.readFileSync(filePath);

// ✅ 安全
const safePath = path.resolve(baseDir, userInput);
if (!safePath.startsWith(path.resolve(baseDir))) {
  throw new Error('Invalid path');
}
fs.readFileSync(safePath);
```

## 出力形式

```markdown
# セキュリティレビュー結果

## サマリー
- **対象**: [ファイル/PR/機能]
- **判定**: [PASS / FAIL / NEEDS REVIEW]
- **Critical**: [件数]
- **High**: [件数]
- **Medium**: [件数]
- **Low**: [件数]

## 検出された問題

### Critical

#### 問題1: [タイトル]
- **ファイル**: [パス:行番号]
- **種別**: [SQLi / XSS / 認証 / 認可 / 機密情報 等]
- **説明**: [問題の説明]
- **影響**: [攻撃された場合の影響]
- **修正案**:
  ```typescript
  // 修正後のコード
  ```

### High
[同様の形式]

### Medium
[同様の形式]

### Low
[同様の形式]

## 確認済み項目

- [x] 入力検証
- [x] SQLインジェクション
- [x] XSS
- [x] 認証・認可
- [x] 機密情報
- [x] ログ
- [x] 依存パッケージ

## 推奨事項

1. [推奨事項1]
2. [推奨事項2]
```

## 重要度の定義

| レベル | 定義 | 対応 |
|-------|------|------|
| Critical | 即座に悪用可能な脆弱性 | 即座に修正 |
| High | 悪用される可能性が高い | マージ前に修正 |
| Medium | 悪用には条件が必要 | 計画的に修正 |
| Low | リスクは低いが改善推奨 | 検討 |
