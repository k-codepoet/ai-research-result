# 통합 스택 가이드: 여러 안 비교

> 작성일: 2025-02-14  
> 기준: Self-Host + 선언형 + AI 친화적  
> 대상: SPA / SSR / SSG

---

## 📋 스택 옵션 요약

| 안 | UI | 메타프레임워크 | 특징 |
|----|-----|---------------|------|
| **A** | Svelte | SvelteKit | 최고의 선언형, 최소 번들 |
| **B** | React | Remix | Web 표준, Self-Host 쉬움 |
| **C** | React | TanStack Start | 타입 안전 극대화, 신규 |
| **D** | React | Vite SPA + Hono | 분리형, 유연함 |
| **E** | React | Astro + Islands | 콘텐츠 중심, 하이브리드 |

---

## 🅰️ 안 A: Svelte 생태계 (기존 추천)

```
┌─────────────────────────────────────────────┐
│  SvelteKit                                  │
│  + Superforms + Zod                         │
│  + Tailwind                                 │
│  + Drizzle + SQLite/PostgreSQL              │
│  + adapter-node                             │
└─────────────────────────────────────────────┘
```

| 항목 | 평가 |
|------|------|
| Self-Host | ⭐⭐⭐⭐⭐ |
| 선언형 | ⭐⭐⭐⭐⭐ |
| AI 친화도 | ⭐⭐⭐⭐ |
| 생태계 | ⭐⭐⭐ |
| 채용 풀 | ⭐⭐ |

**장점:** 번들 최소, 문법 직관적, Self-Host 최적  
**단점:** React 대비 생태계 작음, 라이브러리 선택지 제한

---

## 🅱️ 안 B: React + Remix (균형형)

```
┌─────────────────────────────────────────────┐
│  Remix                                      │
│  + Conform + Zod                            │
│  + Tailwind                                 │
│  + Drizzle + SQLite/PostgreSQL              │
│  + Express adapter                          │
└─────────────────────────────────────────────┘
```

| 항목 | 평가 |
|------|------|
| Self-Host | ⭐⭐⭐⭐⭐ |
| 선언형 | ⭐⭐⭐⭐ |
| AI 친화도 | ⭐⭐⭐⭐ |
| 생태계 | ⭐⭐⭐⭐⭐ |
| 채용 풀 | ⭐⭐⭐⭐ |

### 구조:

```
app/
├── root.tsx                 # 루트 레이아웃
├── routes/
│   ├── _index.tsx          # / 
│   ├── users._index.tsx    # /users
│   ├── users.$id.tsx       # /users/:id
│   └── api.users.tsx       # /api/users
├── components/
└── lib/
    ├── db/
    │   └── schema.ts       # Drizzle 스키마
    └── schemas/
        └── user.ts         # Zod 스키마
```

### 핵심 코드:

```typescript
// app/routes/users._index.tsx
import { json, type LoaderFunctionArgs, type ActionFunctionArgs } from '@remix-run/node';
import { useLoaderData, Form } from '@remix-run/react';
import { parseWithZod } from '@conform-to/zod';
import { db } from '~/lib/db';
import { users } from '~/lib/db/schema';
import { userSchema } from '~/lib/schemas/user';

// 선언형 로더
export const loader = async () => {
  const allUsers = await db.select().from(users);
  return json({ users: allUsers });
};

// 선언형 액션
export const action = async ({ request }: ActionFunctionArgs) => {
  const formData = await request.formData();
  const submission = parseWithZod(formData, { schema: userSchema });
  
  if (submission.status !== 'success') {
    return json({ result: submission.reply() }, { status: 400 });
  }
  
  await db.insert(users).values(submission.value);
  return json({ success: true });
};

// 선언형 UI
export default function UsersPage() {
  const { users } = useLoaderData<typeof loader>();
  
  return (
    <div>
      <h1>Users</h1>
      <ul>
        {users.map(user => (
          <li key={user.id}>{user.name}</li>
        ))}
      </ul>
      
      <Form method="post">
        <input name="name" required />
        <input name="email" type="email" required />
        <button type="submit">Add User</button>
      </Form>
    </div>
  );
}
```

### Conform (선언형 폼):

```typescript
// 선언형 폼 with Zod
import { useForm, getFormProps, getInputProps } from '@conform-to/react';
import { parseWithZod } from '@conform-to/zod';

export default function UserForm() {
  const [form, fields] = useForm({
    onValidate({ formData }) {
      return parseWithZod(formData, { schema: userSchema });
    },
  });

  return (
    <Form method="post" {...getFormProps(form)}>
      <input {...getInputProps(fields.email, { type: 'email' })} />
      {fields.email.errors && <span>{fields.email.errors}</span>}
      
      <input {...getInputProps(fields.name, { type: 'text' })} />
      <button type="submit">Submit</button>
    </Form>
  );
}
```

