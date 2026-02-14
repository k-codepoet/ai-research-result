# Self-Host 최적화 웹 스택 리서치

> 작성일: 2025-02-14  
> 목적: 맥미니/미니PC 환경에서 직접 빌드·배포할 때 AI가 잘 다루는 기술 스택 분석

---

## 📊 Self-Host 관점 프레임워크 비교

| 프레임워크 | Self-Host 난이도 | Docker 친화 | 리소스 사용 | 빌드 속도 | AI 친화도 |
|-----------|-----------------|-------------|------------|----------|----------|
| **SvelteKit** | ⭐ 매우 쉬움 | ✅✅ | 낮음 | 빠름 | ⭐⭐⭐⭐ |
| **Astro** | ⭐ 매우 쉬움 | ✅✅ | 최저 | 빠름 | ⭐⭐⭐⭐ |
| **Remix** | ⭐⭐ 쉬움 | ✅✅ | 낮음 | 빠름 | ⭐⭐⭐⭐ |
| **Hono** | ⭐ 매우 쉬움 | ✅✅ | 최저 | 최빠름 | ⭐⭐⭐ |
| **Next.js** | ⭐⭐⭐ 중간 | ✅ | 높음 | 느림 | ⭐⭐⭐⭐⭐ |
| **Nuxt 3** | ⭐⭐ 쉬움 | ✅✅ | 중간 | 중간 | ⭐⭐⭐⭐ |

---

## 1. Next.js Self-Host의 문제점

### 왜 애매한가:

```
❌ 이미지 최적화 → Vercel 전용, self-host 시 sharp 설정 필요
❌ ISR (Incremental Static Regeneration) → 캐시 서버 별도 구성 필요
❌ Edge Runtime → Vercel/Cloudflare 의존
❌ 번들 크기 → node_modules 무거움, 콜드 스타트 느림
❌ 빌드 시간 → 대규모 프로젝트에서 느림
❌ standalone 모드 → 설정 복잡, 일부 기능 제한
```

### Next.js Self-Host 시 필요한 작업:
```javascript
// next.config.js
module.exports = {
  output: 'standalone',  // 필수
  images: {
    unoptimized: true,   // 또는 별도 이미지 서버 구성
    // loader: 'custom',
  },
}
```

```dockerfile
# 멀티스테이지 빌드 필요, 복잡함
FROM node:20-alpine AS builder
# ... 생략 ...
```

---

## 2. 🥇 Self-Host 최강: SvelteKit

### 왜 좋은가:

```
✅ adapter-node → 단순한 Node.js 서버로 빌드
✅ adapter-static → 완전 정적 파일로 빌드 가능
✅ 번들 크기 최소 → 빠른 콜드 스타트
✅ 빌드 결과물이 깔끔 → build/ 폴더만 배포
✅ 외부 서비스 의존성 없음
```

### 설정:
```bash
npm i -D @sveltejs/adapter-node
```

```javascript
// svelte.config.js
import adapter from '@sveltejs/adapter-node';

export default {
  kit: {
    adapter: adapter({
      out: 'build',
      precompress: true,  // gzip/brotli 미리 생성
    })
  }
};
```

### Docker (초간단):
```dockerfile
FROM node:20-alpine
WORKDIR /app
COPY build/ ./build/
COPY package.json ./
RUN npm ci --omit=dev
EXPOSE 3000
CMD ["node", "build"]
```

### 맥미니 배포:
```bash
# 빌드
npm run build

# PM2로 실행
pm2 start build/index.js --name my-app

# 또는 systemd
sudo systemctl enable my-app
```

---

## 3. 🥈 정적 사이트: Astro

### Self-Host 장점:

```
✅ 빌드 결과물이 순수 HTML/CSS/JS
✅ Nginx/Caddy로 바로 서빙 가능
✅ CDN 없이도 빠름
✅ Node.js 런타임 불필요 (SSG 모드)
```

### 설정:
```javascript
// astro.config.mjs
import { defineConfig } from 'astro/config';
import node from '@astrojs/node';

export default defineConfig({
  output: 'static',  // 또는 'server' for SSR
  adapter: node({
    mode: 'standalone'
  }),
});
```

### Nginx 설정 (정적):
```nginx
server {
    listen 80;
    root /var/www/my-astro-site/dist;
    
    location / {
        try_files $uri $uri/ /index.html;
    }
    
    # 캐싱
    location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }
}
```

---

## 4. 🥉 풀스택: Remix

### Self-Host 장점:

```
✅ Express 어댑터 → 표준 Node.js 서버
✅ 웹 표준 API 사용 → 플랫폼 종속성 낮음
✅ 빌드 결과물 단순
✅ 서버 사이드 중심 → 클라이언트 번들 작음
```

### 설정:
```bash
npx create-remix@latest --template remix-run/remix/templates/express
```

```javascript
// remix.config.js
module.exports = {
  serverBuildTarget: "node-cjs",
  server: "./server.js",
};
```

