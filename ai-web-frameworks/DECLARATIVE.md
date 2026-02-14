# 선언형(Declarative) 스택 리서치

> 작성일: 2025-02-14  
> 관점: Self-Host + 선언형 + AI 친화적

---

## 선언형이란?

```
명령형(Imperative): "어떻게" 할지 단계별로 지시
선언형(Declarative): "무엇을" 원하는지 선언 → 시스템이 알아서 처리
```

**왜 AI에게 유리한가:**
- 의도가 명확 → AI가 이해하기 쉬움
- 보일러플레이트 감소 → 생성 코드가 깔끔
- 타입/스키마 중심 → 구조적 일관성 유지

---

## 1. Drizzle ORM 마이그레이션 상세

### ✅ 예, 선언형 마이그레이션 지원합니다

Drizzle은 **스키마 파일이 Single Source of Truth**입니다.

### 작동 방식:

```
schema.ts (선언) → drizzle-kit → SQL 마이그레이션 파일 → DB
```

### 명령어:

```bash
# 1. 스키마 → 마이그레이션 SQL 생성
npx drizzle-kit generate

# 2. 마이그레이션 적용
npx drizzle-kit migrate

# 3. (개발용) 스키마를 DB에 직접 푸시 (마이그레이션 파일 없이)
npx drizzle-kit push
```

### 스키마 예시 (선언형):

```typescript
// src/db/schema.ts
import { sqliteTable, text, integer } from 'drizzle-orm/sqlite-core';

// 이것이 "선언"입니다 - 테이블이 어떤 모양이어야 하는지
export const users = sqliteTable('users', {
  id: integer('id').primaryKey({ autoIncrement: true }),
  email: text('email').unique().notNull(),
  name: text('name'),
  role: text('role', { enum: ['admin', 'user'] }).default('user'),
  createdAt: integer('created_at', { mode: 'timestamp' })
    .notNull()
    .default(sql`(unixepoch())`),
});

// 관계도 선언형
export const posts = sqliteTable('posts', {
  id: integer('id').primaryKey({ autoIncrement: true }),
  title: text('title').notNull(),
  authorId: integer('author_id')
    .notNull()
    .references(() => users.id, { onDelete: 'cascade' }),
});
```

### 생성된 마이그레이션:

```sql
-- drizzle/0001_create_users.sql
CREATE TABLE `users` (
  `id` integer PRIMARY KEY AUTOINCREMENT,
  `email` text NOT NULL UNIQUE,
  `name` text,
  `role` text DEFAULT 'user',
  `created_at` integer NOT NULL DEFAULT (unixepoch())
);
```

### drizzle.config.ts:

```typescript
import { defineConfig } from 'drizzle-kit';

export default defineConfig({
  schema: './src/db/schema.ts',
  out: './drizzle',
  dialect: 'sqlite',
  dbCredentials: {
    url: './data/app.db',
  },
});
```

### Drizzle vs Prisma 마이그레이션 비교:

| 항목 | Drizzle | Prisma |
|------|---------|--------|
| 스키마 언어 | TypeScript | Prisma DSL |
| 마이그레이션 | SQL 파일 | SQL 파일 |
| 선언형 | ✅ | ✅ |
| 자동 생성 | ✅ | ✅ |
| 커스텀 SQL | 쉬움 | 가능하지만 복잡 |
| 롤백 | 수동 | 수동 |
| AI 친화도 | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |

---

## 2. 선언형 스택 종합 분석

### 📊 선언형 수준 비교

| 레이어 | 도구 | 선언형 수준 | AI 친화도 | Self-Host |
|--------|------|------------|----------|-----------|
| **UI** | Svelte | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ✅✅ |
| **UI** | React (JSX) | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ✅✅ |
| **UI** | Vue SFC | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ✅✅ |
| **스타일** | Tailwind | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ✅✅ |
| **스타일** | UnoCSS | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ✅✅ |
| **라우팅** | 파일 기반 | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ✅✅ |
| **상태** | Zustand | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ✅✅ |
| **상태** | Svelte Store | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ✅✅ |
| **폼** | Zod + 스키마 | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ✅✅ |
| **ORM** | Drizzle | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ✅✅ |
| **ORM** | Prisma | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ✅ |
| **인프라** | Docker Compose | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ✅✅ |
| **배포** | Kamal | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | ✅✅ |