### Docker:

```dockerfile
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM node:20-alpine
WORKDIR /app
COPY --from=builder /app/build ./build
COPY --from=builder /app/public ./public
COPY --from=builder /app/package*.json ./
RUN npm ci --omit=dev
EXPOSE 3000
CMD ["npm", "start"]
```

**장점:** Web 표준, React 생태계 전체 활용, Shopify가 관리  
**단점:** SSG 미지원, Nested routes 학습 필요

---

## 🅲 안 C: React + TanStack Start (최신)

```
┌─────────────────────────────────────────────┐
│  TanStack Start                             │
│  + TanStack Router (타입 안전 라우팅)        │
│  + TanStack Query (서버 상태)               │
│  + TanStack Form + Zod                      │
│  + Tailwind                                 │
│  + Drizzle + SQLite/PostgreSQL              │
└─────────────────────────────────────────────┘
```

| 항목 | 평가 |
|------|------|
| Self-Host | ⭐⭐⭐⭐ |
| 선언형 | ⭐⭐⭐⭐⭐ |
| AI 친화도 | ⭐⭐⭐ |
| 생태계 | ⭐⭐⭐⭐ (TanStack) |
| 타입 안전 | ⭐⭐⭐⭐⭐ |

### 구조:

```
src/
├── routes/
│   ├── __root.tsx           # 루트 레이아웃
│   ├── index.tsx            # /
│   ├── users/
│   │   ├── index.tsx        # /users
│   │   └── $userId.tsx      # /users/:userId
│   └── api/
│       └── users.ts         # API 라우트
├── components/
└── lib/
```

### 핵심 코드:

```typescript
// src/routes/users/index.tsx
import { createFileRoute } from '@tanstack/react-router';
import { createServerFn } from '@tanstack/start';
import { db } from '~/lib/db';
import { users } from '~/lib/db/schema';

// 서버 함수 선언
const getUsers = createServerFn('GET', async () => {
  return db.select().from(users);
});

const createUser = createServerFn('POST', async (data: UserInput) => {
  const validated = userSchema.parse(data);
  return db.insert(users).values(validated).returning();
});

// 라우트 선언
export const Route = createFileRoute('/users/')({
  loader: () => getUsers(),
  component: UsersPage,
});

function UsersPage() {
  const users = Route.useLoaderData();
  
  return (
    <div>
      {users.map(user => (
        <div key={user.id}>{user.name}</div>
      ))}
    </div>
  );
}
```

### 타입 안전 라우팅:

```typescript
// 파라미터 타입이 자동 추론됨
export const Route = createFileRoute('/users/$userId')({
  parseParams: (params) => ({
    userId: z.coerce.number().parse(params.userId),
  }),
  loader: async ({ params }) => {
    // params.userId는 number 타입으로 추론됨
    return getUser(params.userId);
  },
});

// 링크도 타입 체크
<Link to="/users/$userId" params={{ userId: 123 }}>
  View User
</Link>
```

**장점:** 타입 안전성 최고, TanStack 생태계 통합, 최신 패턴  
**단점:** 아직 beta, AI 학습 데이터 부족, 문서 미완성

---

## 🅳 안 D: React SPA + Hono API (분리형)

```
┌─────────────────────────────────────────────┐
│  Frontend (SPA)              Backend (API)  │
│  ├── React + Vite            ├── Hono       │
│  ├── TanStack Router         ├── Drizzle    │
│  ├── TanStack Query          ├── Zod        │
│  └── Tailwind                └── SQLite     │
└─────────────────────────────────────────────┘
```

| 항목 | 평가 |
|------|------|
| Self-Host | ⭐⭐⭐⭐⭐ |
| 선언형 | ⭐⭐⭐⭐ |
| AI 친화도 | ⭐⭐⭐⭐⭐ |
| 유연성 | ⭐⭐⭐⭐⭐ |
| 복잡도 | 높음 (두 프로젝트) |

### 프로젝트 구조:

```
project/
├── frontend/           # React SPA
│   ├── src/
│   │   ├── routes/
│   │   ├── components/
│   │   └── lib/
│   │       └── api.ts  # API 클라이언트
│   └── package.json
│
├── backend/            # Hono API
│   ├── src/
│   │   ├── routes/
│   │   ├── db/
│   │   │   └── schema.ts
│   │   └── index.ts
│   └── package.json
│
└── shared/             # 공유 타입/스키마
    └── schemas/
        └── user.ts
```

### Backend (Hono):

```typescript
// backend/src/index.ts
import { Hono } from 'hono';
import { cors } from 'hono/cors';
import { zValidator } from '@hono/zod-validator';
import { db } from './db';
import { users } from './db/schema';
import { userSchema } from '@shared/schemas/user';

const app = new Hono();

app.use('*', cors());

// 선언형 라우트
const usersRoutes = new Hono()
  .get('/', async (c) => {
    const allUsers = await db.select().from(users);
    return c.json(allUsers);
  })
  .post('/', zValidator('json', userSchema), async (c) => {
    const data = c.req.valid('json');
    const [user] = await db.insert(users).values(data).returning();
    return c.json(user, 201);
  })
  .get('/:id', async (c) => {
    const id = Number(c.req.param('id'));
    const [user] = await db.select().from(users).where(eq(users.id, id));
    return user ? c.json(user) : c.notFound();
  });

app.route('/api/users', usersRoutes);

export default app;
```

### Frontend (React + TanStack Query):

```typescript
// frontend/src/lib/api.ts
import { hc } from 'hono/client';
import type { AppType } from '@backend/index';

export const client = hc<AppType>('http://localhost:3001');
```

```typescript
// frontend/src/routes/users.tsx
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';
import { client } from '~/lib/api';

export default function UsersPage() {
  const queryClient = useQueryClient();
  
  // 선언형 쿼리
  const { data: users } = useQuery({
    queryKey: ['users'],
    queryFn: () => client.api.users.$get().then(r => r.json()),
  });
  
  // 선언형 뮤테이션
  const createUser = useMutation({
    mutationFn: (data) => client.api.users.$post({ json: data }),
    onSuccess: () => queryClient.invalidateQueries({ queryKey: ['users'] }),
  });
  
  return (
    <div>
      {users?.map(user => <div key={user.id}>{user.name}</div>)}
      <button onClick={() => createUser.mutate({ name: 'New', email: 'new@test.com' })}>
        Add User
      </button>
    </div>
  );
}
```

### RPC 스타일 (Hono RPC):

```typescript
// Hono RPC로 타입 안전 API 호출
// 백엔드 타입을 프론트에서 직접 사용
const res = await client.api.users[':id'].$get({ param: { id: '1' } });
const user = await res.json();
// user 타입이 자동 추론됨
```

**장점:** FE/BE 독립 배포, 기존 백엔드 교체 가능, AI가 잘 이해  
**단점:** 두 프로젝트 관리, 타입 공유 설정 필요

---

## 🅴 안 E: Astro + React Islands (콘텐츠 + 앱)

```
┌─────────────────────────────────────────────┐
│  Astro                                      │
│  + React (client:* islands)                 │
│  + Content Collections                      │
│  + Tailwind                                 │
│  + Drizzle + SQLite (API routes)            │
└─────────────────────────────────────────────┘
```

| 항목 | 평가 |
|------|------|
| Self-Host | ⭐⭐⭐⭐⭐ |
| 선언형 | ⭐⭐⭐⭐⭐ |
| AI 친화도 | ⭐⭐⭐⭐ |
| 성능 | ⭐⭐⭐⭐⭐ |
| 용도 | 콘텐츠 + 대시보드 |

### 구조:

```
src/
├── components/
│   ├── Header.astro           # 정적 컴포넌트
│   └── Dashboard.tsx          # React Island
├── content/
│   └── blog/                  # 마크다운 콘텐츠
├── pages/
│   ├── index.astro            # 정적 홈
│   ├── blog/[slug].astro      # 정적 블로그
│   ├── dashboard.astro        # React 앱 포함
│   └── api/
│       └── users.ts           # API 엔드포인트
└── lib/
    └── db/
```

### React Island:

```astro
---
// src/pages/dashboard.astro
import Layout from '../layouts/Layout.astro';
import Dashboard from '../components/Dashboard.tsx';
---

<Layout title="Dashboard">
  <h1>Dashboard</h1>
  
  <!-- React Island: 이 부분만 하이드레이션 -->
  <Dashboard client:load />
  
  <!-- 또는 viewport에 들어올 때 -->
  <Dashboard client:visible />
</Layout>
```

