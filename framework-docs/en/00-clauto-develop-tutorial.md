# clauto-develop Tutorial

A practical tutorial for creating a project from a Template repository and developing using subagents, with an EC site as an example.

---

## Table of Contents

1. [Introduction](#1-introduction)
2. [Creating the Project](#2-creating-the-project)
3. [Configuring CLAUDE.md](#3-configuring-claudemd)
4. [Phase 1: Requirements Analysis](#4-phase-1-requirements-analysis)
5. [Phase 2: Specification Planning](#5-phase-2-specification-planning)
6. [Phase 3: Technology Selection](#6-phase-3-technology-selection)
7. [Phase 4: Design](#7-phase-4-design)
8. [Phase 5: Implementation](#8-phase-5-implementation)
9. [Phase 6: Review](#9-phase-6-review)
10. [Feature Addition Exercise](#10-feature-addition-exercise)
11. [Bug Fix Exercise](#11-bug-fix-exercise)
12. [Tips & Summary](#12-tips--summary)

---

## 1. Introduction

### 1.1 What You'll Learn in This Tutorial

- How to create a project from a Template repository
- How to configure CLAUDE.md for your project
- Practical development flow using subagents
- How to use different agents for each phase

### 1.2 Overview of the EC Site We'll Build

We will build a simple EC site.

**Main Features**:
- Product list display
- Product detail display
- Shopping cart
- Order processing
- User authentication

**Technology Stack** (example for this tutorial):
- Frontend: Next.js + TypeScript
- Backend: Next.js API Routes
- Database: PostgreSQL + Prisma
- Styling: Tailwind CSS

> The technology stack can be changed according to the project.

### 1.3 Prerequisites

- GitHub account
- Node.js (v18 or higher)
- Claude Code CLI (installed)
- uv (Python package manager, for Serena)
- Basic web development knowledge

> **Serena MCP**: This framework utilizes Serena (an LSP-based semantic code operation tool). The Template repository includes `.mcp.json`, which automatically enables Serena.

---

## 2. Creating the Project

### 2.1 Create a Repository from the Template

1. **Create a repository from the template on GitHub**

   Click the "Use this template" button on the clauto-develop repository page

   ```
   https://github.com/[owner]/clauto-develop
   ```

   - Select "Create a new repository"
   - Repository name: `ec-shop` (any name)
   - Click "Create repository"

2. **Clone locally**

   ```bash
   git clone https://github.com/[your-username]/ec-shop.git
   cd ec-shop
   ```

3. **Delete unnecessary files (optional)**

   If you don't need the framework documentation:

   ```bash
   rm -rf framework-docs/
   ```

### 2.2 Verify Directory Structure

```
ec-shop/
├── .claude/
│   └── agents/              # Subagent definitions (10 types)
├── .github/                 # GitHub templates
├── .mcp.json                # Serena MCP configuration (auto-enabled)
├── docs/
│   └── specs/               # Place specifications here
├── src/                     # Source code
├── tests/                   # Tests
├── CLAUDE.md                # ← Edit this
└── README.md
```

---

## 3. Configuring CLAUDE.md

### 3.1 Edit Project Information

Open `CLAUDE.md` and edit it for the EC site.

**Before editing**:
```markdown
# Project: [Project Name]

## Overview

[Brief description of the project (1-2 sentences)]
```

**After editing**:
```markdown
# Project: EC Shop

## Overview

A simple EC site. Provides product browsing, cart management, and order functionality.
```

### 3.2 Configure Technology Stack

```markdown
## Technology Stack

| Category | Technology |
|----------|------------|
| Frontend | Next.js 14 (App Router) + TypeScript |
| Backend | Next.js API Routes |
| Database | PostgreSQL + Prisma |
| Infrastructure | Docker (development environment) |
| Testing | Jest + React Testing Library |
```

### 3.3 Configure Project Structure

```markdown
## Project Structure

```
ec-shop/
├── src/
│   ├── app/              # Next.js App Router
│   │   ├── (shop)/       # Shop-related pages
│   │   ├── api/          # API Routes
│   │   └── layout.tsx
│   ├── components/       # UI components
│   ├── lib/              # Utilities
│   └── types/            # Type definitions
├── prisma/               # Prisma schema
├── tests/                # Test files
└── docs/specs/           # Specifications
```
```

### 3.4 Configure Commonly Used Commands

```markdown
## Commonly Used Commands

```bash
# Development
npm run dev              # Start development server
npm run build            # Build
npm run lint             # Lint

# Testing
npm test                 # Run tests
npm run test:watch       # Watch mode
npm run test:cov         # Coverage

# Database
npm run db:migrate       # Run migrations
npm run db:seed          # Seed data
npm run db:studio        # Start Prisma Studio
```
```

### 3.5 Start Claude Code

Once configuration is complete, start Claude Code.

```bash
claude
```

---

## 4. Phase 1: Requirements Analysis

### 4.1 Starting with Vague Requirements

You (the user) make the following request to Claude Code:

```
I want to create a simple EC site.
I want users to be able to view products, add them to cart, and purchase.
```

### 4.2 Analysis by req-analyzer

Request analysis from Claude Code:

```
As req-analyzer, please analyze the following request:
"I want to create a simple EC site. I want users to be able to view products, add them to cart, and purchase."
```

### 4.3 Example Output from req-analyzer

req-analyzer asks questions and organizes the requirements:

**Clarification Questions**:

| Perspective | Question |
|-------------|----------|
| Who | For general consumers? Members only? |
| What | Physical products? Digital products? |
| When | When does payment occur? Immediate? Deferred? |
| Where | Domestic only? International support needed? |
| Why | EC conversion of existing business? New venture? |
| How | Payment methods? Shipping methods? |

**Example Answers**:

```
- For general consumers, registration required
- Selling physical products (miscellaneous goods)
- Credit card payment (Stripe)
- Domestic shipping only
- Starting as a new business
```

### 4.4 Creating the Requirements Definition Document

req-analyzer organizes requirements based on the answers:

```markdown
# Requirements Definition: EC Shop

## Functional Requirements

### 1. User Management
- FR-001: User registration (email/password)
- FR-002: Login/logout
- FR-003: Password reset
- FR-004: Profile editing

### 2. Product Management
- FR-010: Product list display (pagination)
- FR-011: Product category filtering
- FR-012: Product search
- FR-013: Product detail display

### 3. Cart Management
- FR-020: Add product to cart
- FR-021: Change quantity in cart
- FR-022: Remove product from cart
- FR-023: Persist cart contents (when logged in)

### 4. Order Processing
- FR-030: Shipping address input
- FR-031: Payment processing (Stripe)
- FR-032: Order confirmation email
- FR-033: Order history display

## Non-Functional Requirements

- NFR-001: Page load within 3 seconds
- NFR-002: Handle 100 concurrent users
- NFR-003: HTTPS required
- NFR-004: WCAG 2.1 Level AA compliance

## Acceptance Criteria

- AC-001: Guest user adds product to cart, cart contents persist after registration
- AC-002: Confirmation email arrives within 5 minutes after order completion
- AC-003: All functions available on mobile devices
```

### 4.5 Saving the Deliverable

Instruct to save the specification to `docs/specs/`:

```
Please save this requirements definition document to docs/specs/requirements.md
```

---

## 5. Phase 2: Specification Planning

### 5.1 Handoff to spec-planner

```
As spec-planner, please read the requirements definition in docs/specs/requirements.md
and create technical specifications.
```

### 5.2 Example Output from spec-planner

**Data Model Design**:

```markdown
## Data Model

### User
| Field | Type | Description |
|-------|------|-------------|
| id | UUID | Primary key |
| email | String | Email address (unique) |
| passwordHash | String | Password hash |
| name | String | Display name |
| createdAt | DateTime | Creation date |

### Product
| Field | Type | Description |
|-------|------|-------------|
| id | UUID | Primary key |
| name | String | Product name |
| description | Text | Description |
| price | Decimal | Price (tax included) |
| stock | Integer | Stock quantity |
| categoryId | UUID | Category ID |
| images | String[] | Image URL array |

### Cart
| Field | Type | Description |
|-------|------|-------------|
| id | UUID | Primary key |
| userId | UUID | User ID |
| items | CartItem[] | Cart items |

### Order
| Field | Type | Description |
|-------|------|-------------|
| id | UUID | Primary key |
| userId | UUID | User ID |
| status | OrderStatus | Order status |
| totalAmount | Decimal | Total amount |
| shippingAddress | JSON | Shipping address |
| createdAt | DateTime | Order date |
```

**API Specification Overview**:

```markdown
## API Endpoint Overview

### Authentication
| Method | Path | Description |
|--------|------|-------------|
| POST | /api/auth/register | User registration |
| POST | /api/auth/login | Login |
| POST | /api/auth/logout | Logout |

### Products
| Method | Path | Description |
|--------|------|-------------|
| GET | /api/products | Get product list |
| GET | /api/products/:id | Get product detail |

### Cart
| Method | Path | Description |
|--------|------|-------------|
| GET | /api/cart | Get cart |
| POST | /api/cart/items | Add product |
| PATCH | /api/cart/items/:id | Change quantity |
| DELETE | /api/cart/items/:id | Remove product |

### Orders
| Method | Path | Description |
|--------|------|-------------|
| POST | /api/orders | Create order |
| GET | /api/orders | Get order history |
| GET | /api/orders/:id | Get order detail |
```

### 5.3 Saving the Deliverable

```
Please save this technical specification to docs/specs/technical-spec.md
```

---

## 6. Phase 3: Technology Selection

### 6.1 Handoff to tech-leader

```
As tech-leader, please read the specifications in docs/specs/
and determine the optimal technology selection and architecture policy for this EC site.
Please base your decisions on the technology stack listed in CLAUDE.md.
```

### 6.2 Example Output from tech-leader

```markdown
## Technology Selection Document

### Adopted Technologies and Rationale

| Category | Technology | Selection Rationale |
|----------|------------|---------------------|
| Framework | Next.js 14 | SSR/SSG support, integrated API Routes, App Router adoption |
| Language | TypeScript | Type safety, development efficiency, early error detection |
| ORM | Prisma | Type-safe DB operations, migration management |
| DB | PostgreSQL | ACID compliance, JSON support, high reliability |
| Auth | NextAuth.js | Compatibility with Next.js, multiple provider support |
| Payment | Stripe | Rich API, Webhook support, JPY support |
| CSS | Tailwind CSS | Utility-first, rapid development |

### Architecture Policy

```
┌────────────────────────────────────────────────┐
│                    Client                       │
│              (Next.js React)                    │
└────────────────────┬───────────────────────────┘
                     │
                     ▼
┌────────────────────────────────────────────────┐
│              Next.js Server                     │
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

### Design Principles

1. **Separation of Concerns**: Separate UI / business logic / data access
2. **Type Safety**: Maximize TypeScript's type system
3. **Testability**: Mockable design through dependency injection
4. **Error Handling**: Consistent error format, appropriate HTTP status codes
```

### 6.3 Saving the Deliverable

```
Please save this technology selection document to docs/specs/architecture.md
```

---

## 7. Phase 4: Design

### 7.1 API Design by backend-designer

```
As backend-designer, please read the specifications in docs/specs/
and perform detailed API design and database design.
```

**Example Output (API Design)**:

```markdown
## API Design Document

### POST /api/auth/register

**Request**:
```json
{
  "email": "user@example.com",
  "password": "password123",
  "name": "John Doe"
}
```

**Response (Success)**:
```json
{
  "id": "uuid",
  "email": "user@example.com",
  "name": "John Doe"
}
```

**Error Response**:
```json
{
  "error": {
    "code": "EMAIL_ALREADY_EXISTS",
    "message": "This email address is already registered"
  }
}
```

### GET /api/products

**Query Parameters**:
| Parameter | Type | Description |
|-----------|------|-------------|
| page | number | Page number (default: 1) |
| limit | number | Number of items (default: 20) |
| category | string | Category ID |
| q | string | Search keyword |

**Response**:
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

**Example Output (Prisma Schema)**:

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

### 7.2 UI Design by frontend-designer

```
As frontend-designer, please read the specifications in docs/specs/
and perform UI design and component design.
```

**Example Output (Component Structure)**:

```markdown
## Component Design Document

### Directory Structure

```
src/components/
├── ui/                    # Generic UI components
│   ├── Button.tsx
│   ├── Input.tsx
│   ├── Card.tsx
│   └── Modal.tsx
├── layout/                # Layout
│   ├── Header.tsx
│   ├── Footer.tsx
│   └── Navigation.tsx
├── product/               # Product-related
│   ├── ProductCard.tsx
│   ├── ProductList.tsx
│   ├── ProductDetail.tsx
│   └── ProductSearch.tsx
├── cart/                  # Cart-related
│   ├── CartIcon.tsx
│   ├── CartDrawer.tsx
│   ├── CartItem.tsx
│   └── CartSummary.tsx
├── checkout/              # Checkout-related
│   ├── CheckoutForm.tsx
│   ├── AddressForm.tsx
│   └── PaymentForm.tsx
└── auth/                  # Auth-related
    ├── LoginForm.tsx
    ├── RegisterForm.tsx
    └── UserMenu.tsx
```

### Main Component Specifications

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

**Display Elements**:
- Product image (aspect ratio 1:1)
- Product name (max 2 lines, truncated)
- Price (tax included, thousands separator)
- Add to cart button

#### CartDrawer

```typescript
interface CartDrawerProps {
  isOpen: boolean;
  onClose: () => void;
}
```

**Display Elements**:
- List of products in cart
- Quantity change UI for each product
- Subtotal display
- "Proceed to checkout" button
```

### 7.3 Saving the Deliverables

```
Please save the API design document to docs/specs/api-design.md,
the component design document to docs/specs/component-design.md.
Please create the Prisma schema directly at prisma/schema.prisma.
```

---

## 8. Phase 5: Implementation

### 8.1 Project Initialization

First, initialize the Next.js project:

```
As code-builder, please execute the following:

1. Initialize Next.js project in src/ directory
   - Enable TypeScript, Tailwind CSS, ESLint
   - Use App Router

2. Install required packages
   - prisma, @prisma/client
   - next-auth
   - stripe, @stripe/stripe-js
   - zod (for validation)

3. Create prisma/schema.prisma according to the design document

4. Create docker-compose.yml for development environment (PostgreSQL)
```

### 8.2 Foundation Implementation

```
As code-builder, please implement the following foundation:

1. Prisma client setup (src/lib/prisma.ts)
2. Common error handling (src/lib/errors.ts)
3. Authentication configuration (NextAuth.js)
4. Environment variable template (.env.example)
```

### 8.3 Feature Implementation (Product List)

```
As code-builder, please refer to docs/specs/api-design.md and
docs/specs/component-design.md, and implement the product list feature:

1. GET /api/products API
2. ProductCard component
3. ProductList component
4. Product list page (src/app/(shop)/products/page.tsx)
```

### 8.4 Implementation Flow (code-builder behavior)

code-builder proceeds with implementation in the following order:

1. **Specification Review**: Understand the implementation content by reading design documents
2. **Existing Code Review**: Investigate related existing implementations
3. **Implementation**: Write code according to specifications
4. **Test Creation**: Create unit tests
5. **Verification**: Confirm build and lint pass

> **Serena Usage Tips**: code-builder utilizes Serena's semantic features during implementation:
> - `get_symbols`: Understand existing class/function structures
> - `find_references`: Pre-check usage locations of functions to be changed
> - `get_diagnostics`: Immediately detect errors/warnings after implementation
> - `apply_edit`: Safely execute refactoring like function name changes

**Implementation Example (API)**:

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

**Implementation Example (Component)**:

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
          Add to Cart
        </button>
      </div>
    </div>
  );
}
```

### 8.5 Continuous Implementation

Implement the following features using the same process:

```
1. Product detail feature
2. Cart feature
3. User authentication feature
4. Order feature
```

Example implementation request:

```
As code-builder, please implement the cart feature.
Following the design documents (docs/specs/api-design.md, docs/specs/component-design.md),
please implement the following:

1. Cart API (GET/POST/PATCH/DELETE)
2. CartDrawer component
3. CartIcon component
4. Cart state management (Context or Zustand)
```

---

## 9. Phase 6: Review

### 9.1 Review by code-reviewer

Once implementation is complete, request a review:

```
As code-reviewer, please review the product list feature implemented this time:
- src/app/api/products/route.ts
- src/components/product/ProductCard.tsx
- src/components/product/ProductList.tsx
- src/app/(shop)/products/page.tsx
```

### 9.2 Example Review Results

```markdown
## Code Review Results

### Overview
Reviewed the product list feature implementation. Overall good implementation, but there are several improvement points.

### Issues

#### [Major] src/app/api/products/route.ts:15
**Problem**: Possible SQL injection in search query
**Fix Suggestion**: Safe with Prisma's automatic escaping, but recommend adding input sanitization

```typescript
// Before
const q = searchParams.get('q');

// After
const q = searchParams.get('q')?.trim().slice(0, 100); // Add length limit
```

#### [Minor] src/components/product/ProductCard.tsx:20
**Problem**: No fallback for image loading errors
**Fix Suggestion**: Add onError handler

```typescript
<Image
  src={product.images[0] || '/placeholder.png'}
  alt={product.name}
  onError={(e) => { e.currentTarget.src = '/placeholder.png'; }}
  // ...
/>
```

#### [Suggestion] src/components/product/ProductList.tsx
**Proposal**: Add skeleton UI for loading state to improve UX

### Summary
- Security: Good (basic protection with Prisma)
- Performance: Good (pagination implemented)
- Code Quality: Good (type definitions, naming conventions followed)
- Test Coverage: Needs improvement (recommend adding unit tests)
```

### 9.3 Fixing Issues

Request fixes based on review results:

```
As code-builder, please make the following fixes pointed out in the review:
1. Add length limit to search query
2. Implement fallback for image errors
3. Add loading skeleton
```

---

## 10. Feature Addition Exercise

### 10.1 Adding Favorites Feature

Let's practice adding a new feature through the complete flow.

**Request**:
```
I want to add a feature for users to add/remove products from favorites
```

### 10.2 Executing the Flow

#### Step 1: Requirements Analysis

```
As req-analyzer, please analyze the following request:
"I want to add a feature for users to add/remove products from favorites"
Please analyze as an extension to the existing EC Shop functionality.
```

#### Step 2: Specification Planning

```
As spec-planner, please create technical specifications for the favorites feature.
Please maintain consistency with existing specifications in docs/specs/.
```

#### Step 3: Design

```
As backend-designer, please design the API for the favorites feature.

As frontend-designer, please design the UI for the favorites feature.
```

#### Step 4: Implementation

```
As code-builder, please implement the favorites feature based on the design:
1. Add Favorite model to Prisma schema
2. Favorites API (add/remove/list)
3. Favorite button component
4. Favorites list page
```

#### Step 5: Review

```
As code-reviewer, please review the favorites feature implementation
```

---

## 11. Bug Fix Exercise

### 11.1 Bug Discovery

Suppose the following bug is reported during development:

```
Bug Report:
- Summary: When adding the same product to cart multiple times, it duplicates instead of increasing quantity
- Steps to reproduce:
  1. Open product detail page
  2. Click "Add to Cart"
  3. Click "Add to Cart" again
  4. Check cart - same product appears in 2 rows
- Expected behavior: Quantity of existing product increases by 1
```

### 11.2 Investigation by code-debugger

```
As code-debugger, please investigate the following bug:

"When adding the same product to cart multiple times, it duplicates instead of increasing quantity"

Steps to reproduce:
1. Open product detail page
2. Click "Add to Cart"
3. Click "Add to Cart" again
4. Check cart - same product appears in 2 rows

Related files:
- src/app/api/cart/items/route.ts
- src/components/cart/CartDrawer.tsx
```

### 11.3 Example Debug Results

```markdown
## Bug Analysis Report

### Investigation Results

**Root Cause**: `POST /api/cart/items` doesn't check for existing product

**Problematic Code** (src/app/api/cart/items/route.ts:15):
```typescript
// Problem: Always creates new entry
await prisma.cartItem.create({
  data: {
    cartId: cart.id,
    productId,
    quantity,
  },
});
```

### Fix Suggestion

```typescript
// Fixed: Use upsert
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

### Additional Actions

1. Add compound unique constraint to Prisma schema (verify if existing)
2. Add unit tests to prevent recurrence
```

### 11.4 Implementing the Fix

```
As code-builder, please implement fixes based on the bug analysis report:
1. Update cart item API to use upsert
2. Add relevant unit tests
```

---

## 12. Tips & Summary

### 12.1 Tips for Effective Agent Usage

| Tip | Description |
|-----|-------------|
| **Specific instructions** | Not "make it nice" but "please do X to Y" specifically |
| **Provide context** | Explicitly state related files and background information |
| **Progress step by step** | Break large tasks into smaller requests |
| **Use specifications** | Accumulate specifications in docs/specs/ and reference them |
| **Don't forget reviews** | Always check with code-reviewer after implementation |

### 12.2 Common Prompt Patterns

```markdown
# New feature implementation
"As code-builder, please implement {feature} based on {design document}"

# Bug fix
"As code-debugger, please investigate the following bug: {bug details}"

# Code understanding
"As code-guide, please explain the implementation of {file/feature}"

# Review request
"As code-reviewer, please review {target files}"

# Design consultation
"As tech-leader, please consider architecture for {issue}"
```

### 12.3 Troubleshooting

| Problem | Solution |
|---------|----------|
| Agent output differs from expectation | Make prompt more specific / explicitly specify agent |
| Processing takes time | Split tasks / limit target files |
| Inconsistent with existing code | Update conventions in CLAUDE.md / explicitly state coding standards |
| Generated code has errors | Check with code-reviewer / implement incrementally |

### 12.4 Using Serena MCP

This framework uses Serena MCP (LSP-based semantic code operation tool) in conjunction with subagents.

**Where Serena is Effective**:

| Situation | Serena Usage |
|-----------|--------------|
| Finding function definitions | Pinpoint with `go_to_definition` |
| Refactoring | Pre-check impact scope with `find_references` |
| Checking variable types | Get type info with `get_hover_info` |
| Bug investigation | Analyze call stack with `get_call_hierarchy` |

**Configuration Check**:

Verify that `.mcp.json` exists in the project root and Serena is configured.

```bash
# Verify Serena operation
claude
> "Use serena to display the symbol list for this project"
```

For details, see [Serena MCP Integration Guide](./08-serena-integration-guide.md).

### 12.5 Development Flow Summary

```
[Request] → req-analyzer → [Requirements Definition]
                ↓
        spec-planner → [Technical Specification]
                ↓
        tech-leader → [Architecture Policy]
                ↓
        ┌───────────────┐
        │ Can run in    │
        │ parallel      │
        ↓               ↓
frontend-designer  backend-designer
        │               │
        └───────┬───────┘
                ↓
        code-builder → [Implementation Code]
                ↓
        code-reviewer → [Review Results]
                ↓
        (code-debugger if needed)
                ↓
            [Complete]
```

### 12.6 Next Steps

After completing this tutorial, try the following:

1. **Use Custom Commands**: Use `/spec:init`, `/impl:run`, etc.
2. **Parallel Development**: Run multiple agents simultaneously
3. **CI/CD Integration**: Set up automated testing with GitHub Actions
4. **Production Deployment**: Build deployment flow to Vercel etc.

---

## Reference Links

- [Subagent Detailed Guide](./05-claude-subagent-guide.md)
- [Serena MCP Integration Guide](./08-serena-integration-guide.md)
- [Custom Commands Guide](./06-custom-commands-guide.md)
- [Command/Skill/Agent Usage Guide](./07-command-skill-agent-guide.md)

---

*This tutorial is a practical guide for the clauto-develop framework.*