---

## 3. SPA 선언형 스택

### 🎯 추천: Svelte 5 + TanStack Router + Drizzle

```
┌─────────────────────────────────────────────┐
│  Svelte 5 (Runes)                           │  ← 선언형 반응성
│  + TanStack Router                          │  ← 타입 안전 라우팅
│  + Tailwind / UnoCSS                        │  ← 선언형 스타일
│  + Zod                                      │  ← 선언형 검증
│  + Hono (API)                               │  ← 경량 백엔드
│  + Drizzle + SQLite                         │  ← 선언형 스키마
└─────────────────────────────────────────────┘
```

### Svelte 5 Runes (최신 선언형 반응성):

```svelte
<script>
  // 선언형 반응성 - "이 값이 변하면 UI가 업데이트되어야 한다"
  let count = $state(0);
  let doubled = $derived(count * 2);
  
  // 선언형 효과
  $effect(() => {
    console.log(`Count is now ${count}`);
  });
</script>

<button onclick={() => count++}>
  {count} × 2 = {doubled}
</button>
```

### TanStack Router (타입 안전 선언형 라우팅):

```typescript
// routes.ts - 라우트를 "선언"
import { createFileRoute } from '@tanstack/react-router';

export const Route = createFileRoute('/users/$userId')({
  // 파라미터 스키마 선언
  parseParams: (params) => ({
    userId: z.number().int().parse(Number(params.userId)),
  }),
  // 로더 선언
  loader: ({ params }) => fetchUser(params.userId),
  // 컴포넌트 선언
  component: UserPage,
});
```

### 대안: React + Vite + TanStack

```
React 19 + Vite + TanStack Router + TanStack Query
+ Tailwind + Zod + Hono + Drizzle
```

**AI 친화도**: React가 더 높음 (학습 데이터 많음)  
**선언형 수준**: Svelte가 더 높음 (언어 레벨 반응성)

---

## 4. SSR 선언형 스택

### 🎯 추천: SvelteKit + Drizzle + Zod

```
┌─────────────────────────────────────────────┐
│  SvelteKit                                  │
│  ├── +page.svelte      (선언형 UI)          │
│  ├── +page.server.ts   (선언형 데이터 로딩)  │
│  ├── +layout.svelte    (선언형 레이아웃)     │
│  └── hooks.server.ts   (선언형 미들웨어)     │
│                                             │
│  + Superforms + Zod    (선언형 폼 검증)      │
│  + Drizzle + PostgreSQL                     │
│  + Tailwind                                 │
└─────────────────────────────────────────────┘
```

### SvelteKit의 선언형 구조:

```
src/routes/
├── +layout.svelte          # 전역 레이아웃 "선언"
├── +page.svelte            # 홈 페이지 "선언"
├── users/
│   ├── +page.svelte        # /users UI "선언"
│   ├── +page.server.ts     # 데이터 로딩 "선언"
│   └── [id]/
│       ├── +page.svelte    # /users/:id UI
│       └── +page.server.ts # 파라미터 기반 로딩
```

### +page.server.ts (선언형 서버 로직):

```typescript
// src/routes/users/+page.server.ts
import { db } from '$lib/db';
import { users } from '$lib/db/schema';

// 데이터 로딩 "선언"
export const load = async () => {
  const allUsers = await db.select().from(users);
  return { users: allUsers };
};

// 액션 "선언"
export const actions = {
  create: async ({ request }) => {
    const data = await request.formData();
    const validated = userSchema.parse(Object.fromEntries(data));
    await db.insert(users).values(validated);
    return { success: true };
  },
  delete: async ({ request }) => {
    const data = await request.formData();
    const id = Number(data.get('id'));
    await db.delete(users).where(eq(users.id, id));
    return { success: true };
  },
};
```

### Superforms + Zod (선언형 폼):

```typescript
// 스키마 선언
const userSchema = z.object({
  email: z.string().email(),
  name: z.string().min(2).max(100),
  role: z.enum(['admin', 'user']),
});

// 폼 선언 (자동 검증, 에러 처리)
const { form, errors, enhance } = superForm(data.form, {
  validators: zod(userSchema),
});
```