```tsx
// src/components/Dashboard.tsx
import { useState, useEffect } from 'react';

export default function Dashboard() {
  const [users, setUsers] = useState([]);
  
  useEffect(() => {
    fetch('/api/users').then(r => r.json()).then(setUsers);
  }, []);
  
  return (
    <div className="grid grid-cols-3 gap-4">
      {users.map(user => (
        <div key={user.id} className="p-4 border rounded">
          {user.name}
        </div>
      ))}
    </div>
  );
}
```

### API Route:

```typescript
// src/pages/api/users.ts
import type { APIRoute } from 'astro';
import { db } from '../../lib/db';
import { users } from '../../lib/db/schema';

export const GET: APIRoute = async () => {
  const allUsers = await db.select().from(users);
  return new Response(JSON.stringify(allUsers), {
    headers: { 'Content-Type': 'application/json' },
  });
};

export const POST: APIRoute = async ({ request }) => {
  const data = await request.json();
  const [user] = await db.insert(users).values(data).returning();
  return new Response(JSON.stringify(user), { status: 201 });
};
```

**장점:** 콘텐츠 + 앱 하이브리드, 최고 성능, 점진적 인터랙티브  
**단점:** 복잡한 앱에는 부적합, Islands 간 상태 공유 어려움

---

## 📊 안별 비교 매트릭스

| 기준 | A (Svelte) | B (Remix) | C (TanStack) | D (분리형) | E (Astro) |
|------|------------|-----------|--------------|-----------|-----------|
| Self-Host | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| 선언형 | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| AI 친화도 | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ |
| React 생태계 | ❌ | ✅ | ✅ | ✅ | ✅ |
| 번들 크기 | 최소 | 중간 | 중간 | 중간 | 최소 |
| 학습 곡선 | 중간 | 중간 | 높음 | 중간 | 낮음 |
| 안정성 | 높음 | 높음 | Beta | 높음 | 높음 |
| SPA | ✅ | ✅ | ✅ | ✅ | △ |
| SSR | ✅ | ✅ | ✅ | ❌ | ✅ |
| SSG | ✅ | ❌ | ✅ | ❌ | ✅ |

---

## 🎯 용도별 추천

### "React로 풀스택 SSR 앱"
→ **안 B (Remix)** 또는 **안 C (TanStack Start)**

### "React SPA + 별도 API"
→ **안 D (React + Hono)**

### "콘텐츠 사이트 + React 대시보드"
→ **안 E (Astro + React Islands)**

### "최소 번들, 최고 성능"
→ **안 A (SvelteKit)** 또는 **안 E (Astro)**

### "AI가 가장 잘 생성하는"
→ **안 D (React + Hono)** - 패턴이 명확하고 학습 데이터 풍부

### "타입 안전성 극대화"
→ **안 C (TanStack Start)**

---

## 📁 공통 레이어

모든 안에서 공유하는 선언형 레이어:

### Drizzle Schema

```typescript
// lib/db/schema.ts
import { sqliteTable, text, integer } from 'drizzle-orm/sqlite-core';

export const users = sqliteTable('users', {
  id: integer('id').primaryKey({ autoIncrement: true }),
  email: text('email').unique().notNull(),
  name: text('name').notNull(),
  createdAt: integer('created_at', { mode: 'timestamp' }).default(sql`(unixepoch())`),
});
```

### Zod Schema

```typescript
// lib/schemas/user.ts
import { z } from 'zod';
import { createInsertSchema, createSelectSchema } from 'drizzle-zod';
import { users } from '../db/schema';

// Drizzle에서 Zod 스키마 자동 생성
export const insertUserSchema = createInsertSchema(users, {
  email: z.string().email(),
  name: z.string().min(2).max(100),
});

export const selectUserSchema = createSelectSchema(users);

export type InsertUser = z.infer<typeof insertUserSchema>;
export type User = z.infer<typeof selectUserSchema>;
```

### Docker Compose

```yaml
# docker-compose.yml
services:
  app:
    build: .
    ports:
      - "3000:3000"
    volumes:
      - ./data:/data
    environment:
      - DATABASE_URL=file:/data/app.db
    restart: unless-stopped
```

---

## 다음 단계

1. 한 가지 안을 선택
2. 템플릿 프로젝트 생성
3. 실제 빌드/배포 테스트
4. 성능 벤치마크

어떤 안으로 진행할지 결정하시면, 상세 템플릿을 만들어 드리겠습니다.

---

*각 안은 특정 상황에 최적화되어 있습니다. "최고의 스택"은 없고, "상황에 맞는 스택"이 있습니다.*
