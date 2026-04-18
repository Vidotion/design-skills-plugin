---
name: scroll-frame-sequence
description: >
  Canvas + Image Sequence scroll-driven video effect — the premium approach used by Apple, Active Theory,
  and high-end brands. IMMER verwenden wenn: User "Scroll Video", "Frame Sequence", "Image Sequence",
  "Scroll-driven Video", "wie Apple", "Canvas Scroll Animation", "Scroll-to-play", "Hero Animation
  beim Scrollen", "Video Scrub", "Frames abspielen" sagt; Hero-Section mit scroll-gesteuerter Animation
  gebaut wird; Active Theory oder Apple Product Pages als Referenz genannt werden; im website-builder
  eine animierte Hero-Section geplant ist. Covers: Preloading, Progressive Loading, Responsive Frames,
  Mobile-Fallbacks, Overlay-Choreographie, React Integration, ffmpeg Export-Workflows.
  Ergänzt scroll-animation-3 als spezialisierte Frame-Sequence-Implementierung.
---

# Scroll Frame Sequence — Canvas Image Sequence

## Overview

Scroll-driven Frame Sequences sind der **Industry-Standard** für Premium-Scroll-Experiences. Statt ein Video beim Scrollen abzuspielen (ruckelig, inkonsistent), werden **vorgerenderte Einzelframes** auf ein Canvas-Element gezeichnet — gesteuert durch die Scroll-Position via GSAP ScrollTrigger.

**Wer nutzt das:** Apple (AirPods, iPhone, MacBook Seiten), Active Theory, Google Pixel, Tesla, Nike, und praktisch jede Premium-Brand.

**Warum Canvas statt Video-Scrub:**
- Pixelgenaue Kontrolle — kein Browser-Decoding
- Konsistent smooth auf Desktop UND Mobile
- Keine Safari/iOS Video-Seeking-Probleme
- Volle Kontrolle über Qualität und Timing

---

## Architecture

```
Hero Section (height: 400-600vh)
├── Sticky Container (100vh, position: sticky)
│   ├── Canvas (fullscreen, z-index: 0)
│   ├── Overlay Content (text, CTAs — z-index: 1)
│   └── Optional: Progress Indicator
└── ScrollTrigger binds scroll → frame index
```

---

## Complete Implementation

### 1. HTML Structure

```html
<section class="frame-sequence-hero" id="heroSequence">
  <!-- Sticky wrapper keeps canvas in viewport -->
  <div class="frame-sequence-sticky">
    <canvas id="frameCanvas"></canvas>
    
    <!-- Overlay content appears/disappears during scroll -->
    <div class="frame-overlay frame-overlay--intro">
      <h1>Headline</h1>
      <p>Subtext</p>
    </div>
    
    <div class="frame-overlay frame-overlay--mid">
      <h2>Feature Highlight</h2>
    </div>
  </div>
</section>

<!-- Normal content continues after -->
<section class="content-after">
  ...
</section>
```

### 2. CSS

```css
.frame-sequence-hero {
  position: relative;
  height: 500vh; /* Scrollbare Höhe — mehr = langsamer */
}

.frame-sequence-sticky {
  position: sticky;
  top: 0;
  width: 100%;
  height: 100vh;
  overflow: hidden;
}

.frame-sequence-sticky canvas {
  width: 100%;
  height: 100%;
  display: block;
  object-fit: cover;
}

/* Overlay Content */
.frame-overlay {
  position: absolute;
  inset: 0;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  text-align: center;
  pointer-events: none;
  opacity: 0;
  transition: opacity 0.3s ease;
}
```

### 3. JavaScript — Core Implementation