```svelte
<form method="POST" action="?/create" use:enhance>
  <input name="email" bind:value={$form.email} />
  {#if $errors.email}<span class="error">{$errors.email}</span>{/if}
  
  <input name="name" bind:value={$form.name} />
  <button type="submit">Create</button>
</form>
```

### 대안: Remix + Conform + Zod

```typescript
// Remix의 선언형 구조
export const loader = async ({ params }) => {
  return json(await getUser(params.id));
};

export const action = async ({ request }) => {
  const formData = await request.formData();
  const submission = parseWithZod(formData, { schema: userSchema });
  // ...
};
```

---

## 5. SSG 선언형 스택

### 🎯 추천: Astro + Content Collections + MDX

```
┌─────────────────────────────────────────────┐
│  Astro                                      │
│  ├── astro.config.mjs   (선언형 설정)        │
│  ├── content/config.ts  (선언형 콘텐츠 스키마)│
│  ├── content/blog/      (마크다운 콘텐츠)     │
│  └── pages/             (선언형 라우팅)       │
│                                             │
│  + Zod (콘텐츠 스키마)                       │
│  + Tailwind / UnoCSS                        │
│  + 필요시 React/Svelte Islands              │
└─────────────────────────────────────────────┘
```

### Content Collections (선언형 콘텐츠):

```typescript
// src/content/config.ts
import { defineCollection, z } from 'astro:content';

// 블로그 콘텐츠 스키마 "선언"
const blogCollection = defineCollection({
  type: 'content',
  schema: z.object({
    title: z.string(),
    pubDate: z.date(),
    author: z.string(),
    tags: z.array(z.string()),
    draft: z.boolean().default(false),
    image: z.string().optional(),
  }),
});

// 문서 콘텐츠 스키마 "선언"
const docsCollection = defineCollection({
  type: 'content',
  schema: z.object({
    title: z.string(),
    order: z.number(),
    category: z.enum(['guide', 'api', 'tutorial']),
  }),
});

export const collections = {
  blog: blogCollection,
  docs: docsCollection,
};
```

### 콘텐츠 파일:

```markdown
---
# src/content/blog/my-post.md
title: "선언형 프로그래밍이란"
pubDate: 2025-02-14
author: "홍시아빠"
tags: ["declarative", "programming"]
---

본문 내용...
```

### 페이지에서 사용:

```astro
---
// src/pages/blog/[slug].astro
import { getCollection } from 'astro:content';

export async function getStaticPaths() {
  const posts = await getCollection('blog');
  return posts.map(post => ({
    params: { slug: post.slug },
    props: { post },
  }));
}

const { post } = Astro.props;
const { Content } = await post.render();
---

<article>
  <h1>{post.data.title}</h1>
  <time>{post.data.pubDate}</time>
  <Content />
</article>
```

### 대안: VitePress

```typescript
// .vitepress/config.ts - 선언형 문서 사이트 설정
export default defineConfig({
  title: 'My Docs',
  themeConfig: {
    nav: [
      { text: 'Guide', link: '/guide/' },
      { text: 'API', link: '/api/' },
    ],
    sidebar: {
      '/guide/': [
        { text: 'Introduction', link: '/guide/intro' },
        { text: 'Getting Started', link: '/guide/start' },
      ],
    },
  },
});
```

---

## 6. 선언형 인프라 & 배포

### Docker Compose (선언형 인프라):

```yaml
# docker-compose.yml
services:
  app:
    build: .
    ports:
      - "3000:3000"
    environment:
      - DATABASE_URL=file:/data/app.db
    volumes:
      - ./data:/data
    depends_on:
      - backup
    restart: unless-stopped

  backup:
    image: litestream/litestream
    volumes:
      - ./data:/data
      - ./litestream.yml:/etc/litestream.yml
    command: replicate -config /etc/litestream.yml
```

### Kamal (선언형 배포, Rails 팀 제작):

