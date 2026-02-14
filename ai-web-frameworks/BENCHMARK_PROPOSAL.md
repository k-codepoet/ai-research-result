# AI Code Generation Benchmark Arena 기획안

> 작성일: 2025-02-14  
> 컨셉: "LM Arena for Code Generation × Framework"

---

## 1. 문제 정의

### 현재 상황
- AI 코드 생성 도구 급증 (Copilot, Cursor, Claude, v0 등)
- 프레임워크별 AI 친화도 정보 부재
- "어떤 프레임워크가 AI와 잘 맞나?"에 대한 객관적 데이터 없음
- 개발자들이 감으로 선택

### 필요한 것
```
프레임워크 × AI 모델 × 태스크 유형 → 품질 점수
```

---

## 2. 서비스 컨셉

### 이름 후보
- **CodeArena** - AI 코드 생성 벤치마크
- **FrameworkBench** - 프레임워크별 AI 코드 품질
- **DevBench** - 개발자 생산성 벤치마크
- **AICodeRank** - AI 코드 생성 랭킹

### 핵심 가치
```
"어떤 프레임워크가 AI 시대에 생산성이 높은가?"
에 대한 객관적 데이터 제공
```

---

## 3. 벤치마크 설계

### 3.1 평가 축

```
┌─────────────────────────────────────────────┐
│         3차원 벤치마크 매트릭스              │
├─────────────────────────────────────────────┤
│  X축: 프레임워크                            │
│       React, Vue, Svelte, Angular, ...      │
│                                             │
│  Y축: AI 모델                               │
│       GPT-4, Claude, Gemini, Copilot, ...   │
│                                             │
│  Z축: 태스크 유형                           │
│       컴포넌트, CRUD, API, 폼, ...          │
└─────────────────────────────────────────────┘
```

### 3.2 프레임워크 카테고리

| 카테고리 | 프레임워크 |
|----------|-----------|
| React 메타 | Next.js, Remix, TanStack Start |
| Vue 메타 | Nuxt 3 |
| Svelte 메타 | SvelteKit |
| 기타 메타 | Astro, Qwik |
| 순수 SPA | React+Vite, Vue+Vite, Svelte |
| 백엔드 | Hono, Express, Fastify |
| ORM | Drizzle, Prisma, Kysely |

### 3.3 AI 모델

| 카테고리 | 모델 |
|----------|------|
| OpenAI | GPT-4o, GPT-4-turbo, o1 |
| Anthropic | Claude 3.5 Sonnet, Claude 3 Opus |
| Google | Gemini 1.5 Pro, Gemini 2.0 |
| 오픈소스 | Llama 3, DeepSeek, Qwen |
| 코딩 특화 | Codestral, StarCoder |
| 통합 도구 | Copilot, Cursor, v0 |

### 3.4 태스크 유형

| 난이도 | 태스크 | 설명 |
|--------|--------|------|
| L1 | 컴포넌트 생성 | 버튼, 카드, 모달 등 UI 컴포넌트 |
| L1 | 스타일링 | Tailwind/CSS로 디자인 구현 |
| L2 | 폼 + 검증 | 입력 폼 + Zod 검증 |
| L2 | API 라우트 | REST API 엔드포인트 |
| L2 | DB CRUD | 기본 데이터베이스 작업 |
| L3 | 인증 | 로그인/회원가입 플로우 |
| L3 | 파일 업로드 | 이미지/파일 처리 |
| L4 | 실시간 | WebSocket, SSE |
| L4 | 결제 연동 | Stripe 등 외부 API |
| L5 | 전체 앱 | 기능 명세 → 완성 앱 |

---

## 4. 평가 메트릭

### 4.1 자동 평가 (Automated)

| 메트릭 | 측정 방법 |
|--------|----------|
| **Compilable** | 빌드 성공 여부 (0/1) |
| **Type Safe** | TypeScript 에러 수 |
| **Lint Pass** | ESLint 경고/에러 수 |
| **Test Pass** | 자동 생성 테스트 통과율 |
| **Bundle Size** | 빌드 결과물 크기 |
| **Performance** | Lighthouse 점수 |

### 4.2 LLM-as-Judge 평가