```javascript
import gsap from 'gsap';
import { ScrollTrigger } from 'gsap/ScrollTrigger';
gsap.registerPlugin(ScrollTrigger);

class FrameSequence {
  constructor(options) {
    this.canvas = document.querySelector(options.canvas);
    this.ctx = this.canvas.getContext('2d');
    this.container = document.querySelector(options.container);
    this.frameCount = options.frameCount;
    this.framePath = options.framePath; // z.B. '/frames/hero/frame_{index}.webp'
    this.padLength = options.padLength || 4;
    this.scrubSmoothing = options.scrub || 0.5;
    this.scrollLength = options.scrollLength || '500%';
    
    this.frames = [];
    this.currentFrame = { value: 0 };
    this.loaded = 0;
    this.onProgress = options.onProgress || null;
    this.onReady = options.onReady || null;
    
    this._init();
  }
  
  _init() {
    this._setupCanvas();
    this._preloadFrames();
    window.addEventListener('resize', () => this._setupCanvas());
  }
  
  _setupCanvas() {
    const dpr = Math.min(window.devicePixelRatio, 2);
    this.canvas.width = window.innerWidth * dpr;
    this.canvas.height = window.innerHeight * dpr;
    this.canvas.style.width = '100%';
    this.canvas.style.height = '100%';
    this._render(); // Re-render current frame after resize
  }
  
  _getFramePath(index) {
    const padded = String(index).padStart(this.padLength, '0');
    return this.framePath.replace('{index}', padded);
  }
  
  _preloadFrames() {
    const promises = [];
    
    for (let i = 0; i < this.frameCount; i++) {
      const img = new Image();
      const promise = new Promise((resolve, reject) => {
        img.onload = () => {
          this.loaded++;
          if (this.onProgress) {
            this.onProgress(this.loaded / this.frameCount);
          }
          resolve();
        };
        img.onerror = reject;
      });
      img.src = this._getFramePath(i);
      this.frames[i] = img;
      promises.push(promise);
    }
    
    // Render first frame as soon as it loads
    this.frames[0].onload = () => {
      this._render();
      // Continue tracking overall progress
      this.loaded++;
      if (this.onProgress) this.onProgress(this.loaded / this.frameCount);
    };
    
    Promise.all(promises).then(() => {
      if (this.onReady) this.onReady();
      this._initScrollTrigger();
    });
  }
  
  _render() {
    const idx = Math.min(
      Math.floor(this.currentFrame.value),
      this.frameCount - 1
    );
    const img = this.frames[idx];
    if (!img || !img.complete) return;
    
    const { width, height } = this.canvas;
    this.ctx.clearRect(0, 0, width, height);
    
    // Cover-fit the image (like object-fit: cover)
    const imgRatio = img.width / img.height;
    const canvasRatio = width / height;
    let drawW, drawH, drawX, drawY;
    
    if (canvasRatio > imgRatio) {
      drawW = width;
      drawH = width / imgRatio;
      drawX = 0;
      drawY = (height - drawH) / 2;
    } else {
      drawH = height;
      drawW = height * imgRatio;
      drawX = (width - drawW) / 2;
      drawY = 0;
    }
    
    this.ctx.drawImage(img, drawX, drawY, drawW, drawH);
  }
  
  _initScrollTrigger() {
    gsap.to(this.currentFrame, {
      value: this.frameCount - 1,
      snap: 'value',
      ease: 'none',
      scrollTrigger: {
        trigger: this.container,
        start: 'top top',
        end: `+=${this.scrollLength}`,
        scrub: this.scrubSmoothing,
        pin: false, // Wir nutzen CSS sticky statt pin
      },
      onUpdate: () => this._render()
    });
  }
  
  // Public: Jump to specific frame
  goToFrame(index) {
    this.currentFrame.value = Math.min(index, this.frameCount - 1);
    this._render();
  }
  
  // Public: Get current frame index
  getCurrentFrame() {
    return Math.floor(this.currentFrame.value);
  }
  
  // Public: Destroy
  destroy() {
    ScrollTrigger.getAll().forEach(t => t.kill());
    window.removeEventListener('resize', this._setupCanvas);
    this.frames = [];
  }
}
```

### 4. Usage

```javascript
// Basic usage
const hero = new FrameSequence({
  canvas: '#frameCanvas',
  container: '#heroSequence',
  frameCount: 120,
  framePath: '/frames/hero/frame_{index}.webp',
  padLength: 4,
  scrub: 0.5,
  scrollLength: '500%',
  onProgress: (p) => {
    document.querySelector('.loader').style.width = `${p * 100}%`;
  },
  onReady: () => {
    document.querySelector('.loader-wrapper').classList.add('hidden');
  }
});

// With overlay text animations
const tl = gsap.timeline({
  scrollTrigger: {
    trigger: '#heroSequence',
    start: 'top top',
    end: '+=500%',
    scrub: 0.5
  }
});

tl.fromTo('.frame-overlay--intro',
  { opacity: 1 }, { opacity: 0 },
  0.0  // At 0% scroll
)
.fromTo('.frame-overlay--mid',
  { opacity: 0, y: 30 }, { opacity: 1, y: 0 },
  0.3  // At 30% scroll
)
.to('.frame-overlay--mid',
  { opacity: 0 },
  0.6  // Fade out at 60%
);
```