```yaml
# config/deploy.yml
service: my-app
image: my-registry/my-app

servers:
  web:
    - 192.168.1.100  # 맥미니 1
    - 192.168.1.101  # 맥미니 2
  job:
    - 192.168.1.102

registry:
  server: ghcr.io
  username: my-user
  password:
    - KAMAL_REGISTRY_PASSWORD

env:
  clear:
    DATABASE_URL: "file:/data/app.db"
  secret:
    - SESSION_SECRET

traefik:
  options:
    publish:
      - "443:443"
    volume:
      - "/letsencrypt:/letsencrypt"
```

```bash
# 한 번에 배포
kamal deploy
```

### Coolify (Self-Host PaaS, 선언형):

```
GitHub 연결 → 자동 감지 → 배포 설정 → 원클릭 배포
```

- Vercel/Netlify의 Self-Host 대안
- UI에서 선언형 설정
- 자동 SSL, 롤백, 스케일링

---

## 7. 선언형 검증 레이어: Zod

### 왜 Zod가 중심인가:

```
✅ 런타임 + 타입 검증 동시에
✅ AI가 스키마 생성을 매우 잘함
✅ 프론트/백엔드 스키마 공유 가능
✅ 폼, API, DB 모든 레이어에서 사용
```

### 전체 스택에서 Zod 사용:

```typescript
// shared/schemas.ts - 단일 소스
import { z } from 'zod';

export const userSchema = z.object({
  email: z.string().email(),
  name: z.string().min(2),
  role: z.enum(['admin', 'user']),
});

export type User = z.infer<typeof userSchema>;
```

```typescript
// DB 스키마 (Drizzle)
import { userSchema } from './schemas';
// Drizzle 스키마는 별도지만, 타입은 공유

// API 검증
app.post('/users', async (c) => {
  const body = userSchema.parse(await c.req.json());
  // ...
});

// 폼 검증 (Superforms)
const { form } = superForm(data.form, {
  validators: zod(userSchema),
});
```

---

## 8. 최종 선언형 스택 추천

### SPA

| 레이어 | 1순위 | 2순위 |
|--------|-------|-------|
| 프레임워크 | Svelte 5 | React 19 |
| 라우팅 | TanStack Router | React Router |
| 상태 | Svelte Store | Zustand |
| 스타일 | Tailwind | UnoCSS |
| 검증 | Zod | Valibot |
| API | Hono | tRPC |
| DB | Drizzle + SQLite | - |

### SSR

| 레이어 | 1순위 | 2순위 |
|--------|-------|-------|
| 프레임워크 | SvelteKit | Remix |
| 폼 | Superforms + Zod | Conform + Zod |
| 스타일 | Tailwind | UnoCSS |
| ORM | Drizzle | Kysely |
| DB | PostgreSQL / SQLite | - |

### SSG

| 레이어 | 1순위 | 2순위 |
|--------|-------|-------|
| 프레임워크 | Astro | VitePress |
| 콘텐츠 | Content Collections | 마크다운 |
| 스키마 | Zod | - |
| Islands | Svelte / React | - |

### 공통 인프라

| 레이어 | 추천 |
|--------|------|
| 컨테이너 | Docker Compose |
| 배포 | Kamal / Coolify |
| 리버스프록시 | Caddy (자동 SSL) |
| 백업 | Litestream (SQLite) |
| 모니터링 | Prometheus + Grafana |

---

## 9. 선언형 수준 요약

```
가장 선언형인 스택:

UI:        Svelte 5 (Runes)
라우팅:    파일 기반 (SvelteKit, Astro)
폼:        Superforms + Zod
API:       tRPC (타입 선언만으로 API 완성)
ORM:       Drizzle (TS 스키마 = DB 스키마)
콘텐츠:    Astro Content Collections
인프라:    Docker Compose + Kamal
```

---

## 참고 자료

- [Drizzle Kit 마이그레이션](https://orm.drizzle.team/kit-docs/overview)
- [Svelte 5 Runes](https://svelte.dev/blog/runes)
- [Astro Content Collections](https://docs.astro.build/en/guides/content-collections/)
- [Superforms](https://superforms.rocks)
- [Kamal](https://kamal-deploy.org)
- [Zod](https://zod.dev)

---

*"무엇을" 원하는지 선언하면, 시스템이 "어떻게"를 처리합니다.*
