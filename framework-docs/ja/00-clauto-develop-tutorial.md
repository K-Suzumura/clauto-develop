# 玄人開発（clauto-develop）チュートリアル

ECサイト構築を例に、Template repositoryからプロジェクトを作成し、サブエージェントを活用して開発を進める実践的なチュートリアルです。

---

## 目次

1. [はじめに](#1-はじめに)
2. [プロジェクトの作成](#2-プロジェクトの作成)
3. [プロジェクトの設定](#3-プロジェクトの設定)
4. [Phase 1: 要求分析](#4-phase-1-要求分析)
5. [Phase 2: 仕様策定](#5-phase-2-仕様策定)
6. [Phase 3: 技術選定](#6-phase-3-技術選定)
7. [Phase 4: 設計](#7-phase-4-設計)
8. [Phase 5: 実装](#8-phase-5-実装)
9. [Phase 6: レビュー](#9-phase-6-レビュー)
10. [機能追加演習](#10-機能追加演習)
11. [バグ修正演習](#11-バグ修正演習)
12. [Tips & まとめ](#12-tips--まとめ)

---

## 1. はじめに

### 1.1 このチュートリアルで学ぶこと

- Template repositoryからプロジェクトを作成する方法
- CLAUDE.mdとREADME.mdをプロジェクトに合わせて設定する方法
- サブエージェントを活用した開発フローの実践
- 各フェーズでのエージェントの使い分け

### 1.2 作成するECサイトの概要

シンプルなECサイトを構築します。

**主な機能**:
- 商品一覧表示
- 商品詳細表示
- ショッピングカート
- 注文処理
- ユーザー認証

**技術スタック**（このチュートリアルでの例）:
- フロントエンド: Next.js + TypeScript
- バックエンド: Next.js API Routes
- データベース: PostgreSQL + Prisma
- スタイリング: Tailwind CSS

> 技術スタックはプロジェクトに応じて変更可能です。

### 1.3 前提条件

- GitHubアカウント
- Node.js（v18以上）
- Claude Code CLI（インストール済み）
- uv（Pythonパッケージマネージャ、Serena用）
- 基本的なWeb開発の知識

> **Serena MCP**: 本フレームワークではSerena（LSPベースのセマンティックコード操作ツール）を活用します。Template repositoryには`.mcp.json`が同梱されており、自動的にSerenaが有効になります。

---

## 2. プロジェクトの作成

### 2.1 Template repositoryからリポジトリを作成

1. **GitHubでテンプレートからリポジトリを作成**

   clauto-developリポジトリページで「Use this template」ボタンをクリック

   ```
   https://github.com/[owner]/clauto-develop
   ```

   - 「Create a new repository」を選択
   - Repository name: `ec-shop`（任意の名前）
   - 「Create repository」をクリック

2. **ローカルにクローン**

   ```bash
   git clone https://github.com/[your-username]/ec-shop.git
   cd ec-shop
   ```

3. **不要なファイルを削除（オプション）**

   フレームワーク説明ドキュメントが不要な場合：

   ```bash
   rm -rf framework-docs/
   ```

### 2.2 ディレクトリ構造の確認

```
ec-shop/
├── .claude/
│   └── agents/              # サブエージェント定義（10種類）
├── .github/                 # GitHub テンプレート
├── .mcp.json                # Serena MCP設定（自動有効）
├── docs/
│   └── specs/               # 仕様書を配置
├── src/                     # ソースコード
├── tests/                   # テスト
├── CLAUDE.md.template       # Claude Code設定テンプレート
├── README.md.template       # プロジェクトREADMEテンプレート
└── README.md
```

---

## 3. プロジェクトの設定

### 3.1 テンプレートのコピー

テンプレートファイルをコピーして編集します。

```bash
# Claude Code 設定ファイルをコピー
cp CLAUDE.md.template CLAUDE.md

# プロジェクト README をコピー
cp README.md.template README.md
```

### 3.2 CLAUDE.md の編集

`CLAUDE.md` は Claude Code の動作設定ファイルです。言語設定やコーディング規約を確認・調整します。

```markdown
## 言語設定

- 応答言語: 日本語
- コード内コメント: 日本語
- コミットメッセージ: 日本語
- ドキュメント: 日本語
```

このチュートリアルでは、デフォルトの日本語設定のまま使用します。

### 3.3 README.md の編集

`README.md` にプロジェクト固有の情報を記載します。

```markdown
# EC Shop

## 概要

シンプルなECサイト。商品の閲覧、カート管理、注文機能を提供する。

## 技術スタック

| カテゴリ | 技術 |
|---------|------|
| フロントエンド | Next.js 14 (App Router) + TypeScript |
| バックエンド | Next.js API Routes |
| データベース | PostgreSQL + Prisma |
| インフラ | Docker (開発環境) |
| テスト | Jest + React Testing Library |

## セットアップ

### 前提条件

- Node.js 20.x 以上
- Docker

### インストール

```bash
npm install
cp .env.example .env.local
```

## 開発

```bash
npm run dev              # 開発サーバー起動
npm run build            # ビルド
npm run lint             # リント
npm test                 # テスト実行

# データベース
npm run db:migrate       # マイグレーション実行
npm run db:seed          # シードデータ投入
npm run db:studio        # Prisma Studio起動
```

## プロジェクト構造

```
ec-shop/
├── src/
│   ├── app/              # Next.js App Router
│   │   ├── (shop)/       # ショップ関連ページ
│   │   ├── api/          # API Routes
│   │   └── layout.tsx
│   ├── components/       # UIコンポーネント
│   ├── lib/              # ユーティリティ
│   └── types/            # 型定義
├── prisma/               # Prismaスキーマ
├── tests/                # テストファイル
└── docs/specs/           # 仕様書
```
```

### 3.4 Claude Codeの起動

設定が完了したら、Claude Codeを起動します。

```bash
claude
```

---

## 4. Phase 1: 要求分析

### 4.1 曖昧な要求から始める

ユーザー（あなた）がClaude Codeに以下のように要求します：

```
シンプルなECサイトを作りたい。
ユーザーが商品を見て、カートに入れて、購入できるようにしたい。
```

### 4.2 req-analyzerによる分析

Claude Codeに分析を依頼します：

```
req-analyzer として、以下の要求を分析してください：
「シンプルなECサイトを作りたい。ユーザーが商品を見て、カートに入れて、購入できるようにしたい。」
```

### 4.3 req-analyzerの出力例

req-analyzerは以下のような質問と整理を行います：

**確認事項**:

| 観点 | 質問 |
|------|------|
| Who | 一般消費者向け？ 会員制？ |
| What | 商品は物理商品？ デジタル商品？ |
| When | 決済はどのタイミング？ 即時？ 後払い？ |
| Where | 国内向け？ 海外対応必要？ |
| Why | 既存ビジネスのEC化？ 新規事業？ |
| How | 決済方法は？ 配送方法は？ |

**質問への回答例**:

```
- 一般消費者向け、会員登録必須
- 物理商品（雑貨）を販売
- クレジットカード決済（Stripe）
- 国内配送のみ
- 新規事業として開始
```

### 4.4 要件定義書の作成

req-analyzerは回答を踏まえて要件を整理します：

```markdown
# 要件定義書: EC Shop

## 機能要件

### 1. ユーザー管理
- FR-001: ユーザー登録（メール/パスワード）
- FR-002: ログイン/ログアウト
- FR-003: パスワードリセット
- FR-004: プロフィール編集

### 2. 商品管理
- FR-010: 商品一覧表示（ページネーション）
- FR-011: 商品カテゴリ絞り込み
- FR-012: 商品検索
- FR-013: 商品詳細表示

### 3. カート管理
- FR-020: カートに商品追加
- FR-021: カート内商品の数量変更
- FR-022: カートから商品削除
- FR-023: カート内容の永続化（ログイン時）

### 4. 注文処理
- FR-030: 配送先入力
- FR-031: 決済処理（Stripe）
- FR-032: 注文確認メール送信
- FR-033: 注文履歴表示

## 非機能要件

- NFR-001: ページロード3秒以内
- NFR-002: 同時100ユーザーを処理可能
- NFR-003: HTTPS必須
- NFR-004: WCAG 2.1 Level AA準拠

## 受け入れ基準

- AC-001: ゲストユーザーが商品をカートに追加し、登録後もカート内容が保持される
- AC-002: 注文完了後、確認メールが5分以内に届く
- AC-003: モバイルデバイスで全機能が利用可能
```

### 4.5 成果物の保存

仕様書を`docs/specs/`に保存するよう指示します：

```
この要件定義書を docs/specs/requirements.md に保存してください
```

---

## 5. Phase 2: 仕様策定

### 5.1 spec-plannerへの引き継ぎ

```
spec-planner として、docs/specs/requirements.md の要件定義書を読み込み、
技術仕様書を策定してください。
```

### 5.2 spec-plannerの出力例

**データモデル設計**:

```markdown
## データモデル

### User（ユーザー）
| フィールド | 型 | 説明 |
|-----------|-----|------|
| id | UUID | 主キー |
| email | String | メールアドレス（一意） |
| passwordHash | String | パスワードハッシュ |
| name | String | 表示名 |
| createdAt | DateTime | 作成日時 |

### Product（商品）
| フィールド | 型 | 説明 |
|-----------|-----|------|
| id | UUID | 主キー |
| name | String | 商品名 |
| description | Text | 説明 |
| price | Decimal | 価格（税込） |
| stock | Integer | 在庫数 |
| categoryId | UUID | カテゴリID |
| images | String[] | 画像URL配列 |

### Cart（カート）
| フィールド | 型 | 説明 |
|-----------|-----|------|
| id | UUID | 主キー |
| userId | UUID | ユーザーID |
| items | CartItem[] | カートアイテム |

### Order（注文）
| フィールド | 型 | 説明 |
|-----------|-----|------|
| id | UUID | 主キー |
| userId | UUID | ユーザーID |
| status | OrderStatus | 注文ステータス |
| totalAmount | Decimal | 合計金額 |
| shippingAddress | JSON | 配送先 |
| createdAt | DateTime | 注文日時 |
```

**API仕様概要**:

```markdown
## API エンドポイント概要

### 認証
| メソッド | パス | 説明 |
|---------|------|------|
| POST | /api/auth/register | ユーザー登録 |
| POST | /api/auth/login | ログイン |
| POST | /api/auth/logout | ログアウト |

### 商品
| メソッド | パス | 説明 |
|---------|------|------|
| GET | /api/products | 商品一覧取得 |
| GET | /api/products/:id | 商品詳細取得 |

### カート
| メソッド | パス | 説明 |
|---------|------|------|
| GET | /api/cart | カート取得 |
| POST | /api/cart/items | 商品追加 |
| PATCH | /api/cart/items/:id | 数量変更 |
| DELETE | /api/cart/items/:id | 商品削除 |

### 注文
| メソッド | パス | 説明 |
|---------|------|------|
| POST | /api/orders | 注文作成 |
| GET | /api/orders | 注文履歴取得 |
| GET | /api/orders/:id | 注文詳細取得 |
```

### 5.3 成果物の保存

```
この技術仕様書を docs/specs/technical-spec.md に保存してください
```

---

## 6. Phase 3: 技術選定

### 6.1 tech-leaderへの引き継ぎ

```
tech-leader として、docs/specs/ の仕様書を読み込み、
このECサイトに最適な技術選定とアーキテクチャ方針を決定してください。
README.md に記載した技術スタックを前提としてください。
```

### 6.2 tech-leaderの出力例

```markdown
## 技術選定ドキュメント

### 採用技術と理由

| カテゴリ | 技術 | 選定理由 |
|---------|------|---------|
| フレームワーク | Next.js 14 | SSR/SSG対応、API Routes一体化、App Router採用 |
| 言語 | TypeScript | 型安全性、開発効率、エラー早期発見 |
| ORM | Prisma | 型安全なDB操作、マイグレーション管理 |
| DB | PostgreSQL | ACID準拠、JSON対応、高い信頼性 |
| 認証 | NextAuth.js | Next.jsとの親和性、複数プロバイダ対応 |
| 決済 | Stripe | 豊富なAPI、Webhook対応、日本円対応 |
| CSS | Tailwind CSS | ユーティリティファースト、高速開発 |

### アーキテクチャ方針

```
┌────────────────────────────────────────────────┐
│                  クライアント                    │
│              (Next.js React)                    │
└────────────────────┬───────────────────────────┘
                     │
                     ▼
┌────────────────────────────────────────────────┐
│              Next.js サーバー                    │
│  ┌──────────────┐  ┌───────────────────────┐   │
│  │ App Router   │  │    API Routes         │   │
│  │ (SSR/SSG)    │  │ (REST API)            │   │
│  └──────────────┘  └───────────────────────┘   │
│                           │                      │
│                           ▼                      │
│               ┌───────────────────┐             │
│               │     Prisma        │             │
│               │  (ORM Layer)      │             │
│               └─────────┬─────────┘             │
└─────────────────────────┼───────────────────────┘
                          │
                          ▼
               ┌───────────────────┐
               │    PostgreSQL     │
               │    (Database)     │
               └───────────────────┘
```

### 設計原則

1. **関心の分離**: UI / ビジネスロジック / データアクセス を分離
2. **型安全性**: TypeScriptの型システムを最大限活用
3. **テスト容易性**: 依存性注入でモック可能な設計
4. **エラーハンドリング**: 一貫したエラー形式、適切なHTTPステータス
```

### 6.3 成果物の保存

```
この技術選定ドキュメントを docs/specs/architecture.md に保存してください
```

---

## 7. Phase 4: 設計

### 7.1 backend-designerによるAPI設計

```
backend-designer として、docs/specs/ の仕様書を読み込み、
詳細なAPI設計とデータベース設計を行ってください。
```

**出力例（API設計）**:

```markdown
## API設計書

### POST /api/auth/register

**リクエスト**:
```json
{
  "email": "user@example.com",
  "password": "password123",
  "name": "山田太郎"
}
```

**レスポンス（成功）**:
```json
{
  "id": "uuid",
  "email": "user@example.com",
  "name": "山田太郎"
}
```

**エラーレスポンス**:
```json
{
  "error": {
    "code": "EMAIL_ALREADY_EXISTS",
    "message": "このメールアドレスは既に登録されています"
  }
}
```

### GET /api/products

**クエリパラメータ**:
| パラメータ | 型 | 説明 |
|-----------|-----|------|
| page | number | ページ番号（デフォルト: 1） |
| limit | number | 取得件数（デフォルト: 20） |
| category | string | カテゴリID |
| q | string | 検索キーワード |

**レスポンス**:
```json
{
  "products": [...],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 100,
    "totalPages": 5
  }
}
```
```

**出力例（Prismaスキーマ）**:

```prisma
// prisma/schema.prisma

generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

model User {
  id           String   @id @default(uuid())
  email        String   @unique
  passwordHash String
  name         String
  createdAt    DateTime @default(now())
  updatedAt    DateTime @updatedAt
  cart         Cart?
  orders       Order[]
}

model Category {
  id       String    @id @default(uuid())
  name     String
  slug     String    @unique
  products Product[]
}

model Product {
  id          String      @id @default(uuid())
  name        String
  description String
  price       Decimal     @db.Decimal(10, 0)
  stock       Int         @default(0)
  images      String[]
  categoryId  String
  category    Category    @relation(fields: [categoryId], references: [id])
  cartItems   CartItem[]
  orderItems  OrderItem[]
  createdAt   DateTime    @default(now())
  updatedAt   DateTime    @updatedAt
}

model Cart {
  id        String     @id @default(uuid())
  userId    String     @unique
  user      User       @relation(fields: [userId], references: [id])
  items     CartItem[]
  updatedAt DateTime   @updatedAt
}

model CartItem {
  id        String  @id @default(uuid())
  cartId    String
  cart      Cart    @relation(fields: [cartId], references: [id])
  productId String
  product   Product @relation(fields: [productId], references: [id])
  quantity  Int

  @@unique([cartId, productId])
}

model Order {
  id              String      @id @default(uuid())
  userId          String
  user            User        @relation(fields: [userId], references: [id])
  status          OrderStatus @default(PENDING)
  totalAmount     Decimal     @db.Decimal(10, 0)
  shippingAddress Json
  items           OrderItem[]
  createdAt       DateTime    @default(now())
  updatedAt       DateTime    @updatedAt
}

model OrderItem {
  id        String  @id @default(uuid())
  orderId   String
  order     Order   @relation(fields: [orderId], references: [id])
  productId String
  product   Product @relation(fields: [productId], references: [id])
  quantity  Int
  price     Decimal @db.Decimal(10, 0)
}

enum OrderStatus {
  PENDING
  PAID
  SHIPPED
  DELIVERED
  CANCELLED
}
```

### 7.2 frontend-designerによるUI設計

```
frontend-designer として、docs/specs/ の仕様書を読み込み、
UI設計とコンポーネント設計を行ってください。
```

**出力例（コンポーネント構成）**:

```markdown
## コンポーネント設計書

### ディレクトリ構成

```
src/components/
├── ui/                    # 汎用UIコンポーネント
│   ├── Button.tsx
│   ├── Input.tsx
│   ├── Card.tsx
│   └── Modal.tsx
├── layout/                # レイアウト
│   ├── Header.tsx
│   ├── Footer.tsx
│   └── Navigation.tsx
├── product/               # 商品関連
│   ├── ProductCard.tsx
│   ├── ProductList.tsx
│   ├── ProductDetail.tsx
│   └── ProductSearch.tsx
├── cart/                  # カート関連
│   ├── CartIcon.tsx
│   ├── CartDrawer.tsx
│   ├── CartItem.tsx
│   └── CartSummary.tsx
├── checkout/              # 決済関連
│   ├── CheckoutForm.tsx
│   ├── AddressForm.tsx
│   └── PaymentForm.tsx
└── auth/                  # 認証関連
    ├── LoginForm.tsx
    ├── RegisterForm.tsx
    └── UserMenu.tsx
```

### 主要コンポーネント仕様

#### ProductCard

```typescript
interface ProductCardProps {
  product: {
    id: string;
    name: string;
    price: number;
    image: string;
  };
  onAddToCart: (productId: string) => void;
}
```

**表示要素**:
- 商品画像（アスペクト比1:1）
- 商品名（最大2行、省略表示）
- 価格（税込、3桁カンマ区切り）
- カート追加ボタン

#### CartDrawer

```typescript
interface CartDrawerProps {
  isOpen: boolean;
  onClose: () => void;
}
```

**表示要素**:
- カート内商品一覧
- 各商品の数量変更UI
- 小計表示
- 「レジに進む」ボタン
```

### 7.3 成果物の保存

```
API設計書を docs/specs/api-design.md に、
コンポーネント設計書を docs/specs/component-design.md に保存してください。
Prismaスキーマは prisma/schema.prisma に直接作成してください。
```

---

## 8. Phase 5: 実装

### 8.1 プロジェクト初期化

まず、Next.jsプロジェクトを初期化します：

```
code-builder として、以下を実行してください：

1. Next.js プロジェクトを src/ ディレクトリに初期化
   - TypeScript, Tailwind CSS, ESLint を有効化
   - App Router を使用

2. 必要なパッケージをインストール
   - prisma, @prisma/client
   - next-auth
   - stripe, @stripe/stripe-js
   - zod（バリデーション用）

3. prisma/schema.prisma を設計書の通り作成

4. 開発環境用の docker-compose.yml を作成（PostgreSQL）
```

### 8.2 基盤実装

```
code-builder として、以下の基盤を実装してください：

1. Prisma クライアントのセットアップ（src/lib/prisma.ts）
2. 共通エラーハンドリング（src/lib/errors.ts）
3. 認証設定（NextAuth.js）
4. 環境変数テンプレート（.env.example）
```

### 8.3 機能実装（商品一覧）

```
code-builder として、docs/specs/api-design.md と
docs/specs/component-design.md を参照し、
商品一覧機能を実装してください：

1. GET /api/products API
2. ProductCard コンポーネント
3. ProductList コンポーネント
4. 商品一覧ページ（src/app/(shop)/products/page.tsx）
```

### 8.4 実装の流れ（code-builderの動作）

code-builderは以下の手順で実装を進めます：

1. **仕様確認**: 設計書を読み込んで実装内容を把握
2. **既存コード確認**: 関連する既存実装を調査
3. **実装**: 設計に従ってコードを記述
4. **テスト作成**: ユニットテストを作成
5. **動作確認**: ビルド・リントが通ることを確認

> **Serena活用ポイント**: code-builderは実装時にSerenaのセマンティック機能を活用します：
> - `get_symbols`: 既存のクラス・関数構造を把握
> - `find_references`: 変更する関数の使用箇所を事前確認
> - `get_diagnostics`: 実装後のエラー・警告を即座に検出
> - `apply_edit`: 関数名変更などのリファクタリングを安全に実行

**実装例（API）**:

```typescript
// src/app/api/products/route.ts
import { NextRequest, NextResponse } from 'next/server';
import { prisma } from '@/lib/prisma';

export async function GET(request: NextRequest) {
  const searchParams = request.nextUrl.searchParams;
  const page = Number(searchParams.get('page')) || 1;
  const limit = Number(searchParams.get('limit')) || 20;
  const category = searchParams.get('category');
  const q = searchParams.get('q');

  const where = {
    ...(category && { categoryId: category }),
    ...(q && {
      OR: [
        { name: { contains: q, mode: 'insensitive' } },
        { description: { contains: q, mode: 'insensitive' } },
      ],
    }),
  };

  const [products, total] = await Promise.all([
    prisma.product.findMany({
      where,
      skip: (page - 1) * limit,
      take: limit,
      include: { category: true },
    }),
    prisma.product.count({ where }),
  ]);

  return NextResponse.json({
    products,
    pagination: {
      page,
      limit,
      total,
      totalPages: Math.ceil(total / limit),
    },
  });
}
```

**実装例（コンポーネント）**:

```typescript
// src/components/product/ProductCard.tsx
'use client';

import Image from 'next/image';
import { formatPrice } from '@/lib/utils';

interface ProductCardProps {
  product: {
    id: string;
    name: string;
    price: number;
    images: string[];
  };
  onAddToCart: (productId: string) => void;
}

export function ProductCard({ product, onAddToCart }: ProductCardProps) {
  return (
    <div className="group relative rounded-lg border p-4 hover:shadow-lg transition-shadow">
      <div className="aspect-square overflow-hidden rounded-md">
        <Image
          src={product.images[0] || '/placeholder.png'}
          alt={product.name}
          width={300}
          height={300}
          className="object-cover group-hover:scale-105 transition-transform"
        />
      </div>
      <div className="mt-4">
        <h3 className="text-sm font-medium line-clamp-2">{product.name}</h3>
        <p className="mt-1 text-lg font-bold">{formatPrice(product.price)}</p>
        <button
          onClick={() => onAddToCart(product.id)}
          className="mt-2 w-full rounded-md bg-blue-600 py-2 text-white hover:bg-blue-700"
        >
          カートに追加
        </button>
      </div>
    </div>
  );
}
```

### 8.5 継続的な実装

同様の手順で、以下の機能を実装していきます：

```
1. 商品詳細機能
2. カート機能
3. ユーザー認証機能
4. 注文機能
```

実装の依頼例：

```
code-builder として、カート機能を実装してください。
設計書（docs/specs/api-design.md, docs/specs/component-design.md）に従い、
以下を実装してください：

1. カートAPI（GET/POST/PATCH/DELETE）
2. CartDrawer コンポーネント
3. CartIcon コンポーネント
4. カート状態管理（Context または Zustand）
```

---

## 9. Phase 6: レビュー

### 9.1 code-reviewerによるレビュー

実装が完了したら、レビューを依頼します：

```
code-reviewer として、今回実装した商品一覧機能をレビューしてください：
- src/app/api/products/route.ts
- src/components/product/ProductCard.tsx
- src/components/product/ProductList.tsx
- src/app/(shop)/products/page.tsx
```

### 9.2 レビュー結果の例

```markdown
## コードレビュー結果

### 概要
商品一覧機能の実装をレビューしました。全体的に良好な実装ですが、いくつかの改善点があります。

### 指摘事項

#### [Major] src/app/api/products/route.ts:15
**問題**: 検索クエリにSQLインジェクションの可能性
**修正案**: Prismaの自動エスケープで安全ですが、入力値のサニタイズを追加推奨

```typescript
// 修正前
const q = searchParams.get('q');

// 修正後
const q = searchParams.get('q')?.trim().slice(0, 100); // 長さ制限追加
```

#### [Minor] src/components/product/ProductCard.tsx:20
**問題**: 画像読み込みエラー時のフォールバック未実装
**修正案**: onErrorハンドラを追加

```typescript
<Image
  src={product.images[0] || '/placeholder.png'}
  alt={product.name}
  onError={(e) => { e.currentTarget.src = '/placeholder.png'; }}
  // ...
/>
```

#### [Suggestion] src/components/product/ProductList.tsx
**提案**: ローディング状態のスケルトンUI追加で UX 向上

### 総評
- セキュリティ: 良好（Prismaで基本的な対策済み）
- パフォーマンス: 良好（ページネーション実装済み）
- コード品質: 良好（型定義、命名規則準拠）
- テストカバレッジ: 要改善（ユニットテスト追加推奨）
```

### 9.3 指摘事項の修正

レビュー結果を基に修正を依頼します：

```
code-builder として、レビューで指摘された以下の修正を行ってください：
1. 検索クエリの長さ制限追加
2. 画像エラー時のフォールバック実装
3. ローディングスケルトンの追加
```

---

## 10. 機能追加演習

### 10.1 お気に入り機能の追加

新機能の追加を一通りの流れで実践してみましょう。

**要求**:
```
ユーザーが商品をお気に入りに追加・削除できる機能を追加したい
```

### 10.2 フローの実行

#### Step 1: 要求分析

```
req-analyzer として、以下の要求を分析してください：
「ユーザーが商品をお気に入りに追加・削除できる機能を追加したい」
既存のEC Shopの機能拡張として分析してください。
```

#### Step 2: 仕様策定

```
spec-planner として、お気に入り機能の技術仕様を策定してください。
既存の docs/specs/ の仕様書と整合性を保ってください。
```

#### Step 3: 設計

```
backend-designer として、お気に入り機能のAPI設計を行ってください。

frontend-designer として、お気に入り機能のUI設計を行ってください。
```

#### Step 4: 実装

```
code-builder として、設計に基づいてお気に入り機能を実装してください：
1. Prismaスキーマへの Favorite モデル追加
2. お気に入りAPI（追加・削除・一覧）
3. お気に入りボタンコンポーネント
4. お気に入り一覧ページ
```

#### Step 5: レビュー

```
code-reviewer として、お気に入り機能の実装をレビューしてください
```

---

## 11. バグ修正演習

### 11.1 バグの発見

開発中に以下のバグが報告されたとします：

```
バグ報告:
- 概要: カートに同じ商品を複数回追加すると、数量が増えずに重複してしまう
- 再現手順:
  1. 商品詳細ページを開く
  2. 「カートに追加」をクリック
  3. 再度「カートに追加」をクリック
  4. カートを確認すると、同じ商品が2行表示される
- 期待動作: 既存商品の数量が1増加する
```

### 11.2 code-debuggerによる調査

```
code-debugger として、以下のバグを調査してください：

「カートに同じ商品を複数回追加すると、数量が増えずに重複してしまう」

再現手順:
1. 商品詳細ページを開く
2. 「カートに追加」をクリック
3. 再度「カートに追加」をクリック
4. カートを確認すると、同じ商品が2行表示される

関連ファイル:
- src/app/api/cart/items/route.ts
- src/components/cart/CartDrawer.tsx
```

### 11.3 デバッグ結果の例

```markdown
## バグ分析レポート

### 調査結果

**根本原因**: `POST /api/cart/items` で既存商品の存在確認を行っていない

**問題のコード** (src/app/api/cart/items/route.ts:15):
```typescript
// 問題: 常に新規作成している
await prisma.cartItem.create({
  data: {
    cartId: cart.id,
    productId,
    quantity,
  },
});
```

### 修正案

```typescript
// 修正後: upsert を使用
await prisma.cartItem.upsert({
  where: {
    cartId_productId: {
      cartId: cart.id,
      productId,
    },
  },
  update: {
    quantity: { increment: quantity },
  },
  create: {
    cartId: cart.id,
    productId,
    quantity,
  },
});
```

### 追加対応

1. Prismaスキーマに複合ユニーク制約を追加（既存なら確認）
2. ユニットテストを追加して再発防止
```

### 11.4 修正の実装

```
code-builder として、バグ分析レポートに基づいて修正を実装してください：
1. カートアイテムAPI の upsert 対応
2. 該当するユニットテストの追加
```

---

## 12. Tips & まとめ

### 12.1 効果的なエージェント活用のコツ

| コツ | 説明 |
|------|------|
| **具体的な指示** | 「いい感じに」ではなく「〜を〜してください」と具体的に |
| **コンテキスト提供** | 関連ファイルや背景情報を明示 |
| **段階的に進める** | 大きなタスクは小さく分割して依頼 |
| **仕様書を活用** | docs/specs/ に仕様を蓄積し、参照させる |
| **レビューを忘れずに** | 実装後は必ず code-reviewer でチェック |

### 12.2 よく使うプロンプトパターン

```markdown
# 新機能実装
「code-builder として、{設計書}に基づいて{機能}を実装してください」

# バグ修正
「code-debugger として、以下のバグを調査してください：{バグ詳細}」

# コード理解
「code-guide として、{ファイル/機能}の実装を説明してください」

# レビュー依頼
「code-reviewer として、{対象ファイル}をレビューしてください」

# 設計相談
「tech-leader として、{課題}に対するアーキテクチャを検討してください」
```

### 12.3 トラブルシューティング

| 問題 | 対処法 |
|------|--------|
| エージェントの出力が期待と違う | プロンプトをより具体的にする / 明示的にエージェントを指定 |
| 処理に時間がかかる | タスクを分割する / 対象ファイルを限定する |
| 既存コードと整合性がない | CLAUDE.md のコーディング規約を更新 / 規約を明示 |
| 生成コードにエラー | code-reviewer でチェック / 段階的に実装 |

### 12.4 Serena MCP の活用

本フレームワークでは、Serena MCP（LSPベースのセマンティックコード操作ツール）がサブエージェントと連携して動作します。

**Serenaが有効な場面**:

| シチュエーション | Serenaの活用 |
|----------------|-------------|
| 関数の定義を探す | `go_to_definition` でピンポイントに特定 |
| リファクタリング | `find_references` で影響範囲を事前確認 |
| 変数の型を確認 | `get_hover_info` で型情報を取得 |
| バグ調査 | `get_call_hierarchy` でコールスタックを分析 |

**設定確認**:

プロジェクトルートに `.mcp.json` が存在し、Serenaが設定されていることを確認してください。

```bash
# Serenaの動作確認
claude
> 「serenaを使ってこのプロジェクトのシンボル一覧を表示して」
```

詳細は [Serena MCP 統合ガイド](./08-serena-integration-guide.md) を参照してください。

### 12.5 開発フロー総まとめ

```
[要求] → req-analyzer → [要件定義書]
                ↓
        spec-planner → [技術仕様書]
                ↓
        tech-leader → [アーキテクチャ方針]
                ↓
        ┌───────────────┐
        │ 並列実行可能    │
        ↓               ↓
frontend-designer  backend-designer
        │               │
        └───────┬───────┘
                ↓
        code-builder → [実装コード]
                ↓
        code-reviewer → [レビュー結果]
                ↓
        (必要に応じて code-debugger)
                ↓
            [完成]
```

### 12.6 次のステップ

このチュートリアルを完了したら、以下にも挑戦してみてください：

1. **カスタムコマンドの活用**: `/spec:init`, `/impl:run` などを使用
2. **並列開発**: 複数のエージェントを同時に動かす
3. **CI/CD連携**: GitHub Actionsでの自動テスト設定
4. **本番デプロイ**: Vercel等へのデプロイフロー構築

---

## 参考リンク

- [サブエージェント詳細ガイド](./05-claude-subagent-guide.md)
- [Serena MCP 統合ガイド](./08-serena-integration-guide.md)
- [カスタムコマンドガイド](./06-custom-commands-guide.md)
- [Command・Skill・Agent使い分けガイド](./07-command-skill-agent-guide.md)

---

*このチュートリアルは clauto-develop フレームワークの実践ガイドです。*
