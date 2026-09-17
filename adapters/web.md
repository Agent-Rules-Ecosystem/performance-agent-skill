# 🌐 Web Performance Adapter (`$performance:web`)

> **Guía de perfilado, diagnóstico y optimización para aplicaciones Web modernas (SPA, SSR, Jamstack).**

---

## 🛠️ Herramientas de Perfilado
- **Chrome DevTools**:
  - *Performance Panel*: CPU Throttling (4x/6x slowdown), Network Throttling (Fast 3G/Slow 3G), Main Thread Flame Chart, Long Tasks (>50ms).
  - *Memory Panel*: Heap Snapshots, Allocation Instrumentation on Timeline, Allocation Sampling.
  - *Coverage Tab*: Código CSS/JS no utilizado por página.
  - *Rendering Tab*: Paint Flashing, Layout Shift Regions, Frame Rendering Stats.
- **Lighthouse & PageSpeed Insights**:
  - Score global y desglose de auditorías de rendimiento.
- **Web Vitals CLI & Extension**:
  - Medición en tiempo real de Core Web Vitals en campo y laboratorio.

---

## 📊 Métricas Críticas (Core Web Vitals)
- **Largest Contentful Paint (LCP)**: ≤ 2.5 s (Carga del elemento visual principal).
- **Interaction to Next Paint (INP)**: ≤ 200 ms (Capacidad de respuesta ante clicks/teclas).
- **Cumulative Layout Shift (CLS)**: ≤ 0.1 (Estabilidad visual sin saltos repentinos).
- **First Contentful Paint (FCP)**: ≤ 1.8 s.
- **Total Blocking Time (TBT)**: ≤ 200 ms.
- **Initial JS Bundle Size**: ≤ 150 KB gzip para la ruta principal.

---

## 🎯 Qué Auditar en el Proyecto
1. **Long Tasks & Event Handlers (INP)**:
   - Event listeners sincrónicos pesados que bloquean el main thread (clicks, inputs, scroll).
   - Ausencia de `scheduler.yield()` o `requestAnimationFrame` para fragmentar tareas largas.
2. **Carga y Optimización de Recursos (LCP & FCP)**:
   - Imágenes de banner o hero en formato JPG/PNG pesado sin `fetchpriority="high"`, sin compresión WebP/AVIF o sin dimensiones explícitas (`width` y `height` obligatorios para evitar CLS).
   - Fuentes web sin `font-display: swap` bloqueando el renderizado inicial (FOIT).
   - Scripts de terceros (analytics, widgets) cargados sin `defer` o `async`.
3. **Paquetes y Bundles (Bundle Budget)**:
   - Importaciones completas de librerías monolíticas (ej. `import _ from 'lodash'` en vez de funciones específicas o `lodash-es`).
   - Falta de *Code Splitting* dinámico (`import()` dinámico) en rutas y componentes pesados (gráficas, editores rich-text, modales).
4. **Fugas de Memoria en SPAs**:
   - Event listeners en `window` / `document` o suscripciones a WebSockets no removidos al desmontar componentes de React, Vue, Svelte o Angular.
   - Retención de nodos DOM desvinculados (*Detached DOM nodes*).

---

## 🧩 Patrones de Optimización Recomendados

### 1. Fragmentación de Tareas Largas (`scheduler.yield()`)
```javascript
// Dividir cómputo pesado para mantener el hilo principal interactivo
async function processLargeBatch(items) {
  for (let i = 0; i < items.length; i++) {
    processItem(items[i]);
    // Ceder el control al navegador cada 50 items
    if (i % 50 === 0 && 'scheduler' in window && 'yield' in scheduler) {
      await scheduler.yield();
    }
  }
}
```

### 2. Imagen LCP Optimizada (Zero CLS)
```html
<!-- Dimensiones explícitas, formato moderno y prioridad alta para LCP -->
<picture>
  <source srcset="/images/hero.avif" type="image/avif">
  <source srcset="/images/hero.webp" type="image/webp">
  <img 
    src="/images/hero.jpg" 
    alt="Hero Principal" 
    width="1200" 
    height="600" 
    fetchpriority="high" 
    decoding="async"
    style="width: 100%; height: auto; aspect-ratio: 2 / 1;"
  >
</picture>
```

### 3. Code Splitting y Carga Dinámica
```javascript
// Carga diferida de componentes pesados
const HeavyChart = lazy(() => import('./components/AnalyticsChart'));
```
