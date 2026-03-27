# Astro Migration Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Migrate Atom Research portal from raw HTML to Astro with shared layouts, components, and a single CSS design system.

**Architecture:** Astro static site generator with component-based layouts. Each research topic becomes a set of `.astro` pages sharing `Research.astro` layout. Design system extracted to `global.css` from `ai-agent/summary.html`. GitHub Actions deploys to GitHub Pages.

**Tech Stack:** Astro 5.x, CSS (no framework), GitHub Actions, GitHub Pages

---

## File Structure

### New files to create:
```
package.json
astro.config.mjs
tsconfig.json
src/styles/global.css
src/layouts/Base.astro
src/layouts/Research.astro
src/layouts/Portal.astro
src/components/Nav.astro
src/components/ThemeToggle.astro
src/components/Section.astro
src/pages/index.astro
src/pages/ai-agent/index.astro
src/pages/ai-agent/summary.astro
src/pages/ai-agent/insights.astro
src/pages/tts/index.astro
src/pages/tts/summary.astro
src/pages/tts/insights.astro
src/pages/llm-comparison/index.astro
src/pages/llm-comparison/specs.astro
src/pages/llm-comparison/usecase.astro
src/pages/llm-comparison/deploy.astro
src/pages/llm-comparison/summary.astro
src/pages/llm-comparison/insights.astro
.github/workflows/deploy.yml
```

### Files to delete after migration:
```
index.html
ai-agent/index.html
ai-agent/summary.html
ai-agent/insights.html
tts/index.html
tts/summary.html
tts/insights.html
llm-comparison/index.html
llm-comparison/specs.html
llm-comparison/usecase.html
llm-comparison/deploy.html
llm-comparison/summary.html
llm-comparison/insights.html
```

### Files to modify:
```
.gitignore  — add node_modules/, dist/
```

---

### Task 1: Astro 프로젝트 초기화

**Files:**
- Create: `package.json`
- Create: `astro.config.mjs`
- Create: `tsconfig.json`
- Modify: `.gitignore`

- [ ] **Step 1: Initialize Astro project**

```bash
cd /Users/zpro/02_dev/01_workspace/etc/ai-agent-comparison
npm create astro@latest . -- --template minimal --no-install --no-git
```

If interactive prompt blocks, create manually:

```json
// package.json
{
  "name": "atom-research",
  "type": "module",
  "scripts": {
    "dev": "astro dev",
    "build": "astro build",
    "preview": "astro preview"
  },
  "dependencies": {
    "astro": "^5.0.0"
  }
}
```

- [ ] **Step 2: Configure Astro for GitHub Pages**

```js
// astro.config.mjs
import { defineConfig } from 'astro/config';

export default defineConfig({
  site: 'https://solution-ai-agent.github.io',
  base: '/atom-research',
  output: 'static',
});
```

```json
// tsconfig.json
{
  "extends": "astro/tsconfigs/strict"
}
```

- [ ] **Step 3: Update .gitignore**

Add to `.gitignore`:
```
node_modules/
dist/
.astro/
```

- [ ] **Step 4: Install dependencies**

```bash
npm install
```

- [ ] **Step 5: Verify Astro runs**

```bash
npx astro build
```

Expected: Build succeeds (may warn about no pages yet).

- [ ] **Step 6: Commit**

```bash
git add package.json package-lock.json astro.config.mjs tsconfig.json .gitignore
git commit -m "feat: initialize Astro project"
```

---

### Task 2: global.css 디자인 시스템 추출

**Files:**
- Create: `src/styles/global.css`

Extract the ENTIRE CSS from `ai-agent/summary.html` (lines 11-162) into a single file. This is the canonical design system.

- [ ] **Step 1: Create global.css**

Extract CSS from `ai-agent/summary.html`. Include ALL classes: variables, reset, `.c`, `.avg`, `.hdr`, `.sec`, `.hero-grid`, `.bill-grid`, `.bench`, `.usage-grid`, `.arch-grid`, `.take`, `.bottom`, `.theme-btn`, bar chart classes, and the 768px mobile breakpoint. Also include any additional classes needed across pages (`.bar-wrap`, `.bar-row`, `.bar-label`, `.bar-track`, `.bar-fill`, `.bar-val`, `.bench-scroll`, `.warn-card`).