```
GPT-4/Claude가 생성된 코드를 평가:
- 코드 품질 (1-10)
- 모범 사례 준수 (1-10)
- 가독성 (1-10)
- 프레임워크 관용구 사용 (1-10)
```

### 4.3 인간 평가 (Human Eval)

LM Arena 스타일 블라인드 비교:

```
┌─────────────────────────────────────────────┐
│  "버튼 컴포넌트를 만들어줘"                  │
├────────────────────┬────────────────────────┤
│  코드 A            │  코드 B                │
│  (프레임워크 숨김)  │  (프레임워크 숨김)     │
├────────────────────┴────────────────────────┤
│  [ A가 더 좋음 ] [ 비슷함 ] [ B가 더 좋음 ]  │
└─────────────────────────────────────────────┘
```

### 4.4 종합 점수

```
Final Score = 
  0.3 × Automated Score +
  0.3 × LLM Judge Score +
  0.4 × Human Elo Rating
```

---

## 5. 데이터 수집

### 5.1 벤치마크 데이터셋

표준화된 태스크 세트:

```yaml
# benchmarks/L2/todo-crud.yaml
name: "TODO CRUD App"
level: 2
description: "Create a TODO app with create, read, update, delete"
requirements:
  - List all todos
  - Add new todo
  - Toggle completion
  - Delete todo
  - Persist to database
evaluation:
  automated:
    - builds: true
    - has_types: true
  functional:
    - can_add_todo: true
    - can_delete_todo: true
    - can_toggle_todo: true
    - persists_data: true
```

### 5.2 크라우드소싱

1. **개발자 제출**
   - 실제 프로젝트에서 AI에게 요청한 프롬프트 제출
   - 생성된 코드와 수정 내역 제출

2. **블라인드 평가**
   - 커뮤니티가 A/B 비교 평가
   - Elo 레이팅 시스템

### 5.3 자동 수집

```python
# 주기적으로 각 모델에 동일 프롬프트 전송
for framework in FRAMEWORKS:
    for model in MODELS:
        for task in TASKS:
            prompt = generate_prompt(task, framework)
            code = model.generate(prompt)
            result = evaluate(code, task)
            save_to_db(framework, model, task, result)
```

---

## 6. 결과 시각화

### 6.1 리더보드

```
┌─────────────────────────────────────────────────────────┐
│  AI Code Generation Leaderboard                         │
├──────┬─────────────┬────────┬────────┬────────┬────────┤
│ Rank │ Framework   │ Score  │ L1-L3  │ L4-L5  │ Trend  │
├──────┼─────────────┼────────┼────────┼────────┼────────┤
│  1   │ Next.js     │ 87.3   │ 92.1   │ 78.5   │ ↑ +2.1 │
│  2   │ React+Vite  │ 85.7   │ 90.8   │ 75.2   │ ↓ -0.5 │
│  3   │ SvelteKit   │ 84.2   │ 88.5   │ 76.1   │ ↑ +3.4 │
│  4   │ Remix       │ 83.9   │ 87.2   │ 77.8   │ → 0    │
│  5   │ Vue+Nuxt    │ 82.1   │ 86.5   │ 73.4   │ ↑ +1.2 │
└──────┴─────────────┴────────┴────────┴────────┴────────┘
```

### 6.2 히트맵

```
             │ GPT-4o │ Claude │ Gemini │ Copilot │
─────────────┼────────┼────────┼────────┼─────────┤
Next.js      │  🟢92  │  🟢90  │  🟡82  │   🟢95  │
React+Vite   │  🟢90  │  🟢88  │  🟡80  │   🟢93  │
SvelteKit    │  🟡78  │  🟢85  │  🟡75  │   🟡79  │
Remix        │  🟡80  │  🟢86  │  🟡77  │   🟡81  │
Astro        │  🟡76  │  🟡82  │  🟡73  │   🟡75  │
```

### 6.3 시계열 트렌드

```
Score
100 ┤
 90 ┤    ╭────Next.js
 80 ┤  ╭─╯  ╭─SvelteKit
 70 ┤╭─╯────╯
 60 ┤│
    └┴─────────────────────
     Jan  Mar  May  Jul  Sep
```

---

