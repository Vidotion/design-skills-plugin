---
name: 21st-dev
description: >
  Nachschlagewerk und Entscheidungshilfe für 21st.dev — die größte React/Tailwind-Komponentensammlung für Landing Pages und Websites. Verwende diesen Skill IMMER wenn: du eine Website-Section baust und eine fertige Komponente als Basis nutzen willst; du überlegst ob du custom bauen oder eine bestehende Komponente anpassen sollst; der User "21st.dev", "fertige Komponente", "vorgefertigte Section", "shadcn Komponente" erwähnt; du im website-builder Workflow bist und eine Section aus dem Architekturplan umsetzt (Hero, Navbar, Pricing, Testimonials, Footer, Features, CTA, Forms); du schnell eine hochwertige UI brauchst ohne alles from scratch zu coden. Triggere diesen Skill auch wenn der User nach "UI Bibliothek", "Component Library", "React Komponenten", "Landing Page Sections", "vorgefertigte Elemente" fragt. Dieser Skill ergänzt webdesign-4 (Magic UI / React Bits) als dritte Komponentenquelle — 21st.dev ist am stärksten bei kompletten Section-Layouts für Landing Pages und Websites.
---

# 21st.dev — Component Library für Vidotion Websites

## Was ist 21st.dev?

Die größte Open-Source Community-Registry für React UI-Komponenten. Basiert auf **shadcn/ui**, **Tailwind CSS** und **Radix UI**. Alle Komponenten sind TypeScript-first, copy-paste ready, und vollständig anpassbar.

**Kern-URL:** https://21st.dev

---

## Wann 21st.dev vs. andere Libraries?

```
Brauchst du eine komplette Section (Hero, Pricing, Testimonials)?
├─ Ja → 21st.dev (größte Auswahl an Section-Layouts)
└─ Nein → Brauchst du animierte Mikro-Komponenten?
          ├─ Ja → Magic UI (Framer Motion-basiert) oder React Bits
          └─ Nein → Brauchst du einzelne UI-Primitives?
                    ├─ Ja → shadcn/ui direkt
                    └─ Nein → Custom bauen
```

### Stärken-Vergleich

| Quelle | Stärke | Beste für |
|--------|--------|-----------|
| **21st.dev** | Komplette Section-Layouts, riesige Vielfalt | Heroes, Navbars, Pricing, Testimonials, Footers, Forms |
| **Magic UI** | Animierte Effekt-Komponenten | Animated Beams, Marquees, Number Tickers, Shimmer Buttons |
| **React Bits** | Minimal-Dependency Effekte | Backgrounds, Text-Animationen, Cursor-Effekte |
| **shadcn/ui** | Solide UI-Primitives | Buttons, Inputs, Dialogs, Tables, Selects |

**Faustregel:** 21st.dev für die **Struktur**, Magic UI / React Bits für den **Wow-Effekt**, shadcn/ui für die **Basics**.

---

## Komponenten-Katalog (Aktueller Stand)

### Landing Page Sections

| Kategorie | Anzahl | URL | Typische Anwendung |
|-----------|--------|-----|-------------------|
| **Heroes** | 73+ | `/s/hero` | Startbereich, Above the Fold |
| **Navigation Menus** | 43+ | `/s/navbar` | Header, Mobile Nav |
| **Pricing Sections** | 17+ | `/s/pricing-section` | Preistabellen, Plan-Vergleiche |
| **Testimonials** | 42+ | `/s/testimonials` | Kundenstimmen, Social Proof |
| **Scroll Areas** | 24+ | `/s/scroll-area` | Parallax, Smooth Scroll Sections |
| **Shaders** | 15+ | `/s/shader` | WebGL Hintergründe, Effekte |
| **Images** | 26+ | `/s/image` | Galerien, Bild-Layouts |
| **Videos** | 9+ | `/s/video` | Video-Hintergründe, Player |
| **Texts** | 58+ | `/s/text` | Animierte Headlines, Typografie |
| **Hooks** | 31+ | `/s/hook` | Wiederverwendbare Logik |
| **Maps** | 2+ | `/s/map` | Standort-Anzeigen |

### UI Components

