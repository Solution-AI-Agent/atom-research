# Executive Dashboard Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Rebuild the AI Agent comparison page as a modern Executive Dashboard with personal/enterprise tab switching and deploy to GitHub Pages.

**Architecture:** Single HTML file with embedded CSS and vanilla JS. Data-driven rendering via a `render()` function that reads a `DATA` object and `state` to produce the full page. Tab switching (personal/enterprise) filters plans by category and swaps insight content.

**Tech Stack:** HTML5, CSS3 (custom properties, `prefers-color-scheme`), vanilla JavaScript, Pretendard font (CDN)

**Spec:** `docs/superpowers/specs/2026-03-27-executive-dashboard-design.md`
**Reference mockup:** `.superpowers/brainstorm/80396-1774573167/tab-layout.html`
**Reference data:** `docs/ref/index.html` (existing DATA object)

---

## File Structure

| File | Purpose |
|------|---------|
| `index.html` | The single deliverable — all CSS, JS, data, and markup |
| `docs/ref/index.html` | Existing reference (untouched) |
| `.gitignore` | Exclude `.superpowers/` |

---

### Task 1: Initialize Git repo and project structure

**Files:**
- Create: `.gitignore`
- Existing: `docs/ref/index.html` (untouched)

- [ ] **Step 1: Initialize git repo**

```bash
cd /Users/zpro/02_dev/01_workspace/etc/ai-agent-comparison
git init
```

- [ ] **Step 2: Create .gitignore**

Create `.gitignore`:
```
.superpowers/
.DS_Store
```

- [ ] **Step 3: Initial commit**

```bash
git add .gitignore docs/
git commit -m "init: project with reference page and specs"
```

---

### Task 2: Build the DATA object and CSS foundation

**Files:**
- Create: `index.html`

Build the `<head>`, CSS variables (light/dark), and the complete `DATA` object with all new fields. No rendering yet — just the foundation.

- [ ] **Step 1: Create index.html with head, CSS, and DATA**

Create `index.html` with:
- `<!DOCTYPE html>`, `<html lang="ko">`, meta tags, Pretendard CDN link
- Full CSS: `:root` variables (light mode defaults), `@media (prefers-color-scheme: dark)` override, `[data-theme="dark"]` / `[data-theme="light"]` manual overrides
- All component styles: `.container`, `.header`, `.main-tabs`, `.section`, `.table-wrap`, `.billing-card`, `.insight-card`, `.pill`, `.filters`, `.footer`, etc.

**Critical design values (must be exact):**
- Container: `max-width: 1120px`
- Card border-radius: `16px` (all cards, table wrap, insight cards)
- Card separation: **box-shadow only, no borders** — light: `0 1px 3px rgba(0,0,0,0.04), 0 4px 12px rgba(0,0,0,0.03)`, hover: `0 2px 8px rgba(0,0,0,0.06), 0 8px 24px rgba(0,0,0,0.06)`
- Typography: title `32px/800`, section title `20px/800`, card price `28px/800`, body `12-13px`
- Hover: `transform: translateY(-2px)` + shadow-hover
- Padding: generous — cards `28px 24px`, sections `margin-bottom: 48px`
- Table first column: `position: sticky; left: 0` with card background

**Color system:**
- Light: bg `#f4f6f9`, card `#ffffff`, border `#f1f5f9`, text `#1e293b`, dim `#94a3b8`, accent `#0ea5e9`
- Dark: bg `#0f172a`, card `#1e293b`, border `#334155`, text `#f1f5f9`, dim `#64748b`, accent `#38bdf8`
- Service colors: Perplexity `#0ea5e9`, Claude `#f59e0b`, OpenAI `#10b981`, OpenClaw `#ef4444`
- Service light backgrounds: `#e0f2fe`, `#fef3c7`, `#d1fae5`, `#fee2e2` (for icon wraps, badges, billing model tags)

**Responsive breakpoints:** 960px (2-col cards), 600px (1-col)

- [ ] **Step 1b: Research and populate new data fields**

The spec requires web research to update data to March 2026. For new fields (pros, cons, billingModel, limit, resetCycle, onExhaust), populate based on confirmed information from the reference data and spec. For any field that cannot be verified, use "— 미확인" as the value. Do NOT guess or speculate.

