# AI Agent 서비스 비교 — Executive Dashboard 디자인 스펙

## 개요

AI Agent 서비스(Perplexity Computer, Claude Cowork/Code, OpenAI Codex, OpenClaw) 비교 정적 페이지를 Executive Dashboard 스타일로 재설계하고 GitHub Pages로 배포한다.

**타겟:** C레벨 임원 — 핵심 수치가 즉시 눈에 들어오는 구조
**배포:** GitHub Pages (`username.github.io/ai-agent-comparison/`)
**구조:** 단일 HTML 파일 (빌드 도구 없음)

## 페이지 구조

개인용/기업용 탭으로 분리. 위에서 아래로:

### 0. 탭 전환
- **개인용** / **기업용** 탭 — 탭 전환 시 아래 모든 섹션의 내용이 해당 맥락으로 변경
- pill 스타일 탭 버튼, 선택 시 액센트 컬러 배경

### 1. 헤더
- 제목: "구독 요금 & 과금 모델 비교"
- 부제: "정액제 vs 크레딧제 — 같은 가격이라도 사용 패턴이 완전히 다릅니다" + 기준일
- 다크/라이트 토글 버튼 (시스템 설정 자동 감지 + 수동 전환, localStorage로 선택 유지)

### 2. 플랜별 기능 & 제약사항 비교표 (최상단)
- **개인용 탭:** 개인 플랜들을 열로 표시 (Perplexity Free/Pro/Max, Claude Free/Pro/Max5x/Max20x, OpenAI Plus/Pro, OpenClaw OSS 등)
- **기업용 탭:** 기업 플랜들을 열로 표시 (Perplexity Ent.Pro/Ent.Max, Claude Team Std/Team Prem/Enterprise, OpenAI Business/Enterprise, OpenClaw Self-hosted)
- 카테고리 필터 pill (전체/에이전트/모델/생성/통합/보안)
- **사용량 제한 행 포함:** 메시지/토큰 제한, 리셋 주기, 소진 시 대응 — 별도 카테고리로 표 상단에 배치
- ✓/— 표시, 카테고리 구분 행

### 3. Key Insights (과금 모델 핵심 차이)
- 2열 그리드 카드 4장
- **개인용 탭:** 시간 기반 정액제(Claude) / 크레딧 소진형(Perplexity) / 메시지 카운트제(OpenAI) / API 종량제(OpenClaw)
- **기업용 탭:** 보안 & 컴플라이언스 / 비용 구조 / 팀 협업 / 데이터 거버넌스

### 4. 서비스별 플랜 & 장단점 카드
- 4열 그리드 (모바일: 2열→1열)
- 각 카드 내용:
  - 아이콘 + 서비스명 + 부제
  - 과금 모델 태그 (크레딧 소진형 / 시간 윈도우 정액제 / 메시지 카운트제 / API 종량제)
  - 해당 탭의 플랜 목록 (추천 플랜 하이라이트)
  - 장점 / 단점 리스트
- **개인용 탭:** 개인 플랜만 표시
- **기업용 탭:** 기업 플랜만 표시

### 5. 푸터
- 가격 기준일 (2026년 3월 27일), 세금 미포함, 면책 조항

## 비주얼 디자인

### 스타일 톤
- 모던 클린: 화이트 배경 + 소프트 섀도우 (보더 대신 그림자로 카드 분리)
- 넉넉한 패딩/여백, 에어리한 레이아웃
- 둥근 모서리 (16px)

### 컬러 시스템
- **라이트 모드:** 배경 `#f4f6f9`, 카드 `#ffffff`, 보더 `#f1f5f9`, 텍스트 `#1e293b`, 텍스트 dim `#94a3b8`, 액센트 `#0ea5e9`
- **다크 모드:** 배경 `#0f172a`, 카드 `#1e293b`, 보더 `#334155`, 텍스트 `#f1f5f9`, 텍스트 dim `#64748b`, 액센트 `#38bdf8`
- `prefers-color-scheme` 미디어 쿼리로 자동 전환 + `data-theme` 속성으로 수동 오버라이드
- 서비스 컬러: Perplexity `#0ea5e9`, Claude `#f59e0b`, OpenAI `#10b981`, OpenClaw `#ef4444`
- 서비스별 라이트 배경: `#e0f2fe`, `#fef3c7`, `#d1fae5`, `#fee2e2`

### 서비스별 strength 텍스트
- Perplexity: "멀티모델 자동 오케스트레이션 + 클라우드 격리 실행"
- Claude: "코딩 + 데스크톱 자동화 + 모바일 원격 제어"
- OpenAI: "코딩 에이전트 + 이미지/비디오 생성 통합"
- OpenClaw: "모델 비종속 · 로컬 실행 · 완전 커스터마이징"

### 타이포그래피
- Pretendard (CDN)
- 제목: 32px/800, 섹션 제목: 20px/800, 카드 가격: 28px/800, 본문: 12-13px

### 반응형
- max-width 1120px 컨테이너
- 960px 이하: 카드 2열
- 600px 이하: 모두 1열

## 인터랙션

- **탭 전환:** 개인용/기업용 전환 시 인사이트, 카드, 비교표 모두 해당 맥락으로 변경
- **카테고리 필터:** pill 클릭 시 비교표 행을 해당 카테고리만 표시 (토글)
- **다크/라이트 토글:** `<html>` 에 `data-theme` 속성 설정, CSS 변수 전환, localStorage 저장
- **hover 효과:** translateY(-2px) + box-shadow 강화

## 데이터

현재 `docs/ref/index.html`의 DATA 객체를 기반으로 한다:
- 서비스 4개, 플랜 총 19개, 기능 16개 — 구조 동일하게 유지
- 추가 필드: `strength` (핵심 강점), `billingModel` (과금 모델명), `pros`/`cons` (장단점 배열)
- 플랜에 `category` 필드 추가: "personal" 또는 "enterprise"로 분류
- 사용량 제한 데이터: 각 플랜에 `limit` (메시지/토큰 제한), `resetCycle` (리셋 주기), `onExhaust` (소진 시 대응)
- Key Insights 텍스트는 개인용/기업용 별도 정의

데이터는 웹 리서치를 통해 2026년 3월 기준으로 최신화한다. 추측 금지 — 확인 안 되는 항목은 "— 미확인" 처리.

## GitHub Pages 배포

1. `git init` + GitHub repo 생성 (`ai-agent-comparison`)
2. `index.html`을 repo 루트에 배치 (기존 `docs/ref/index.html`은 참조용으로 유지)
3. GitHub Settings → Pages → Source: main branch, / (root)
4. `.gitignore`에 `.superpowers/` 추가
5. 배포 확인: `https://<username>.github.io/ai-agent-comparison/`

## 범위 외

- 빌드 도구, 프레임워크
- 다국어 지원
- 백엔드/데이터베이스
- 자동 데이터 업데이트
- 접근성 (ARIA) — 추후 개선 가능
- 시나리오별 추천 (제외됨)

## 디자인 레퍼런스

- 목업 파일: `.superpowers/brainstorm/` 내 `tab-layout.html` (최종 확정 목업)
- 참조 이미지 톤: 화이트 베이스, 소프트 섀도우, 청록 액센트, 넉넉한 여백
