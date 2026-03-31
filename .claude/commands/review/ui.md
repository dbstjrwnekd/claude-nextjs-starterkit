---
description: '프론트엔드 UX/UI 관점(접근성, 반응형, 상태 처리, 시각적 일관성, 성능, 컴포넌트 구조)에서 코드를 리뷰합니다'
allowed-tools:
  [
    'Glob',
    'Grep',
    'Read',
    'Bash(find:*)',
    'Bash(ls:*)',
  ]
---

# Claude 명령어: Review UI

프론트엔드 UX/UI 관점에서 코드를 분석하고 개선점을 제안합니다. 코드를 수정하지 않으며 리뷰 리포트만 출력합니다.

## 사용법

```
/review:ui
/review:ui app/components/Button.tsx
/review:ui app/components/
```

`$ARGUMENTS`가 없으면 `app/` 전체를 대상으로 합니다.

## 프로세스

1. 리뷰 대상 파일 목록 탐색 (`$ARGUMENTS` 또는 `app/`)
2. 각 파일을 6가지 관점으로 순차 분석
3. 심각도별로 분류된 리뷰 리포트 출력
4. 우선순위 개선 제안 정리

## 리뷰 관점 및 체크리스트

### 1. 접근성 (Accessibility / a11y)

**시맨틱 HTML**
- `<button>`, `<nav>`, `<main>`, `<header>`, `<footer>`, `<section>`, `<article>` 등 시맨틱 태그 사용 여부
- `<div onClick>` 대신 `<button>` 사용 여부
- 제목 태그 계층 구조(`h1` → `h2` → `h3`) 논리적 순서 여부

**ARIA 속성**
- 아이콘 전용 버튼에 `aria-label` 또는 `aria-labelledby` 존재 여부
- 모달/다이얼로그에 `role="dialog"`, `aria-modal="true"`, `aria-labelledby` 적용 여부
- 동적으로 업데이트되는 영역에 `aria-live` 적용 여부
- 상태 변화(토글, 확장/축소)에 `aria-expanded`, `aria-selected`, `aria-checked` 사용 여부
- 장식용 이미지에 `alt=""`, 의미 있는 이미지에 구체적 `alt` 텍스트 여부

**키보드 네비게이션**
- 모든 인터랙티브 요소가 Tab으로 포커스 가능한지
- 커스텀 드롭다운/모달에서 포커스 트랩(focus trap) 구현 여부
- `onKeyDown`에서 Enter, Space, Escape, Arrow 키 처리 여부
- 포커스 인디케이터(outline)를 `outline: none`으로 제거하고 대체 스타일 없는 경우

**색상 대비**
- Tailwind 색상 클래스 조합에서 WCAG AA 기준(4.5:1) 충족 여부
- `text-zinc-400` on `bg-white` 등 낮은 대비 조합 탐지
- 색상만으로 정보를 전달하는 경우 (아이콘, 텍스트 레이블 병행 여부)

### 2. 반응형 디자인 (Responsive Design)

**브레이크포인트 대응**
- Tailwind 반응형 접두사(`sm:`, `md:`, `lg:`, `xl:`) 적절한 사용 여부
- 모바일 퍼스트 원칙 준수 (기본값이 모바일 스타일인지)
- `w-[고정px]`로 하드코딩된 너비가 소형 화면에서 overflow 유발 여부

**레이아웃**
- Flexbox/Grid 방향이 좁은 화면에서 적절히 전환되는지 (`flex-col sm:flex-row`)
- 이미지/미디어가 `max-w-full` 또는 `w-full`로 컨테이너 초과 방지 여부
- 터치 타겟 최소 크기 (44x44px, Tailwind `h-11 w-11`) 충족 여부

**Next.js Image 최적화**
- `<img>` 태그 대신 `next/image`의 `<Image>` 사용 여부
- `width`, `height` 또는 `fill` prop 지정 여부
- `sizes` prop으로 반응형 이미지 최적화 여부

### 3. 사용자 경험 - 상태 처리 (UX State Handling)

**로딩 상태**
- 비동기 데이터 패칭 시 로딩 UI 존재 여부 (스켈레톤, 스피너 등)
- Next.js App Router `loading.tsx` 파일 활용 여부
- 버튼 클릭 후 중복 제출 방지 (`disabled` 상태, `isPending`) 여부
- `React.Suspense` 경계 적절한 설정 여부