**Section labels to use:**
- Feature table: section-label "Billing Comparison", section-title "플랜별 기능 & 제약사항 비교표"
- Key Insights: section-label "Key Insights", section-title "과금 모델 핵심 차이" (personal) / "기업 도입 시 핵심 고려사항" (enterprise)
- Service cards: section-label "Plans & Pricing", section-title "개인용 플랜 & 장단점" (personal) / "기업용 플랜 & 장단점" (enterprise)

**Insight card icons and backgrounds:**
- Personal: ⏱ (`amber-light`), 💳 (`accent-light`), 📊 (`green-light`), 🔓 (`red-light`)
- Enterprise: 🔐 (`accent-light`), 💰 (`amber-light`), 👥 (`green-light`), 📊 (`red-light`)

DATA object — extend the existing structure from `docs/ref/index.html` with these additions per service:
- `strength`: one-line strength text (from spec)
- `billingModel`: billing model name (크레딧 소진형 / 시간 윈도우 정액제 / 메시지 카운트제 / API 종량제)
- `pros`: array of advantage strings
- `cons`: array of disadvantage strings

Per plan, add:
- `cat`: `"personal"` or `"enterprise"` (classify each of the 19 plans)
- `limit`: usage limit text (e.g., "롤링 5hr 윈도우", "160msg/3hr", "크레딧")
- `resetCycle`: reset cycle text (e.g., "5시간", "3시간", "월간")
- `onExhaust`: what happens when exhausted (e.g., "윈도우 대기", "대기/추가결제")
- `rec`: `true` for the recommended plan in each category (personal/enterprise)

Plan classification:
- **Personal:** Perplexity Free/Pro/Max, Claude Free/Pro/Max5x/Max20x, OpenAI Free/Go/Plus/Pro, OpenClaw 오픈소스
- **Enterprise:** Perplexity Ent.Pro/Ent.Max, Claude Team Std/Team Prem/Enterprise, OpenAI Business/Enterprise, OpenClaw 오픈소스 (shown in both)

INSIGHTS object — two sets of 4 cards:
- `personal`: Claude 시간 기반 정액제, Perplexity 크레딧 소진형, OpenAI 메시지 카운트제, OpenClaw API 종량제
- `enterprise`: 보안 & 컴플라이언스, 비용 구조, 팀 협업, 데이터 거버넌스

Each insight: `{ icon, bg, title, text }` (text can include `<strong>` tags)

- [ ] **Step 2: Add empty body with container div**

```html
<body>
<div class="container" id="app"></div>
<script>
// DATA, INSIGHTS defined above
let state = { tab: 'personal', filterCat: '전체' };
// render() to be implemented in next task
</script>
</body>
```

- [ ] **Step 3: Verify the page loads without errors**

Open `index.html` in browser. Should show a blank page with correct background color. Check browser console for zero errors.

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "feat: add CSS foundation and DATA object"
```

---

### Task 3: Implement render() — Header + Tabs + Feature Table

**Files:**
- Modify: `index.html` (script section)

The feature table is the most important section and goes right after the tabs. Implement the core rendering.

- [ ] **Step 1: Implement header rendering**

In the `render()` function, build the header HTML:
- Section label: "AI Agent Service Comparison · 2026.03"
- Title: "구독 요금 & 과금 모델 비교"
- Subtitle: "정액제 vs 크레딧제 — 같은 가격이라도 사용 패턴이 완전히 다릅니다"
- Theme toggle button (top-right)

- [ ] **Step 2: Implement tab switching**

- Render personal/enterprise tab buttons as **pill-style** (rounded, padding `10px 24px`, border-radius `10px`)
- Wrap in a `.main-tabs` container (background: card color, border-radius `14px`, padding `4px`, box-shadow)
- `switchTab(tab)` function: update `state.tab`, call `render()`
- Active tab: accent color background (`var(--accent)`), white text, subtle box-shadow

- [ ] **Step 3: Implement feature comparison table**

Filter plans by `state.tab` to get visible plans for the current tab. Build table:
- Column headers: service dot + name + tier + price (+ "추천" badge if `rec` for current tab)
- **사용량 제한 category first:** rows for 메시지/토큰 제한 (`limit`), 리셋 주기 (`resetCycle`), 소진 시 (`onExhaust`) — styled as `.limit-row`
- Feature rows grouped by category with `.cat-row` separators
- ✓/— rendering from `f` array
- Category filter pills (전체 + all categories from DATA.features)
- `setFilter(cat)` function: update `state.filterCat`, call `render()`

- [ ] **Step 4: Implement theme toggle**

```javascript
function toggleTheme() {
  const html = document.documentElement;
  const current = html.getAttribute('data-theme');
  const isDark = current === 'dark' || (!current && window.matchMedia('(prefers-color-scheme: dark)').matches);
  const next = isDark ? 'light' : 'dark';
  html.setAttribute('data-theme', next);
  localStorage.setItem('theme', next);
  render();
}
// On load: restore from localStorage
const saved = localStorage.getItem('theme');
if (saved) document.documentElement.setAttribute('data-theme', saved);
```

- [ ] **Step 5: Verify table renders correctly in both tabs**

Open in browser. Click personal/enterprise tabs — columns should change. Click category pills — rows should filter. Toggle theme — colors should switch. Verify no console errors.

- [ ] **Step 6: Commit**

```bash
git add index.html
git commit -m "feat: header, tabs, feature table with filters"
```

---

### Task 4: Implement Key Insights section

**Files:**
- Modify: `index.html` (add to render function, after table)

- [ ] **Step 1: Add insights rendering**

After the table section in `render()`, output the Key Insights section:
- Section label + title
- 2-column grid of 4 insight cards
- Content switches based on `state.tab` (personal vs enterprise INSIGHTS)
- Each card: icon (44px rounded bg), title (14px bold), text (12px with `<strong>` highlights)

- [ ] **Step 2: Verify both tabs show different insights**

Switch tabs — personal should show billing model differences, enterprise should show security/compliance topics.

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: key insights section with tab-aware content"
```