- [ ] **Step 2: Verify file is complete**

The file should contain all shared CSS — no page should need to define its own base styles.

- [ ] **Step 3: Commit**

```bash
git add src/styles/global.css
git commit -m "feat: extract global.css design system from AI Agent reference"
```

---

### Task 3: Base 레이아웃 + 핵심 컴포넌트

**Files:**
- Create: `src/layouts/Base.astro`
- Create: `src/components/ThemeToggle.astro`
- Create: `src/components/Nav.astro`
- Create: `src/components/Section.astro`

- [ ] **Step 1: Create ThemeToggle.astro**

```astro
---
// src/components/ThemeToggle.astro
// No props needed
---
<button class="theme-btn" id="themeBtn">Light / Dark</button>
<script>
  const btn = document.getElementById('themeBtn');
  btn?.addEventListener('click', () => {
    const h = document.documentElement;
    const c = h.getAttribute('data-theme');
    const d = c === 'dark' || (!c && matchMedia('(prefers-color-scheme:dark)').matches);
    const n = d ? 'light' : 'dark';
    h.setAttribute('data-theme', n);
    localStorage.setItem('theme', n);
  });
  const s = localStorage.getItem('theme');
  if (s) document.documentElement.setAttribute('data-theme', s);
</script>
```

- [ ] **Step 2: Create Nav.astro**

```astro
---
// src/components/Nav.astro
interface Props {
  links: { href: string; label: string }[];
}
const { links } = Astro.props;
const allLinks = [{ href: `${import.meta.env.BASE_URL}`, label: 'Atom Research' }, ...links];
---
<div style="display:flex;gap:8px;margin-bottom:32px;flex-wrap:wrap">
  {allLinks.map(link => (
    <a href={link.href} style="padding:10px 18px;border-radius:8px;border:1px solid var(--border2);background:var(--card);color:var(--text);font-size:13px;font-weight:700;text-decoration:none">{link.label}</a>
  ))}
</div>
```

- [ ] **Step 3: Create Section.astro**

```astro
---
// src/components/Section.astro
interface Props {
  label: string;
  title: string;
}
const { label, title } = Astro.props;
---
<div class="sec">
  <div class="sec-label">{label}</div>
  <div class="sec-title">{title}</div>
  <slot />
</div>
```

- [ ] **Step 4: Create Base.astro**

```astro
---
// src/layouts/Base.astro
import '../styles/global.css';
import ThemeToggle from '../components/ThemeToggle.astro';

interface Props {
  title: string;
  description?: string;
}
const { title, description = '' } = Astro.props;
---
<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>{title}</title>
  {description && <meta name="description" content={description} />}
  <link rel="preconnect" href="https://cdn.jsdelivr.net">
  <link rel="stylesheet" href="https://cdn.jsdelivr.net/gh/orioncactus/pretendard/dist/web/static/pretendard.css">
</head>
<body>
  <ThemeToggle />
  <slot />
</body>
</html>
```

- [ ] **Step 5: Commit**

```bash
git add src/layouts/Base.astro src/components/
git commit -m "feat: add Base layout and core components (Nav, ThemeToggle, Section)"
```

---

### Task 4: Research + Portal 레이아웃

**Files:**
- Create: `src/layouts/Research.astro`
- Create: `src/layouts/Portal.astro`

- [ ] **Step 1: Create Research.astro**

```astro
---
// src/layouts/Research.astro
import Base from './Base.astro';
import Nav from '../components/Nav.astro';

interface Props {
  title: string;
  label: string;
  sub: string;
  meta?: string;
  description?: string;
  navLinks: { href: string; label: string }[];
}
const { title, label, sub, meta, description, navLinks } = Astro.props;
---
<Base title={title} description={description}>
  <div class="c">
    <div class="hdr">
      <div class="hdr-label">{label}</div>
      <div class="hdr-title" set:html={title} />
      <div class="hdr-sub">{sub}</div>
      {meta && <div class="hdr-meta">{meta}</div>}
    </div>
    <Nav links={navLinks} />
    <slot />
    <div class="bottom">
      <div class="bottom-left">Atom Research · {meta || ''}</div>
    </div>
  </div>
</Base>
```

