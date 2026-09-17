# 🔌 Adaptadores de Performance Agent Skill

Este directorio contiene dos tipos de adaptadores:

## 1. Adaptadores para Herramientas de IA
- `GEMINI.md`: Adaptador para Google Gemini / Antigravity IDE.
- `CLAUDE.md`: Adaptador para Anthropic Claude.
- `AGENTS.md`: Adaptador universal OpenAI / Codex.
- `cursor-rule.mdc`: Regla de integración para Cursor IDE.

## 2. Adaptadores Técnicos por Plataforma
- `flutter.md`: Herramientas (DevTools, Performance Overlay), métricas (frame timing, rebuilds, raster cache) y patrones (RepaintBoundary, compute, imageCache).
- `android.md`: Herramientas (Android Profiler, Perfetto, Layout Inspector), métricas (GPU rendering, janky frames) y patrones (Baseline Profiles, Coroutines Dispatchers, Bitmap Pool).
- `ios.md`: Herramientas (Xcode Instruments, Time Profiler, Leaks), métricas (Hang rate, Memory Footprint) y patrones (Swift Concurrency Actors, AutoReleasePool, Lazy Stacks).
- `web.md`: Herramientas (Chrome DevTools, Lighthouse, Web Vitals), métricas (LCP, INP, CLS, Long Tasks) y patrones (Code Splitting, Web Workers, Virtualization).
- `backend.md`: Herramientas (pprof, async-profiler, APM), métricas (p95/p99 latency, RPS, GC pauses) y patrones (Connection Pooling, Zero-Copy I/O, Async non-blocking).