**에러 상태**
- API 에러, 네트워크 에러에 대한 사용자 친화적 메시지 표시 여부
- Next.js App Router `error.tsx` 파일 활용 여부
- 폼 유효성 검사 에러 메시지 위치가 해당 필드 근처인지
- 에러 상태에서 복구 액션 (재시도 버튼 등) 제공 여부

**빈 상태 (Empty State)**
- 데이터 없을 때 빈 화면 대신 안내 메시지 표시 여부
- 빈 상태에서 다음 행동 유도 (CTA) 제공 여부

**인터랙션 피드백**
- `hover:`, `focus:`, `active:` 상태 스타일 정의 여부
- `transition` / `duration-*`으로 상태 전환 애니메이션 여부
- 긴 작업 완료 후 성공/실패 토스트/알림 여부

### 4. 시각적 일관성 (Visual Consistency)

**디자인 토큰 / CSS 변수**
- Tailwind arbitrary value (`bg-[#123456]`) 남용 확인
- `globals.css`에 정의된 CSS 변수 (`--background`, `--foreground`, `--font-sans` 등) 일관 사용 여부
- 하드코딩된 색상/간격 대신 Tailwind 스케일 토큰 사용 여부

**컴포넌트 일관성**
- 동일한 역할의 UI 요소 (버튼, 카드, 입력 필드)가 다른 스타일로 구현된 경우
- `dark:` 접두사를 사용하는 다크모드 스타일이 누락된 요소
- 폰트 패밀리가 `--font-sans`, `--font-mono` 토큰 외 임의값 사용 여부

### 5. 성능 (Performance)

**렌더링 최적화**
- 클라이언트 컴포넌트(`'use client'`) 범위가 최소화되었는지 (서버 컴포넌트 우선 원칙)
- 리스트 렌더링 시 `key` prop에 index 대신 고유 식별자 사용 여부
- 대형 컴포넌트에서 불필요한 리렌더 유발 가능성

**코드 분할 및 지연 로딩**
- 크고 독립적인 섹션에 `next/dynamic` 또는 `React.lazy` + `Suspense` 적용 여부
- 초기 렌더에 불필요한 heavy 라이브러리 import 여부

**이미지 및 미디어**
- `priority` prop이 Above-the-fold 이미지에만 적용되었는지 (남용 여부)

### 6. 코드 품질 - 컴포넌트 구조 (Code Quality)

**관심사 분리**
- UI 로직(상태, 이벤트)과 비즈니스 로직(데이터 패칭, 변환)이 혼재하는지
- 커스텀 훅으로 분리 가능한 복잡한 상태 로직 여부

**컴포넌트 크기 및 분리**
- 단일 컴포넌트가 150줄 이상으로 여러 역할을 담당하는지
- 반복되는 JSX 패턴을 별도 컴포넌트로 분리할 수 있는지
- prop drilling이 3단계 이상 발생하는지

**TypeScript**
- `any` 타입 사용 여부
- 이벤트 핸들러 타입 (`React.MouseEvent`, `React.ChangeEvent`) 명시 여부
- 컴포넌트 props interface/type 정의 여부

## 출력 형식

리뷰 완료 후 아래 구조로 리포트를 출력합니다.

---

## UI/UX 코드 리뷰 리포트

**리뷰 대상**: `[파일 또는 디렉토리]`

---

### 요약

| 심각도 | 건수 |
|--------|------|
| 🔴 Critical (접근성/기능 저해) | N건 |
| 🟡 Warning (UX 개선 필요) | N건 |
| 🔵 Info (권고 사항) | N건 |

---

### 상세 리뷰

각 항목은 아래 형식으로 작성합니다.

**[심각도] 관점 > 항목명**
- **파일**: `파일경로:라인번호`
- **문제**: 문제 설명
- **제안**: 구체적인 개선 방향 (코드 예시 포함 가능)

---

### 우선 개선 권고

가장 임팩트 높은 개선 항목 3~5개를 우선순위 순으로 정리합니다.

---

## 참고사항

- 코드를 직접 수정하지 않으며, 분석과 제안만 제공합니다
- Next.js App Router, React 19, Tailwind CSS v4 컨텍스트를 기준으로 리뷰합니다
- WCAG 2.1 AA 기준을 접근성 판단 기준으로 사용합니다
- `$ARGUMENTS`에 여러 경로를 공백으로 구분하여 전달할 수 있습니다