### Docker:
```dockerfile
FROM node:20-alpine
WORKDIR /app
COPY package*.json ./
RUN npm ci --omit=dev
COPY build/ ./build/
COPY public/ ./public/
EXPOSE 3000
CMD ["npm", "start"]
```

---

## 5. 🚀 초경량: Hono

### 특징:

```
✅ 번들 크기 ~14KB (압축 시)
✅ Bun/Deno/Node 모두 지원
✅ 엣지에서도, 미니PC에서도 동일하게 동작
✅ JSX 지원으로 풀스택 가능
✅ 콜드 스타트 거의 즉시
```

### 풀스택 예시:
```typescript
// src/index.tsx
import { Hono } from 'hono';
import { serveStatic } from 'hono/serve-static';

const app = new Hono();

app.get('/', (c) => {
  return c.html(
    <html>
      <body>
        <h1>Hello from Hono!</h1>
      </body>
    </html>
  );
});

app.use('/static/*', serveStatic({ root: './' }));

export default app;
```

### Bun으로 실행 (맥미니 최적):
```bash
# Bun 설치
curl -fsSL https://bun.sh/install | bash

# 실행
bun run src/index.tsx
```

---

## 6. DB & ORM Self-Host 분석

### 📊 ORM 비교

| ORM | 타입 안전성 | 마이그레이션 | 성능 | AI 친화도 | Self-Host |
|-----|-----------|-------------|------|----------|-----------|
| **Drizzle** | ✅✅✅ | 수동/자동 | 최고 | ⭐⭐⭐⭐ | ✅✅ |
| **Prisma** | ✅✅ | 자동 | 중간 | ⭐⭐⭐⭐⭐ | ✅ |
| **Kysely** | ✅✅✅ | 수동 | 최고 | ⭐⭐⭐ | ✅✅ |
| **TypeORM** | ✅ | 자동 | 낮음 | ⭐⭐⭐ | ✅ |
| **Knex** | ❌ | 수동 | 높음 | ⭐⭐⭐ | ✅✅ |

### 🥇 Self-Host 최고: **Drizzle ORM**

**왜 좋은가:**
```
✅ 순수 SQL에 가까움 → 디버깅 쉬움
✅ 번들 크기 작음 → Prisma의 1/10
✅ 런타임 의존성 없음 → Prisma Engine 불필요
✅ SQLite 완벽 지원 → 미니PC에 최적
✅ 타입 추론이 정확
```

**설치:**
```bash
npm i drizzle-orm
npm i -D drizzle-kit
```

**스키마 예시:**
```typescript
// src/db/schema.ts
import { sqliteTable, text, integer } from 'drizzle-orm/sqlite-core';

export const users = sqliteTable('users', {
  id: integer('id').primaryKey({ autoIncrement: true }),
  name: text('name').notNull(),
  email: text('email').unique().notNull(),
  createdAt: integer('created_at', { mode: 'timestamp' })
    .default(sql`CURRENT_TIMESTAMP`),
});

export const posts = sqliteTable('posts', {
  id: integer('id').primaryKey({ autoIncrement: true }),
  title: text('title').notNull(),
  content: text('content'),
  authorId: integer('author_id').references(() => users.id),
});
```

**쿼리:**
```typescript
// 타입 안전한 쿼리
const result = await db
  .select()
  .from(users)
  .where(eq(users.email, 'test@example.com'));
```

### 🥈 AI 친화도 최고: **Prisma**

**장점:**
- AI가 가장 잘 생성하는 ORM
- 스키마 문법이 직관적
- 자동 마이그레이션

**Self-Host 단점:**
```
❌ Prisma Engine 바이너리 필요 (~15MB)
❌ 콜드 스타트 느림
❌ ARM64 호환성 이슈 있었음 (최근 개선)
```

**그래도 쓴다면:**
```prisma
// prisma/schema.prisma
generator client {
  provider = "prisma-client-js"
  binaryTargets = ["native", "linux-arm64-openssl-3.0.x"]
}

datasource db {
  provider = "sqlite"  // self-host에 적합
  url      = "file:./dev.db"
}
```

### 🥉 SQL 빌더: **Kysely**

**특징:**
```
✅ 순수 타입스크립트 SQL 빌더
✅ ORM 오버헤드 없음
✅ 복잡한 쿼리에 강함
✅ Drizzle과 비슷하지만 더 로우레벨
```

```typescript
const result = await db
  .selectFrom('users')
  .innerJoin('posts', 'posts.author_id', 'users.id')
  .select(['users.name', 'posts.title'])
  .where('users.id', '=', 1)
  .execute();
```

---

## 7. Self-Host DB 선택

### 📊 데이터베이스 비교

