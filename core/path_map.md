# 🗺️ Mapa de Rutas Canónicas para Rendimiento (Path Map)

Rutas y directivas del proyecto cliente monitoreadas y auditadas por la skill:

## 📦 Assets y Recursos Estáticos
- `assets/`, `static/`, `public/`, `res/`: Inspección de peso de imágenes (PNG/JPG vs WebP/AVIF), fuentes tipográficas, animaciones Lottie/Rive y SVGs no optimizados.
- `pubspec.yaml`, `package.json`, `build.gradle`, `Package.swift`: Declaración de dependencias pesadas y configuración de empaquetado/minificación.

## 🎨 Capa de Presentación y UI
- `lib/src/presentation/`, `src/views/`, `src/components/`, `app/views/`: Auditoría de ciclos de vida, recomposiciones, listas virtualizadas, memoización y fronteras de repintado.
- `lib/main.dart`, `src/index.ts`, `MainActivity.kt`, `App.swift`: Puntos de entrada y rutinas de inicialización temprana (Startup Path).

## ⚡ Concurrencia y Tareas en Segundo Plano
- `lib/src/workers/`, `src/isolates/`, `src/services/background/`, `app/jobs/`: Tareas asíncronas, offloading de cómputo, workers y pools de hilos.

## 🧠 Estado y Servicios de Datos
- `lib/src/state/`, `src/store/`, `src/services/api/`, `app/services/`: Gestión de caches en memoria, tamaño de payloads JSON, suscripciones activas y conexiones de red.

## 📊 Configuración de Diagnóstico y Benchmarks
- `test/benchmark/`, `benchmark/`, `profile/`: Suites de benchmarking de rendimiento y perfiles automatizados.
- `overview/work/skill/performance.md`: Persistencia obligatoria de hallazgos, mediciones Antes vs Después y planes de optimización.
