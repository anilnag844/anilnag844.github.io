# Portfolio Improvement Prompt
> Paste this into GitHub Copilot Chat / Claude in VS Code and run it against `index.html`

---

## Context

This is a single-file static portfolio at `index.html` for Anil Kumar Nag — a Senior SDET and QA AI/ML Architect. Several improvements have already been made (SEO meta, typewriter phrases, open-to-work badge, skill icons). The remaining issues below still need fixing.

---

## Fix 1 — Hero lede still says "Principal QE Architect" (not updated)

In the `<p class="lede">` paragraph (around line 213), change:

```
A <strong>Principal QE Architect</strong> leading AI-Driven Lifecycle
```

To:

```
A <strong>Senior SDET &amp; QA AI/ML Architect</strong> currently serving as Principal QE Architect, leading AI-Driven Lifecycle
```

---

## Fix 2 — Work section stack tags are missing icons

The `#work` section has a `.stack` div with `.tag` spans. These are currently plain text. The existing JavaScript `map` object (in the `<script>` tag) already handles icon injection for `.tag` elements — but `Playwright v1.52`, `TypeScript v5.4`, `Node.js 24.x`, `Claude AI`, `MCP Servers`, `GitHub Actions`, `AWS Bedrock` need to be in the map. Verify these slugs are present in the `map` object:

```js
'Playwright v1.52': 'playwright',
'TypeScript v5.4': 'typescript',
'Node.js 24.x': 'nodedotjs',
'Claude AI': 'anthropic',
'AWS Bedrock': 'amazonaws',
'GitHub Actions': 'githubactions',
'Cline Skills': null,   // use emojiMap: '🤖'
'AI-DLC': null,         // use emojiMap: '🧠'
'Jira + Figma MCP': null, // use emojiMap: '🔌'
```

They should already be there — just verify and add any that are missing.

---

## Fix 3 — Reveal animation causes large blank gaps between sections

The current IntersectionObserver code is:

```js
const io = new IntersectionObserver((entries) => {
  entries.forEach((e, i) => {
    if (e.isIntersecting) {
      setTimeout(() => e.target.classList.add('in'), i * 55);
      io.unobserve(e.target);
    }
  });
}, { threshold: .12 });
```

Two problems:
1. `i * 55` — the `i` index can be large when many elements trigger at once, causing the last elements to wait up to 1+ seconds to appear, leaving visible blank white space.
2. `threshold: .12` — elements only trigger when 12% is visible, which is too late for tall elements.

**Fix:**

```js
const io = new IntersectionObserver((entries) => {
  entries.forEach((e, i) => {
    if (e.isIntersecting) {
      setTimeout(() => e.target.classList.add('in'), Math.min(i * 55, 220));
      io.unobserve(e.target);
    }
  });
}, { threshold: 0.05, rootMargin: '0px 0px -40px 0px' });
```

Changes: cap stagger at 220ms max, lower threshold to 0.05, add rootMargin to trigger slightly before element enters viewport.

---

## Fix 4 — Add client logo badges to Wipro sub-roles

Inside the Wipro `.company` block, each `.role` has a `.role-client` span (e.g., `· MGM Resorts`, `· McGraw-Hill`, `· Quest Diagnostics`). Add small inline favicon images before the client name text using Google's favicon service:

```html
<!-- MGM Resorts role-client -->
<span class="role-client">
  &middot; <img src="https://www.google.com/s2/favicons?domain=mgmresorts.com&sz=16" width="14" height="14" style="vertical-align:middle;border-radius:3px;margin-right:3px" alt="">MGM Resorts
</span>

<!-- McGraw-Hill role-client -->
<span class="role-client">
  &middot; <img src="https://www.google.com/s2/favicons?domain=mheducation.com&sz=16" width="14" height="14" style="vertical-align:middle;border-radius:3px;margin-right:3px" alt="">McGraw-Hill (EdTech)
</span>

<!-- Quest Diagnostics role-client -->
<span class="role-client">
  &middot; <img src="https://www.google.com/s2/favicons?domain=questdiagnostics.com&sz=16" width="14" height="14" style="vertical-align:middle;border-radius:3px;margin-right:3px" alt="">Quest Diagnostics
</span>
```

---

## Fix 5 — Writing article cards all point to the same LinkedIn profile URL

Each `.article-card` `href` is `https://www.linkedin.com/in/anilkumarnag/`. If the actual LinkedIn article URLs exist, replace them. If not, at minimum add a `data-article` attribute and update the CTA text to make it clearer these are LinkedIn posts:

```html
<div class="article-cta">View on LinkedIn &#8599;</div>
```

Also add `title` attributes to each card so hover shows the article name.

---

## Fix 6 — Add `twitter:title` and `twitter:description` meta tags

Currently only `twitter:card` and `twitter:image` exist. Add:

```html
<meta name="twitter:title" content="Anil Kumar Nag | SDET &amp; QA AI/ML Architect">
<meta name="twitter:description" content="Senior SDET &amp; QA AI/ML Architect. 12+ yrs · Playwright · AWS Bedrock · Claude · MCP · Las Vegas NV">
```

---

## Fix 7 — Footer copyright year is dynamic but missing structured data

Add a minimal JSON-LD `Person` schema just before `</body>` for better Google indexing:

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "Person",
  "name": "Anil Kumar Nag",
  "jobTitle": "SDET & QA AI/ML Architect",
  "url": "https://anilnag844.github.io/",
  "sameAs": [
    "https://www.linkedin.com/in/anilkumarnag/",
    "https://github.com/anilnag844"
  ],
  "knowsAbout": ["SDET", "QA Automation", "AI Testing", "Playwright", "AWS Bedrock", "Test Architecture"],
  "worksFor": {
    "@type": "Organization",
    "name": "Wipro Limited"
  },
  "address": {
    "@type": "PostalAddress",
    "addressLocality": "Las Vegas",
    "addressRegion": "NV",
    "addressCountry": "US"
  }
}
</script>
```

---

## Summary of all 7 fixes

| # | Fix | Impact |
|---|-----|--------|
| 1 | Hero lede copy — add SDET title | Recruiter first impression |
| 2 | Work section stack tag icons | Visual consistency |
| 3 | Reveal animation blank gaps | UX / scroll feel |
| 4 | Client favicon badges in roles | Visual richness |
| 5 | Writing cards — improve CTAs | Credibility |
| 6 | Twitter meta tags | Social sharing |
| 7 | JSON-LD Person schema | Google indexing / SEO |
