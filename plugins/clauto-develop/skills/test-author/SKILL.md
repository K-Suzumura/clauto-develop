---
name: テスト作成
description: Spec からテスト観点とテストコードを標準化して生成。新機能、バグ修正、回帰テスト追加時に使用。
---

# テスト作成 Skill

仕様書からテスト観点を抽出し、標準化されたテストコードを生成します。

## テスト観点の分類

### 1. 正常系（Happy Path）

- 典型的な入力での期待動作
- 全ての正常フローの網羅

### 2. 異常系（Error Cases）

- 無効な入力
- 認証・認可エラー
- 外部サービスエラー
- タイムアウト

### 3. 境界値（Boundary Values）

| 対象 | テストする値 |
|-----|------------|
| 数値 | 最小値、最小値-1、最大値、最大値+1、0 |
| 文字列 | 空文字、最大長、最大長+1、特殊文字 |
| 配列 | 空配列、1要素、最大要素数、最大+1 |
| 日付 | 過去、現在、未来、境界日時 |

## テストコード構造

### ファイル配置

```
tests/
├── unit/           # ユニットテスト
│   └── services/
│       └── user-service.test.ts
├── integration/    # インテグレーションテスト
│   └── api/
│       └── users.test.ts
└── e2e/            # E2Eテスト
    └── user-flow.test.ts
```

### テストファイルの構造

```typescript
import { describe, it, expect, beforeEach, afterEach } from 'vitest';

describe('UserService', () => {
  // テスト対象のセットアップ
  let userService: UserService;
  let mockRepository: MockUserRepository;

  beforeEach(() => {
    mockRepository = createMockUserRepository();
    userService = new UserService(mockRepository);
  });

  afterEach(() => {
    vi.clearAllMocks();
  });

  describe('getUserById', () => {
    describe('正常系', () => {
      it('存在するIDでユーザーを取得できる', async () => {
        // Arrange
        const expectedUser = createTestUser({ id: '123' });
        mockRepository.findById.mockResolvedValue(expectedUser);

        // Act
        const result = await userService.getUserById('123');

        // Assert
        expect(result).toEqual(expectedUser);
        expect(mockRepository.findById).toHaveBeenCalledWith('123');
      });
    });

    describe('異常系', () => {
      it('存在しないIDでNotFoundErrorをスローする', async () => {
        // Arrange
        mockRepository.findById.mockResolvedValue(null);

        // Act & Assert
        await expect(userService.getUserById('999'))
          .rejects.toThrow(NotFoundError);
      });

      it('空のIDでValidationErrorをスローする', async () => {
        await expect(userService.getUserById(''))
          .rejects.toThrow(ValidationError);
      });
    });

    describe('境界値', () => {
      it('最大長のIDを処理できる', async () => {
        const maxLengthId = 'a'.repeat(36); // UUID長
        mockRepository.findById.mockResolvedValue(createTestUser({ id: maxLengthId }));

        const result = await userService.getUserById(maxLengthId);

        expect(result.id).toBe(maxLengthId);
      });
    });
  });
});
```

## テスト命名規則

### describe

```
describe('[テスト対象クラス/関数]')
  describe('[メソッド名]')
    describe('[条件/カテゴリ]')
```

### it / test

```
it('[条件]で[期待する動作]')

例:
it('有効なメールアドレスでユーザーを作成できる')
it('無効なメールアドレスでValidationErrorをスローする')
it('最大長を超える名前で登録を拒否する')
```

## モック方針

### モック対象

| 対象 | モック方法 |
|-----|-----------|
| 外部API | MSW または手動モック |
| DB | インメモリDB または Repository モック |
| 時刻 | vi.useFakeTimers() |
| 乱数 | vi.spyOn(Math, 'random') |
| 環境変数 | vi.stubEnv() |

### モック作成ヘルパー

```typescript
// tests/helpers/mock-factory.ts
export function createMockUserRepository(): MockUserRepository {
  return {
    findById: vi.fn(),
    save: vi.fn(),
    delete: vi.fn(),
  };
}

export function createTestUser(overrides?: Partial<User>): User {
  return {
    id: 'test-id',
    name: 'Test User',
    email: 'test@example.com',
    createdAt: new Date('2024-01-01'),
    ...overrides,
  };
}
```

## テストデータ作成ルール

### Factory パターン

```typescript
// tests/factories/user-factory.ts
import { faker } from '@faker-js/faker';

export const userFactory = {
  build: (overrides?: Partial<User>): User => ({
    id: faker.string.uuid(),
    name: faker.person.fullName(),
    email: faker.internet.email(),
    createdAt: faker.date.past(),
    ...overrides,
  }),

  buildList: (count: number, overrides?: Partial<User>): User[] =>
    Array.from({ length: count }, () => userFactory.build(overrides)),
};
```

### テストデータの原則

- [ ] テストごとに独立したデータを使用
- [ ] マジックナンバーを避け、意図が明確な値を使用
- [ ] 境界値は明示的に定義

## 出力形式

```markdown
# テスト設計書

## 対象
- 仕様書: [パス]
- テスト対象: [クラス/関数]

## テスト観点

### 正常系
| # | 観点 | 入力 | 期待結果 |
|---|-----|------|---------|

### 異常系
| # | 観点 | 入力 | 期待エラー |
|---|-----|------|----------|

### 境界値
| # | 対象 | テスト値 | 期待結果 |
|---|-----|---------|---------|

## 生成されたテストコード

[テストコード]
```

## チェックリスト

- [ ] 全正常パスがテストされているか
- [ ] 全エラーケースがテストされているか
- [ ] 境界値がテストされているか
- [ ] モックが適切に使用されているか
- [ ] テストが独立しているか（順序依存なし）
- [ ] テスト名が意図を表しているか