---

### Task 5: Implement Service Cards (Plans & Pros/Cons)

**Files:**
- Modify: `index.html` (add to render function, after insights)

- [ ] **Step 1: Add billing cards rendering**

After insights in `render()`, output the service cards section:
- Section label + title
- 4-column grid
- Each card: accent bar (3px top), icon (44px rounded), service name, subtitle, billing model tag, plan tier list (filtered by `state.tab`), divider, pros list (+ prefix, green), cons list (− prefix, red)
- Recommended plan highlighted with accent background

- [ ] **Step 2: Verify cards show correct plans per tab**

Personal tab should show individual plans, enterprise tab should show team/enterprise plans. OpenClaw appears in both.

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: service billing cards with pros/cons"
```

---

### Task 6: Implement Footer and final polish

**Files:**
- Modify: `index.html`

- [ ] **Step 1: Add footer**

After billing cards, render footer:
- "가격 정보는 2026년 3월 27일 기준 · 세금 미포함 · 엔터프라이즈 요금은 별도 문의"
- Centered, subtle text

- [ ] **Step 2: Polish and verify responsive layout**

- Resize browser to 960px → cards should be 2-col
- Resize to 600px → cards should be 1-col
- Table should scroll horizontally on small screens
- Sticky first column on table
- Hover effects on cards (translateY, shadow)

- [ ] **Step 3: Cross-check all data against docs/ref/index.html**

Verify all 19 plans are present with correct feature flags, prices, and notes. Ensure no data was lost in the migration.

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "feat: footer, responsive layout, data verification"
```

---

### Task 7: GitHub Pages deployment setup

**Files:**
- No file changes — GitHub configuration

- [ ] **Step 1: Create GitHub repository**

```bash
gh repo create ai-agent-comparison --public --source=. --remote=origin
```

(If user doesn't have `gh` CLI, guide them through github.com: New repo → ai-agent-comparison → push existing)

- [ ] **Step 2: Push to GitHub**

```bash
git push -u origin main
```

- [ ] **Step 3: Enable GitHub Pages**

```bash
gh api repos/{owner}/ai-agent-comparison/pages -X POST -f "build_type=workflow" -f "source[branch]=main" -f "source[path]=/"
```

Or guide user: Settings → Pages → Source: Deploy from branch → main → / (root) → Save

- [ ] **Step 4: Verify deployment**

Wait 1-2 minutes, then check `https://<username>.github.io/ai-agent-comparison/`. The page should load with all features working.

- [ ] **Step 5: Final commit with any deployment fixes**

If the deployment URL needs a `<base>` tag or any path adjustments, fix and commit.

```bash
git add -A
git commit -m "chore: github pages deployment"
git push
```