- [ ] **Step 2: Create Portal.astro**

```astro
---
// src/layouts/Portal.astro
import Base from './Base.astro';

interface Props {
  title: string;
  description?: string;
}
const { title, description } = Astro.props;
---
<Base title={title} description={description}>
  <div class="c">
    <slot />
  </div>
</Base>
```

- [ ] **Step 3: Commit**

```bash
git add src/layouts/Research.astro src/layouts/Portal.astro
git commit -m "feat: add Research and Portal layouts"
```

---

### Task 5: 포털 메인 이전

**Files:**
- Create: `src/pages/index.astro`
- Reference: `index.html`

- [ ] **Step 1: Create portal index.astro**

Read `index.html` and extract the `<body>` content into `src/pages/index.astro`, using `Portal.astro` layout. Keep the research-grid cards markup. Replace hardcoded URLs with `{import.meta.env.BASE_URL}` prefix where needed.

- [ ] **Step 2: Verify build**

```bash
npx astro build && ls dist/index.html
```

- [ ] **Step 3: Commit**

```bash
git add src/pages/index.astro
git commit -m "feat: migrate portal index to Astro"
```

---

### Task 6: TTS 3페이지 이전 (검증)

**Files:**
- Create: `src/pages/tts/index.astro`
- Create: `src/pages/tts/summary.astro`
- Create: `src/pages/tts/insights.astro`
- Reference: `tts/index.html`, `tts/summary.html`, `tts/insights.html`

- [ ] **Step 1: Migrate tts/index.astro**

Read `tts/index.html`. Extract `<body>` content (everything between nav and footer). Wrap in `Research.astro` layout with appropriate props. Remove duplicated CSS (now in global.css). If page-specific CSS exists (e.g., `.oss-grid`, `.nr-grid`), add it in a `<style>` block within the page.

navLinks for TTS pages:
```js
[
  { href: 'summary', label: 'Executive Summary' },
  { href: 'insights', label: 'TCO 분석' },
]
```

- [ ] **Step 2: Migrate tts/summary.astro and tts/insights.astro**

Same approach: extract body content, use Research.astro layout, page-specific CSS in `<style>`.

- [ ] **Step 3: Build and verify**

```bash
npx astro build
ls dist/tts/index.html dist/tts/summary/index.html dist/tts/insights/index.html
```

- [ ] **Step 4: Visual verification**

```bash
npx astro preview
```

Open browser at `http://localhost:4321/atom-research/tts/` — verify layout matches the old pages.

- [ ] **Step 5: Commit**

```bash
git add src/pages/tts/
git commit -m "feat: migrate TTS pages to Astro"
```

---

### Task 7: LLM 비교 6페이지 이전

**Files:**
- Create: `src/pages/llm-comparison/index.astro`
- Create: `src/pages/llm-comparison/specs.astro`
- Create: `src/pages/llm-comparison/usecase.astro`
- Create: `src/pages/llm-comparison/deploy.astro`
- Create: `src/pages/llm-comparison/summary.astro`
- Create: `src/pages/llm-comparison/insights.astro`
- Reference: `llm-comparison/*.html`

- [ ] **Step 1: Define shared navLinks for LLM pages**

```js
const llmNav = [
  { href: './', label: '메인' },
  { href: 'specs', label: '모델 스펙' },
  { href: 'usecase', label: '용도별 비교' },
  { href: 'deploy', label: '배포 옵션' },
  { href: 'summary', label: '요약' },
  { href: 'insights', label: '인사이트' },
];
```

- [ ] **Step 2: Migrate all 6 pages**

For each page: read the HTML source, extract body content (between nav and footer), wrap in Research.astro with navLinks. Page-specific CSS in `<style>` block.

Process pages in this order: index → specs → usecase → deploy → summary → insights.

- [ ] **Step 3: Build and verify**

```bash
npx astro build
ls dist/llm-comparison/
```

Should contain: `index.html`, `specs/index.html`, `usecase/index.html`, `deploy/index.html`, `summary/index.html`, `insights/index.html`