---

## Frame Preparation

### Export-Workflows

**After Effects:**
```
Composition → Add to Render Queue
Format: PNG Sequence oder WebP
Resolution: 1920×1080 (Desktop) / 1080×1920 (Mobile)
Frame Rate: 24-30fps source, export every frame
Naming: frame_0000.webp, frame_0001.webp, ...
```

**Blender:**
```
Output Properties → File Format: PNG / WebP
Output Path: //frames/frame_####
Render → Render Animation
```

**Cinema 4D / Houdini:**
```
Render Settings → Save → Format: PNG
File: frame_$F4.png (Houdini) / frame_####.png (C4D)
```

**ffmpeg (aus bestehendem Video):**
```bash
# Video → WebP Frames (beste Option)
ffmpeg -i input.mp4 -vf "scale=1920:1080" -q:v 80 frames/frame_%04d.webp

# Video → PNG Frames (höchste Qualität)
ffmpeg -i input.mp4 -vf "scale=1920:1080" frames/frame_%04d.png

# Nur jeden 2. Frame (halbiert Datenmenge)
ffmpeg -i input.mp4 -vf "select=not(mod(n\,2)),scale=1920:1080" -vsync vfr frames/frame_%04d.webp
```

### Optimale Frame-Anzahl

| Scroll-Dauer | Empfohlene Frames | Effekt |
|---|---|---|
| Quick reveal (2-3vh) | 30-60 | Schneller Produkt-Reveal |
| Standard Hero (4-5vh) | 90-150 | Smooth, wie Apple es macht |
| Extended Story (6-8vh) | 150-300 | Scrollytelling, detailreich |

**Faustregel:** `scrollHöhe / viewportHöhe * 30 = minimale Frames`

### Bildformat-Vergleich

| Format | Qualität | Größe (120 Frames, 1080p) | Browser-Support |
|---|---|---|---|
| **WebP** | Sehr gut | **3-6 MB** | Alle modernen Browser |
| PNG | Perfekt | 15-40 MB | Universal |
| JPEG | Gut | 5-10 MB | Universal |
| AVIF | Exzellent | 2-4 MB | Chrome, Firefox (kein Safari <16.4) |

**Empfehlung:** WebP als Standard, AVIF als progressive Enhancement.

---

## Performance & Optimierung

### Progressive Loading (Prioritäts-basiert)

```javascript
// Lade zuerst Frame 0, dann verteilt, dann den Rest
function getLoadOrder(totalFrames) {
  const order = [0]; // Erster Frame hat Priorität
  const keyFrames = [
    Math.floor(totalFrames * 0.25),
    Math.floor(totalFrames * 0.5),
    Math.floor(totalFrames * 0.75),
    totalFrames - 1
  ];
  order.push(...keyFrames);
  
  // Rest auffüllen
  for (let i = 0; i < totalFrames; i++) {
    if (!order.includes(i)) order.push(i);
  }
  return order;
}
```

### Responsive Frames (Mobile vs Desktop)

```javascript
function getFrameSet() {
  const isMobile = window.innerWidth < 768;
  return {
    path: isMobile
      ? '/frames/hero-mobile/frame_{index}.webp'   // 720p
      : '/frames/hero-desktop/frame_{index}.webp',  // 1080p
    count: isMobile ? 60 : 120  // Weniger Frames auf Mobile
  };
}

const config = getFrameSet();
const hero = new FrameSequence({
  canvas: '#frameCanvas',
  container: '#heroSequence',
  frameCount: config.count,
  framePath: config.path
});
```

### Preloader UI Pattern

