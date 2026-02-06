---
name: coding-standards
description: Apply team coding standards during implementation and review. Covers naming, exception design, logging, and comments.
related_commands:
  - /impl:run
  - /refactor:cleanup
related_agents:
  - code-builder
---

# コーディング規約 Skill

一貫したコード品質を維持するための規約を適用します。

> **関連コマンド**: `/impl:run` `/refactor:cleanup` がこのSkillの規約を適用します。
> **関連エージェント**: `code-builder` が実装時にこの規約を参照します。

## 1. 命名規約

### 変数・関数

| 種別 | 規則 | 例 |
|-----|------|-----|
| 変数 | camelCase | `userId`, `isActive` |
| 定数 | UPPER_SNAKE_CASE | `MAX_RETRY_COUNT`, `API_BASE_URL` |
| 関数 | camelCase + 動詞始まり | `getUserById`, `validateInput` |
| プライベート | 先頭アンダースコア禁止 | `#privateMethod` (ES2022) |
| ブール値 | is/has/can/should 接頭辞 | `isValid`, `hasPermission` |

### クラス・型

| 種別 | 規則 | 例 |
|-----|------|-----|
| クラス | PascalCase | `UserService`, `HttpClient` |
| インターフェース | PascalCase（I 接頭辞禁止） | `Repository`, `Logger` |
| 型エイリアス | PascalCase | `UserId`, `ApiResponse` |
| Enum | PascalCase + 値は UPPER_SNAKE | `Status.PENDING` |

### ファイル・ディレクトリ

| 種別 | 規則 | 例 |
|-----|------|-----|
| ファイル | kebab-case | `user-service.ts` |
| テストファイル | `.test.ts` / `.spec.ts` | `user-service.test.ts` |
| 型定義ファイル | `.types.ts` / `.d.ts` | `api.types.ts` |

## 2. 例外設計

### 例外の種類

```typescript
// カスタム例外の基底クラス
class AppError extends Error {
  constructor(
    message: string,
    public readonly code: string,
    public readonly statusCode: number = 500
  ) {
    super(message);
    this.name = this.constructor.name;
  }
}

// ドメイン例外
class ValidationError extends AppError {
  constructor(message: string) {
    super(message, 'VALIDATION_ERROR', 400);
  }
}

class NotFoundError extends AppError {
  constructor(resource: string) {
    super(`${resource} not found`, 'NOT_FOUND', 404);
  }
}
```

### 例外ハンドリング規則

- [ ] 回復可能な例外は呼び出し元でハンドリング
- [ ] 回復不可能な例外は上位でキャッチしてログ出力
- [ ] 例外は握りつぶさない（空の catch 禁止）
- [ ] 例外メッセージは具体的に
- [ ] スタックトレースを保持

## 3. ログ方針

### ログレベル

| レベル | 用途 | 例 |
|-------|------|-----|
| ERROR | 回復不可能なエラー | DB接続失敗、外部API障害 |
| WARN | 回復可能だが注意が必要 | リトライ発生、非推奨API使用 |
| INFO | 重要なビジネスイベント | ユーザー登録、決済完了 |
| DEBUG | 開発時のデバッグ情報 | 変数の値、処理の流れ |

### ログフォーマット

```typescript
// 構造化ログ
logger.info('User created', {
  userId: user.id,
  email: user.email,
  timestamp: new Date().toISOString()
});

// 禁止: 機密情報のログ出力
// logger.info('Login', { password: input.password }); // NG
```

### ログ規則

- [ ] 機密情報（パスワード、トークン、PII）は出力しない
- [ ] 構造化ログを使用
- [ ] リクエストIDを含める
- [ ] 処理の開始・終了をログ出力

## 4. コメント規約

### JSDoc

```typescript
/**
 * ユーザーを ID で取得する
 *
 * @param userId - ユーザー ID
 * @returns ユーザー情報
 * @throws {NotFoundError} ユーザーが存在しない場合
 * @example
 * const user = await getUser('123');
 */
async function getUser(userId: string): Promise<User> {
  // ...
}
```

### インラインコメント

```typescript
// 良い例: なぜそうするかを説明
// キャッシュの有効期限を過ぎたデータは再取得が必要
if (cache.isExpired(key)) {
  return await fetchFromApi(key);
}

// 悪い例: 何をしているかの説明（コードを見ればわかる）
// キャッシュが期限切れかチェック
if (cache.isExpired(key)) { ... }
```

### コメント規則

- [ ] 公開 API には JSDoc を記述
- [ ] 「なぜ」を説明するコメントを優先
- [ ] TODO/FIXME にはチケット番号を付与
- [ ] 不要なコメントは削除

## 5. 関数設計

### 長さ

- 1関数 = 最大 30 行（目安）
- 長くなる場合は責務を分割

### パラメータ

- 最大 3 パラメータ（それ以上はオブジェクトにまとめる）
- ブール値パラメータは避ける（オプションオブジェクトに）

```typescript
// 良い例
function createUser(options: CreateUserOptions): User

// 悪い例
function createUser(name: string, email: string, isAdmin: boolean, isActive: boolean): User
```

### 早期リターン

```typescript
// 良い例: 早期リターンでネストを減らす
function process(input: Input): Result {
  if (!input) {
    throw new ValidationError('Input is required');
  }

  if (!input.isValid) {
    throw new ValidationError('Input is invalid');
  }

  return doProcess(input);
}
```

## 6. 型・Null 方針

### TypeScript 設定

```json
{
  "compilerOptions": {
    "strict": true,
    "noImplicitAny": true,
    "strictNullChecks": true
  }
}
```

### Null/Undefined

- [ ] `null` と `undefined` を区別して使用
- [ ] 戻り値の「なし」は `undefined`
- [ ] 明示的な「空」は `null`
- [ ] Optional chaining (`?.`) を活用
- [ ] Nullish coalescing (`??`) を活用

```typescript
// 良い例
const name = user?.profile?.name ?? 'Unknown';

// 悪い例
const name = user && user.profile && user.profile.name || 'Unknown';
```

## 7. 依存注入

```typescript
// 良い例: コンストラクタ注入
class UserService {
  constructor(
    private readonly userRepository: UserRepository,
    private readonly logger: Logger
  ) {}
}

// 悪い例: 直接インスタンス化
class UserService {
  private userRepository = new UserRepository();
}
```

## チェックリスト

実装・レビュー時に確認：

- [ ] 命名規約に従っているか
- [ ] 例外が適切にハンドリングされているか
- [ ] ログが適切に出力されているか
- [ ] コメントが適切か
- [ ] 関数の長さ・パラメータ数が適切か
- [ ] 型が厳密に定義されているか
- [ ] 依存が注入されているか