| DB | 설치 난이도 | 리소스 | 백업 | 미니PC 적합 |
|----|-----------|--------|------|------------|
| **SQLite** | 없음 | 최저 | 파일 복사 | ⭐⭐⭐⭐⭐ |
| **PostgreSQL** | 중간 | 중간 | pg_dump | ⭐⭐⭐⭐ |
| **MySQL/MariaDB** | 중간 | 중간 | mysqldump | ⭐⭐⭐ |
| **Turso (libSQL)** | 쉬움 | 최저 | 자동 | ⭐⭐⭐⭐⭐ |

### 🥇 미니PC 최적: **SQLite + Litestream**

```
✅ 설치 필요 없음
✅ 단일 파일 DB
✅ 읽기 성능 최고
✅ Litestream으로 실시간 백업
```

**Litestream 설정 (자동 백업):**
```yaml
# litestream.yml
dbs:
  - path: /data/app.db
    replicas:
      - url: s3://my-bucket/app.db
        sync-interval: 1s
```

```bash
# 실행
litestream replicate -config litestream.yml
```

### 🥈 확장 필요시: **PostgreSQL**

```bash
# Docker로 실행
docker run -d \
  --name postgres \
  -e POSTGRES_PASSWORD=secret \
  -v pgdata:/var/lib/postgresql/data \
  -p 5432:5432 \
  postgres:16-alpine
```

---

## 8. 실전 Self-Host 스택 추천

### 🎯 미니PC 1대 (맥미니 M1/M2)

```
프레임워크: SvelteKit + adapter-node
DB: SQLite + Drizzle ORM
런타임: Bun (또는 Node.js)
프로세스: PM2
리버스 프록시: Caddy (자동 HTTPS)
백업: Litestream → S3/R2
```

**Caddy 설정:**
```
my-app.example.com {
    reverse_proxy localhost:3000
}
```

### 🎯 미니PC 여러 대 (클러스터)

```
프레임워크: SvelteKit 또는 Hono
DB: PostgreSQL (1대) + 읽기 복제본
ORM: Drizzle
컨테이너: Docker Swarm 또는 K3s
로드밸런서: Traefik
모니터링: Prometheus + Grafana
```

### 🎯 정적 사이트 + API

```
프론트: Astro (정적 빌드)
API: Hono (Bun)
DB: SQLite + Drizzle
서빙: Nginx (정적) + PM2 (API)
```

---

## 9. 맥미니 배포 자동화 예시

### GitHub Actions → Self-Host

```yaml
# .github/workflows/deploy.yml
name: Deploy to Mac Mini

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Setup Bun
        uses: oven-sh/setup-bun@v1
        
      - name: Build
        run: |
          bun install
          bun run build
          
      - name: Deploy via SSH
        uses: appleboy/scp-action@v0.1.7
        with:
          host: ${{ secrets.HOST }}
          username: ${{ secrets.USER }}
          key: ${{ secrets.SSH_KEY }}
          source: "build/*"
          target: "/home/app/my-app"
          
      - name: Restart App
        uses: appleboy/ssh-action@v1.0.3
        with:
          host: ${{ secrets.HOST }}
          username: ${{ secrets.USER }}
          key: ${{ secrets.SSH_KEY }}
          script: |
            cd /home/app/my-app
            pm2 restart my-app
```

### 간단한 배포 스크립트

```bash
#!/bin/bash
# deploy.sh

set -e

echo "Building..."
bun run build

echo "Deploying to mac-mini..."
rsync -avz --delete build/ mac-mini:/home/app/my-app/build/

echo "Restarting..."
ssh mac-mini "pm2 restart my-app"

echo "Done!"
```

---

## 10. 결론

### Self-Host 최종 추천:

| 용도 | 프레임워크 | DB | ORM |
|------|-----------|-----|-----|
| 풀스택 웹앱 | **SvelteKit** | SQLite | **Drizzle** |
| API 서버 | **Hono** | PostgreSQL | **Drizzle** |
| 콘텐츠 사이트 | **Astro** | - | - |
| 대시보드 | **SvelteKit** | SQLite | **Drizzle** |

### AI + Self-Host 밸런스:

```
AI 친화도 최고 + Self-Host 불편: Next.js + Prisma
AI 친화도 높음 + Self-Host 최고: SvelteKit + Drizzle ← 추천
```

### 피해야 할 것:

```
❌ Next.js ISR/이미지 최적화 의존
❌ Vercel/Netlify 전용 기능 사용
❌ 무거운 ORM (TypeORM)
❌ 클라우드 전용 DB (PlanetScale, Neon의 서버리스 모드)
```

---

## 참고 자료

- [SvelteKit adapter-node](https://kit.svelte.dev/docs/adapter-node)
- [Drizzle ORM 문서](https://orm.drizzle.team)
- [Hono 문서](https://hono.dev)
- [Litestream](https://litestream.io)
- [Caddy Server](https://caddyserver.com)

---

*이 문서는 Self-Host/미니PC 환경 최적화 관점으로 작성되었습니다.*
