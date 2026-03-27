# Atom Research — Astro 정적 사이트 전환 설계

## 목표

기존 순수 HTML 기반 Atom Research 포털을 Astro로 전환하여:
- 디자인 시스템(CSS)을 단일 파일로 관리
- 레이아웃 템플릿으로 헤더/네비/푸터 중복 제거
- 재사용 컴포넌트로 테이블/차트/카드 일관성 확보
- 새 리서치 추가 시 콘텐츠만 작성

## 디자인 기준

AI Agent 서비스 비교 페이지(`ai-agent/summary.html`)의 CSS를 canonical 디자인 시스템으로 사용.

## 프로젝트 구조

```
astro.config.mjs
package.json
src/
  layouts/
    Base.astro            # 공통: <html>, <head>, CSS, 테마토글, 메타태그
    Research.astro         # 리서치 페이지: 헤더, 네비, 푸터 + <slot>
    Portal.astro           # 포털 메인 전용 레이아웃
  components/
    Nav.astro              # 네비게이션 버튼 (links prop)
    ThemeToggle.astro      # 라이트/다크 토글
    HeroGrid.astro         # 4열 하이라이트 카드 그리드
    BenchTable.astro       # .bench 테이블 + 모바일 카드 폴백
    BarChart.astro         # 바 차트 (rows prop)
    Section.astro          # .sec 섹션 래퍼 (label, title)
    Takeaway.astro         # .take 핵심 판단 카드
    UsageGrid.astro        # .usage-grid 카드 그리드
    ArchGrid.astro         # .arch-grid 3열 비교 카드
  styles/
    global.css             # 디자인 시스템 (ai-agent/summary.html 기준)
  pages/
    index.astro            # 포털 메인
    ai-agent/
      index.astro          # AI Agent 비교 (JS→Astro 변환)
      summary.astro
      insights.astro
    tts/
      index.astro
      summary.astro
      insights.astro
    llm-comparison/
      index.astro
      specs.astro
      usecase.astro
      deploy.astro
      summary.astro
      insights.astro
public/
  (정적 파일)
.github/
  workflows/
    deploy.yml             # Astro build → GitHub Pages
```

## 핵심 컴포넌트 설계

### Base.astro
- `<html lang="ko">`, `<head>` 메타, Pretendard 폰트 CDN
- `global.css` import
- `ThemeToggle` 포함
- Props: `title`, `description`

### Research.astro (extends Base)
- `.c` 컨테이너 (max-width: 920px)
- `.hdr` 헤더 (label, title, sub, meta)
- `Nav` 컴포넌트 (navLinks prop)
- `<slot />` — 페이지 콘텐츠
- `.bottom` / `.bottom-left` 푸터
- Props: `title`, `label`, `sub`, `meta`, `navLinks[]`

### Nav.astro
- 인라인 스타일 버튼 (ai-agent/summary.html 패턴)
- `display:flex; gap:8px; margin-bottom:32px; flex-wrap:wrap`
- Props: `links: { href: string, label: string }[]`
- 항상 첫 번째에 "Atom Research" (../) 포함

### global.css
ai-agent/summary.html에서 추출한 전체 CSS:
- CSS 변수: --bg, --card, --text, --mid, --dim, --mute, --border, --border2
- 색상: --blue, --amber, --green, --red, --purple + -bg 변종
- 다크모드: prefers-color-scheme + data-theme
- 컴포넌트: .avg, .hdr, .sec, .hero-grid, .bench, .usage-grid, .arch-grid, .take, .bar-*, .bottom
- 반응형: 768px 단일 브레이크포인트

## 배포

### astro.config.mjs
```js
import { defineConfig } from 'astro/config';

export default defineConfig({
  site: 'https://solution-ai-agent.github.io',
  base: '/atom-research',
  output: 'static',
});
```

### GitHub Actions (.github/workflows/deploy.yml)
- main 브랜치 push 시 트리거
- `npm ci` → `astro build` → GitHub Pages 배포
- 기존 main 브랜치 직접 배포에서 Actions 빌드로 전환

## 마이그레이션 순서

### Phase 1: 기반 구축
1. Astro 프로젝트 초기화 (package.json, astro.config.mjs)
2. global.css 추출 (ai-agent/summary.html CSS)
3. Base.astro, Research.astro, Portal.astro 레이아웃 작성
4. Nav, ThemeToggle, Section 등 기본 컴포넌트 작성

### Phase 2: 포털 메인 이전
5. index.astro (포털 메인) — 카드 그리드

### Phase 3: TTS 이전 (검증용)
6. tts/ 3페이지 이전
7. 로컬 빌드 확인

### Phase 4: LLM 비교 이전
8. llm-comparison/ 6페이지 이전
9. BenchTable, BarChart 등 추가 컴포넌트 작성

### Phase 5: AI Agent 이전
10. ai-agent/index.html JS 렌더링 → Astro 컴포넌트 변환
11. ai-agent/ summary, insights 이전

### Phase 6: 배포 전환
12. GitHub Actions 워크플로 작성
13. 기존 HTML 파일 제거
14. 배포 확인

## 성공 기준
- 모든 페이지가 동일한 디자인 시스템 사용
- 새 리서치 추가 시 레이아웃/네비/푸터 코드 작성 불필요
- `astro build` 결과물이 기존 사이트와 동일하게 렌더링
- GitHub Pages 배포 정상 작동