```javascript
// Minimaler Preloader
function createPreloader(onComplete) {
  const bar = document.querySelector('.preload-bar');
  const text = document.querySelector('.preload-text');
  
  return {
    update(progress) {
      bar.style.transform = `scaleX(${progress})`;
      text.textContent = `${Math.round(progress * 100)}%`;
    },
    complete() {
      gsap.to('.preloader', {
        opacity: 0,
        duration: 0.6,
        onComplete: () => {
          document.querySelector('.preloader').remove();
          onComplete();
        }
      });
    }
  };
}
```

### Memory Management

```javascript
// Für sehr lange Sequenzen (300+ Frames):
// Nur Frames im Sichtbereich + Buffer laden
class LazyFrameSequence {
  constructor(options) {
    this.bufferSize = options.buffer || 30; // Frames vor/nach
    this.loadedRange = { start: 0, end: 0 };
    // ... wie oben, aber mit lazy loading
  }
  
  _loadRange(centerFrame) {
    const start = Math.max(0, centerFrame - this.bufferSize);
    const end = Math.min(this.frameCount - 1, centerFrame + this.bufferSize);
    
    for (let i = start; i <= end; i++) {
      if (!this.frames[i]?.complete) {
        this._loadFrame(i);
      }
    }
    
    // Alte Frames freigeben
    for (let i = 0; i < start - this.bufferSize; i++) {
      if (this.frames[i]) {
        this.frames[i].src = '';
        this.frames[i] = null;
      }
    }
  }
}
```

---

## React Integration

```jsx
import { useEffect, useRef, useCallback } from 'react';
import gsap from 'gsap';
import { ScrollTrigger } from 'gsap/ScrollTrigger';

gsap.registerPlugin(ScrollTrigger);

function FrameSequenceHero({
  frameCount = 120,
  framePath = '/frames/frame_{index}.webp',
  scrollLength = '500%',
  scrub = 0.5,
  children
}) {
  const canvasRef = useRef(null);
  const containerRef = useRef(null);
  const framesRef = useRef([]);
  const currentFrameRef = useRef({ value: 0 });

  const render = useCallback(() => {
    const canvas = canvasRef.current;
    if (!canvas) return;
    const ctx = canvas.getContext('2d');
    const idx = Math.min(
      Math.floor(currentFrameRef.current.value),
      frameCount - 1
    );
    const img = framesRef.current[idx];
    if (!img?.complete) return;

    ctx.clearRect(0, 0, canvas.width, canvas.height);
    
    // Cover-fit
    const imgR = img.width / img.height;
    const canR = canvas.width / canvas.height;
    let dw, dh, dx, dy;
    if (canR > imgR) {
      dw = canvas.width; dh = dw / imgR;
      dx = 0; dy = (canvas.height - dh) / 2;
    } else {
      dh = canvas.height; dw = dh * imgR;
      dx = (canvas.width - dw) / 2; dy = 0;
    }
    ctx.drawImage(img, dx, dy, dw, dh);
  }, [frameCount]);

  useEffect(() => {
    const canvas = canvasRef.current;
    const dpr = Math.min(window.devicePixelRatio, 2);
    
    const resize = () => {
      canvas.width = window.innerWidth * dpr;
      canvas.height = window.innerHeight * dpr;
      render();
    };
    resize();
    window.addEventListener('resize', resize);

    // Preload
    const frames = [];
    for (let i = 0; i < frameCount; i++) {
      const img = new Image();
      img.src = framePath.replace('{index}', String(i).padStart(4, '0'));
      if (i === 0) img.onload = render;
      frames.push(img);
    }
    framesRef.current = frames;

    // ScrollTrigger
    const tween = gsap.to(currentFrameRef.current, {
      value: frameCount - 1,
      snap: 'value',
      ease: 'none',
      scrollTrigger: {
        trigger: containerRef.current,
        start: 'top top',
        end: `+=${scrollLength}`,
        scrub,
        pin: false,
      },
      onUpdate: render
    });

    return () => {
      tween.kill();
      ScrollTrigger.getAll().forEach(t => t.kill());
      window.removeEventListener('resize', resize);
    };
  }, [frameCount, framePath, scrollLength, scrub, render]);

  return (
    <section ref={containerRef} style={{ position: 'relative', height: scrollLength }}>
      <div style={{
        position: 'sticky', top: 0,
        width: '100%', height: '100vh', overflow: 'hidden'
      }}>
        <canvas ref={canvasRef} style={{ width: '100%', height: '100%', display: 'block' }} />
        {children}
      </div>
    </section>
  );
}

export default FrameSequenceHero;

// Usage:
// <FrameSequenceHero frameCount={120} framePath="/frames/frame_{index}.webp">
//   <div className="overlay">
//     <h1>Your Hero Text</h1>
//   </div>
// </FrameSequenceHero>
```