## 7. 기술 스택 (서비스 자체)

### MVP

```
Frontend:  Astro + React Islands (이 서비스도 Self-Host 가능하게)
Backend:   Hono + Drizzle + SQLite
Infra:     Docker + Fly.io 또는 Self-Host
Queue:     BullMQ (벤치마크 작업)
Storage:   S3/R2 (생성된 코드 저장)
```

### 벤치마크 실행 환경

```
Docker containers for each framework
→ 격리된 환경에서 빌드/테스트 실행
→ 리소스 사용량 측정
```

---

## 8. 비즈니스 모델

### 무료
- 공개 리더보드 조회
- 기본 벤치마크 결과
- 커뮤니티 평가 참여

### 유료 (Pro)
- 상세 분석 리포트
- 프라이빗 벤치마크 (자사 코드 스타일로)
- API 접근
- 커스텀 태스크 추가

### 엔터프라이즈
- 사내 배포 (Self-Host)
- 자사 모델 평가
- 컨설팅

---

## 9. 로드맵

### Phase 1: MVP (2-3개월)
- [ ] 핵심 프레임워크 5개 (Next, React, Svelte, Vue, Remix)
- [ ] 핵심 모델 3개 (GPT-4, Claude, Gemini)
- [ ] L1-L3 태스크 10개
- [ ] 자동 평가 파이프라인
- [ ] 기본 리더보드 UI

### Phase 2: 커뮤니티 (2개월)
- [ ] 블라인드 평가 시스템
- [ ] Elo 레이팅
- [ ] 사용자 프롬프트 제출
- [ ] 결과 공유 기능

### Phase 3: 확장 (3개월)
- [ ] ORM/백엔드 프레임워크 추가
- [ ] L4-L5 복잡한 태스크
- [ ] 시계열 트렌드
- [ ] API 공개

### Phase 4: 고도화 (지속)
- [ ] LLM-as-Judge 파이프라인
- [ ] 프레임워크 버전별 비교
- [ ] 모델 버전별 비교
- [ ] 연간 리포트 발행

---

## 10. 경쟁 분석 / 차별점

| 기존 서비스 | 한계 | 우리의 차별점 |
|------------|------|--------------|
| LM Arena | 일반 대화만 | **코드 생성 특화** |
| SWE-bench | 이슈 해결만 | **프레임워크별 비교** |
| HumanEval | 알고리즘 문제 | **실제 웹 개발 태스크** |
| State of JS | 설문 기반 | **객관적 벤치마크** |

### 핵심 차별점

```
"AI 시대, 어떤 프레임워크를 배워야 하는가?"
에 대한 데이터 기반 답변 제공
```

---

## 11. 리스크 & 대응

| 리스크 | 대응 |
|--------|------|
| 모델 API 비용 | 캐싱, 샘플링, 스폰서십 |
| 평가 객관성 | 다중 평가자, 투명한 기준 |
| 프레임워크 편향 | 커뮤니티 감수, 오픈소스화 |
| 빠른 버전 변화 | 자동화된 정기 재평가 |

---

## 12. 다음 단계

1. **관심도 검증** - 커뮤니티 반응 확인 (트위터, HN, 레딧)
2. **MVP 스펙 확정** - 최소 범위 결정
3. **파일럿** - 수동으로 5개 프레임워크 × 3개 모델 × 5개 태스크 = 75개 샘플 생성
4. **결과 공개** - 파일럿 결과로 관심 유도
5. **개발 시작** - 반응 좋으면 본격 개발

---

## 부록: 파일럿 태스크 예시

### Task: Button Component

```
Prompt: "Create a reusable Button component with:
- Variants: primary, secondary, outline
- Sizes: sm, md, lg
- Loading state with spinner
- Disabled state
- Full TypeScript types
Use Tailwind CSS for styling."
```

### 평가 기준

```yaml
compilable: true/false
typescript_errors: 0
variants_implemented: 3/3
sizes_implemented: 3/3
loading_state: true/false
disabled_state: true/false
tailwind_usage: true/false
code_lines: N
reusability_score: 1-10 (LLM judge)
```

---

*"측정할 수 없으면 개선할 수 없다." - 피터 드러커*
