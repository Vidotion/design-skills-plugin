---
name: webdesign-3
description: Review UI code for Web Interface Guidelines compliance. Use when asked to "review my UI", "check accessibility", "audit design", "review UX", "UI-Review & Audit", or "check my site against best practices". Wird im website-builder Workflow am Ende jeder Section und in Phase Assembly & Feinschliff für den finalen UI-Audit eingesetzt.
metadata:
  author: vercel
  version: "1.0.0"
  argument-hint: <file-or-pattern>
---

# Web Interface Guidelines — UI-Review & Audit

Review files for compliance with Web Interface Guidelines.

## How It Works

1. Fetch the latest guidelines from the source URL below
2. Read the specified files (or prompt user for files/pattern)
3. Check against all rules in the fetched guidelines
4. Output findings in the terse `file:line` format

## Guidelines Source

Fetch fresh guidelines before each review:

```
https://raw.githubusercontent.com/vercel-labs/web-interface-guidelines/main/command.md
```

Use WebFetch to retrieve the latest rules. The fetched content contains all the rules and output format instructions.

## Usage

When a user provides a file or pattern argument:
1. Fetch guidelines from the source URL above
2. Read the specified files
3. Apply all rules from the fetched guidelines
4. Output findings using the format specified in the guidelines

If no files specified, ask the user which files to review.

## ⚠️ Fallback — wenn die GitHub-URL nicht erreichbar ist

Falls der WebFetch-Aufruf fehlschlägt (404, Timeout, kein Netzwerk), führe den Audit trotzdem durch — mit diesen eingebetteten Kern-Regeln:

### Accessibility
- Alle `<img>` haben `alt`-Attribut (nicht leer außer bei dekorativem Bild)
- Farbkontraste: Text auf Hintergrund mind. 4.5:1 (WCAG AA), große Schrift mind. 3:1
- Interaktive Elemente (Links, Buttons) haben sichtbaren Fokus-Stil
- Keine Information wird ausschließlich über Farbe vermittelt
- `<button>` nicht für Navigation, `<a>` nicht für Aktionen

### Semantik & Struktur
- Nur eine `<h1>` pro Seite
- Heading-Hierarchie logisch (kein Sprung von h1 auf h3)
- Landmarks vorhanden: `<header>`, `<main>`, `<footer>`, `<nav>`
- Listen (`<ul>`/`<ol>`) für Aufzählungen, keine `<div>`-Stacks

### Performance
- Bilder haben `width` und `height` Attribute (verhindert CLS)
- `loading="lazy"` bei Bildern unterhalb des Folds
- Keine inline `style`-Blöcke mit duplizierten Werten — CSS-Variablen nutzen
- Keine `@import` in CSS (blockiert Rendering)

### Interaktion & UX
- Hover-States auf allen klickbaren Elementen
- Touch-Targets mind. 44×44px
- Kein `cursor: pointer` auf nicht-interaktiven Elementen
- Formulare: jedes `<input>` hat ein assoziiertes `<label>`

### Vidotion-spezifisch
- Keine generischen AI-Ästhetiken: kein Inter+lila Gradient als Default
- CSS Custom Properties aus Globals genutzt (keine hardcodierten Hex-Werte im Component-Code)
- Animationen respektieren `prefers-reduced-motion`
- Mobile-First: kein ungeplantes Overflow auf < 375px

### Output-Format (terse)
```
src/components/Hero.jsx:42  img missing alt attribute
src/styles/main.css:18      hardcoded color #6B4FFF — use CSS variable
src/components/Nav.jsx:7    missing focus style on interactive element
```