---

## Mobile-Fallbacks

```javascript
// Strategie: Weniger Frames + kleinere Auflösung auf Mobile
function initResponsiveSequence() {
  const isMobile = window.matchMedia('(max-width: 768px)').matches;
  const isSlowConnection = navigator.connection?.effectiveType === '2g'
    || navigator.connection?.effectiveType === 'slow-2g';
  
  if (isSlowConnection) {
    // Fallback: Statisches Bild + Parallax
    document.querySelector('#frameCanvas').style.display = 'none';
    document.querySelector('.static-hero').style.display = 'block';
    return;
  }
  
  return new FrameSequence({
    canvas: '#frameCanvas',
    container: '#heroSequence',
    frameCount: isMobile ? 60 : 120,
    framePath: isMobile
      ? '/frames/mobile/frame_{index}.webp'
      : '/frames/desktop/frame_{index}.webp',
    scrub: isMobile ? 1 : 0.5,
    scrollLength: isMobile ? '350%' : '500%'
  });
}
```

---

## Overlay-Text-Choreographie

Typisches Pattern: Text erscheint/verschwindet synchron zu bestimmten Frame-Bereichen.

```javascript
// Timeline parallel zur Frame-Sequence
const overlayTL = gsap.timeline({
  scrollTrigger: {
    trigger: '#heroSequence',
    start: 'top top',
    end: '+=500%',
    scrub: 0.5
  }
});

// Frame 0-30: Intro Text sichtbar
overlayTL
  .set('.overlay-intro', { opacity: 1 })
  .to('.overlay-intro', { opacity: 0, duration: 0.1 }, 0.2)

  // Frame 40-70: Feature Text einblenden
  .fromTo('.overlay-feature',
    { opacity: 0, y: 40 },
    { opacity: 1, y: 0, duration: 0.1 },
    0.3
  )
  .to('.overlay-feature', { opacity: 0, duration: 0.1 }, 0.55)

  // Frame 90-120: CTA einblenden
  .fromTo('.overlay-cta',
    { opacity: 0, scale: 0.9 },
    { opacity: 1, scale: 1, duration: 0.15 },
    0.75
  );
```

---

## Checklist für Production

- [ ] **Frames exportiert** in WebP, 1920×1080 (Desktop) + 1080×608 (Mobile)
- [ ] **Preloader** implementiert mit Progress-Bar
- [ ] **Erster Frame** wird sofort angezeigt (vor vollständigem Laden)
- [ ] **Responsive:** Mobile bekommt weniger/kleinere Frames
- [ ] **Slow-Connection-Fallback:** Statisches Bild statt Sequence
- [ ] **Canvas DPR:** `Math.min(devicePixelRatio, 2)` — nicht höher
- [ ] **Cover-Fit:** Canvas füllt Viewport ohne Verzerrung
- [ ] **ScrollTrigger scrub:** 0.3-0.8 für smooth feel
- [ ] **Section-Höhe:** 400-600vh je nach gewünschter Scroll-Dauer
- [ ] **Overlay-Text** mit eigenem ScrollTrigger-Timeline synchronisiert
- [ ] **Cleanup:** ScrollTrigger.kill() bei SPA-Navigation
- [ ] **CDN:** Frames über CDN ausliefern (Cloudfront, Vercel Edge, etc.)

---

## When to Use This Skill

Immer verwenden wenn:
- Hero-Section mit scroll-gesteuerter Animation gebaut wird
- User "Video beim Scrollen", "Frame Sequence", "wie Apple" erwähnt
- Premium Scroll-Experience gefragt ist
- website-builder eine animierte Hero-Section plant

Kombinieren mit:
- **scroll-animation-3** für GSAP/ScrollTrigger Grundlagen
- **webdesign-5** für Scrollytelling-Kontext
- **3d-effekt-4** wenn Frames aus Three.js Echtzeit-Rendering kommen
- **animation4** für React/Framer Motion Integration