| Kategorie | Anzahl | Typische Anwendung |
|-----------|--------|--------------------|
| **Buttons** | 130+ | CTAs, Actions, Toggles |
| **Cards** | 79+ | Feature-Cards, Bento Grids, Produkt-Cards |
| **Inputs** | 102+ | Form-Felder, Search, OTP |
| **Accordions** | 40+ | FAQs, Collapsible Content |
| **Selects** | 62+ | Dropdowns, Multi-Select |
| **Dialogs / Modals** | 37+ | Popups, Confirmations, Lightbox |
| **Tabs** | 38+ | Content-Switcher, Feature-Tabs |
| **Tables** | 30+ | Daten-Tabellen, Vergleiche |
| **Sliders** | 45+ | Range, Carousels |
| **Forms** | 23+ | Kontakt, Newsletter, Login |
| **Carousels** | 16+ | Bild-Slider, Testimonial-Slider |
| **Tooltips** | 28+ | Hilfe-Texte, Info-Popups |
| **Calendars** | 34+ | Termin-Buchung, Datepicker |
| **Badges** | 25+ | Status, Tags, Labels |
| **Spinner / Loaders** | 21+ | Loading States |
| **Popovers** | 23+ | Kontext-Menüs, Info-Panels |
| **AI Chats** | 30+ | Chat-Interfaces, Chatbots |
| **Sign Ins / Sign Ups** | 8+ | Auth-Formulare |
| **Sidebars** | 10+ | Dashboard-Navigation |
| **Alerts** | 23+ | Benachrichtigungen, Warnungen |
| **Avatars** | 17+ | Profilbilder, User-Icons |
| **Checkboxes** | 19+ | Formulare, Filter |
| **Paginations** | 20+ | Seitennavigation |
| **Radio Groups** | 22+ | Auswahl-Formulare |
| **Toggles** | 12+ | On/Off-Schalter |

---

## Installation & Nutzung

### Installation per CLI (empfohlen)

```bash
npx shadcn@latest add "https://21st.dev/r/{author}/{component-name}"
```

Beispiel:
```bash
npx shadcn@latest add "https://21st.dev/r/shadcn/accordion"
```

### Voraussetzungen im Projekt

- React 18+
- Tailwind CSS v3 oder v4
- TypeScript (empfohlen)
- shadcn/ui Projekt-Setup (cn() Utility, CSS Variables)

### Tailwind v4 CSS Basis (wenn neues Projekt)

```css
@import "tailwindcss";
@import "tw-animate-css";

@custom-variant dark (&:is(.dark *));

@theme inline {
  --radius-sm: calc(var(--radius) - 4px);
  --radius-md: calc(var(--radius) - 2px);
  --radius-lg: var(--radius);
  --radius-xl: calc(var(--radius) + 4px);
  --color-background: var(--background);
  --color-foreground: var(--foreground);
  --color-card: var(--card);
  --color-card-foreground: var(--card-foreground);
  --color-popover: var(--popover);
  --color-popover-foreground: var(--popover-foreground);
  /* ... weitere Theme-Variablen nach Bedarf */
}
```

---

## Magic MCP Server (für IDE-Integration)

21st.dev bietet einen MCP Server für die direkte Nutzung in Cursor, Windsurf, VS Code und Claude Code.

### Setup

1. API Key generieren: https://21st.dev/magic
2. Installation:

```bash
npx @21st-dev/cli@latest install <client> --api-key <key>
```

Unterstützte Clients: `cursor`, `windsurf`, `cline`, `claude`

### Manuelle Konfiguration

```json
{
  "mcpServers": {
    "@21st-dev/magic": {
      "command": "npx",
      "args": ["-y", "@21st-dev/magic@latest"],
      "env": {
        "API_KEY": "dein-api-key"
      }
    }
  }
}
```

### Magic Features

- `/ui` Befehl: Beschreibe eine Komponente in natürlicher Sprache → mehrere Style-Varianten werden generiert
- SVGL Integration: Zugriff auf Firmen-Logos und Brand-Assets
- Komponenten werden automatisch in dein Projekt eingefügt
- Alle generierten Komponenten sind vollständig editierbar

---

## Integration in den Vidotion Website-Builder

### Wo im Workflow?

Dieser Skill wird in **Phase 2 (Section für Section bauen)** des website-builder Skills aktiv.

### Entscheidung pro Section

Für jede Section im Architekturplan:

```
1. Prüfe: Gibt es eine passende 21st.dev-Komponente?
   ├─ Browse: https://21st.dev/s/{kategorie}
   └─ Oder: /ui Befehl im MCP

2. Bewerte den Fit:
   ├─ Stil passt zum Brief → Als Basis nehmen, Farben/Fonts/Spacing anpassen
   ├─ Struktur passt, Stil nicht → Struktur übernehmen, komplett re-stylen
   ├─ Teilweise passend → Einzelne Elemente extrahieren, in Custom-Section einbauen
   └─ Nichts passt → Custom bauen mit Magic UI / React Bits / from scratch

3. Anpassung:
   ├─ CSS Custom Properties aus Globals übernehmen
   ├─ Fonts durch Projekt-Fonts ersetzen
   ├─ Farben durch Brief-Palette ersetzen
   ├─ Spacing an Design-System anpassen
   └─ Animationen mit GSAP/Motion ergänzen oder ersetzen
```

### Typisches Mapping: Brief-Section → 21st.dev

| Brief-Section | 21st.dev Suche | Anpassungsaufwand |
|---------------|---------------|-------------------|
| **Navigation** | `/s/navbar` | Niedrig — Logo, Links, Farben tauschen |
| **Hero** | `/s/hero` | Mittel — Text, CTA, ggf. Animation ersetzen |
| **Features / Services** | `/s/card` + Bento-Layout | Mittel — Icons, Text, Grid anpassen |
| **Über uns / Team** | `/s/testimonials` oder `/s/card` | Mittel — Bilder, Text, Layout |
| **Pricing** | `/s/pricing-section` | Niedrig — Pläne, Preise, Features eintragen |
| **Testimonials** | `/s/testimonials` | Niedrig — Zitate, Namen, Bilder tauschen |
| **FAQ** | `/s/accordion` | Niedrig — Fragen und Antworten einsetzen |
| **Kontakt** | `/s/form` + `/s/input` | Mittel — Felder, Validierung, Styling |
| **CTA-Banner** | `/s/hero` (kleinere Varianten) | Niedrig — Text und Button anpassen |
| **Footer** | `/s/navbar` (Footer-Varianten) | Niedrig — Links, Social Icons, Copyright |
| **Blog / News** | `/s/card` | Mittel — Card-Layout, Datum, Kategorien |
| **Galerie** | `/s/image` + `/s/carousel` | Mittel — Bilder, Lightbox, Grid |
| **Login / Auth** | `/s/sign-in` + `/s/sign-up` | Niedrig — Felder, Branding anpassen |
| **Dashboard** | `/s/sidebar` + `/s/table` | Hoch — Daten, Charts, Navigation |

---

## Best Practices

### Do's
- **Immer den Brief als Referenz behalten** — 21st.dev liefert die Struktur, der Brief bestimmt den Stil
- **CSS Variables nutzen** — Nie Farben/Fonts hardcoden, immer über Projekt-Variablen
- **Responsive prüfen** — Die meisten 21st.dev-Komponenten sind responsive, aber immer testen
- **Nur nehmen was du brauchst** — Keine ganze Komponente importieren wenn du nur ein Element brauchst
- **Dependencies prüfen** — Manche Komponenten haben zusätzliche npm-Abhängigkeiten

### Don'ts
- **Nicht unverändert einsetzen** — Jede Komponente MUSS ans Projekt-Design angepasst werden
- **Nicht zu viele verschiedene Komponenten mischen** — Max 2-3 verschiedene Autoren pro Projekt für konsistenten Stil
- **Nicht bei komplexen Custom-Animationen** — Wenn der Brief spezifische GSAP-Animationen vorgibt, lieber custom bauen
- **Nicht für hochspezifische Layouts** — Wenn der Brief ein sehr spezifisches Layout beschreibt, ist custom oft schneller als Anpassung

---

## Qualitäts-Check nach Einbau

Nach dem Einbau einer 21st.dev-Komponente immer prüfen:

- [ ] Farben durch Projekt-Palette ersetzt?
- [ ] Fonts durch Projekt-Typografie ersetzt?
- [ ] Spacing konsistent mit restlichem Design?
- [ ] Responsive auf allen Breakpoints?
- [ ] Animationen passen zum Projekt-Stil?
- [ ] Keine unnötigen Dependencies mitgezogen?
- [ ] Dark Mode berücksichtigt (falls relevant)?
- [ ] Accessibility: Alt-Texte, Keyboard-Nav, Kontraste?
- [ ] Performance: Lazy Loading, Code-Splitting wo nötig?