- [ ] **Step 4: Visual verification**

```bash
npx astro preview
```

Check all 6 pages in browser. Verify navigation links work between pages.

- [ ] **Step 5: Commit**

```bash
git add src/pages/llm-comparison/
git commit -m "feat: migrate LLM comparison pages to Astro"
```

---

### Task 8: AI Agent 3페이지 이전

**Files:**
- Create: `src/pages/ai-agent/index.astro`
- Create: `src/pages/ai-agent/summary.astro`
- Create: `src/pages/ai-agent/insights.astro`
- Reference: `ai-agent/index.html`, `ai-agent/summary.html`, `ai-agent/insights.html`

- [ ] **Step 1: Migrate ai-agent/summary.astro and insights.astro**

These are static HTML — straightforward migration like TTS/LLM pages.

- [ ] **Step 2: Migrate ai-agent/index.astro (JS rendering)**

`ai-agent/index.html` renders via JS with a `DATA` object and `render()` function. Convert to Astro:
- Move `DATA` and `INSIGHTS` objects to the frontmatter (`---` block)
- Convert the JS `render()` template logic to Astro template expressions
- Use `{data.map(...)}` instead of JS string concatenation
- Page-specific CSS (`.main-tabs`, `.billing-grid`, `.billing-card`, `.agent-card` etc.) goes in `<style>` block
- The tab filtering logic needs `<script>` for client-side interactivity

- [ ] **Step 3: Build and verify**

```bash
npx astro build
ls dist/ai-agent/
```

- [ ] **Step 4: Visual verification**

Verify tabs, filtering, and all interactive elements work in browser.

- [ ] **Step 5: Commit**

```bash
git add src/pages/ai-agent/
git commit -m "feat: migrate AI Agent pages to Astro (JS→Astro conversion)"
```

---

### Task 9: GitHub Actions 배포 설정

**Files:**
- Create: `.github/workflows/deploy.yml`

- [ ] **Step 1: Create deploy workflow**

```yaml
# .github/workflows/deploy.yml
name: Deploy to GitHub Pages

on:
  push:
    branches: [main]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: pages
  cancel-in-progress: false

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: npm
      - run: npm ci
      - run: npm run build
      - uses: actions/upload-pages-artifact@v3
        with:
          path: dist

  deploy:
    needs: build
    runs-on: ubuntu-latest
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - id: deployment
        uses: actions/deploy-pages@v4
```

- [ ] **Step 2: Commit**

```bash
git add .github/workflows/deploy.yml
git commit -m "feat: add GitHub Actions deploy workflow for Astro"
```

---

### Task 10: 기존 HTML 제거 + 최종 배포

**Files:**
- Delete: `index.html`, `ai-agent/`, `tts/`, `llm-comparison/`
- Modify: `README.md` (optional — update tech stack)

- [ ] **Step 1: Final local build verification**

```bash
npx astro build
npx astro preview
```

Check ALL pages in browser:
- Portal: `/atom-research/`
- AI Agent: `/atom-research/ai-agent/`, `/atom-research/ai-agent/summary/`, `/atom-research/ai-agent/insights/`
- TTS: `/atom-research/tts/`, `/atom-research/tts/summary/`, `/atom-research/tts/insights/`
- LLM: `/atom-research/llm-comparison/` and all 5 sub-pages

- [ ] **Step 2: Remove old HTML files**

```bash
rm index.html
rm -rf ai-agent/ tts/ llm-comparison/
```

- [ ] **Step 3: Update .gitignore if needed**

Ensure `dist/`, `node_modules/`, `.astro/` are ignored.

- [ ] **Step 4: Commit and push**

```bash
git add -A
git commit -m "feat: complete Astro migration, remove legacy HTML"
git push
```

- [ ] **Step 5: Switch GitHub Pages source**

GitHub Pages needs to be switched from "Deploy from branch" to "GitHub Actions":

```bash
gh api repos/Solution-AI-Agent/atom-research/pages -X PUT -f build_type=workflow
```

- [ ] **Step 6: Verify deployment**

```bash
gh run list --limit 1
```

Wait for the workflow to complete, then verify the live site at `https://solution-ai-agent.github.io/atom-research/`.
