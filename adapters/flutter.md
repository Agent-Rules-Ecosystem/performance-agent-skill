# 📱 Flutter Performance Adapter (`$performance:flutter`)

> **Guía de perfilado, diagnóstico y optimización para aplicaciones Flutter (Impeller & Skia).**

---

## 🛠️ Herramientas de Perfilado
- **Flutter DevTools**:
  - *Performance View*: Timeline trace, Frame analysis (UI vs Raster thread), Track Widget Rebuilds.
  - *Memory View*: Heap allocation profile, Diff snapshot, Object retainers, Leaked instances tracking.
  - *Network & Logging View*: Latencia de requests y payload size.
- **Flags de Diagnóstico**:
  - `debugProfileBuildsEnabled = true`: Profiling detallado de builds de widgets.
  - `debugProfilePaintsEnabled = true`: Profiling de etapas de pintado.
  - `debugRepaintRainbowEnabled = true`: Visualizar bordes de repintado en runtime.
  - `--profile` mode (NUNCA auditar en `--debug`).

---

## 📊 Métricas Críticas
- **Frame Timing**:
  - *UI Thread Time*: ≤ 8.3ms (120Hz) / ≤ 16.6ms (60Hz).
  - *Raster Thread Time*: ≤ 8.3ms / ≤ 16.6ms.
- **Widget Rebuild Count**: Número de widgets reconstruidos por frame en scrolling / interacción.
- **Image Cache Memory**: `PaintingBinding.instance.imageCache.currentSizeBytes`.
- **Startup Time**: Cold start hasta `FlutterView` rendering inicial.

---

## 🎯 Qué Auditar en el Proyecto
1. **Árbol de Widgets y Rebuilds**:
   - Falta de `const` en constructores invariantes.
   - Reconstrucción de árboles completos por uso de `setState()` o `ChangeNotifier` en nodos raíz en lugar de `Selector`, `BlocBuilder.buildWhen` o `Consumer` atómicos.
   - Uso de `ListView(children: ...)` en vez de `ListView.builder` o `CustomScrollView` con `SliverList`.
2. **Pintado y Rasterización**:
   - Falta de `RepaintBoundary` en widgets que se actualizan a alta frecuencia (animaciones, tickers, barras de progreso).
   - Uso intensivo de `Opacity` / `BackdropFilter` / `ClipRRect` dentro de listas en scroll (causan saveLayer costosos).
3. **Manejo de Imágenes y Memoria**:
   - Carga de imágenes grandes de red o assets sin especificar `cacheWidth` o `cacheHeight`.
   - Tamaño del `imageCache` sin límites acotados para dispositivos de gama baja (`maximumSize`, `maximumSizeBytes`).
4. **Concurrencia**:
   - Deserialización de JSON masivo o procesamiento de criptografía en el hilo principal sin usar `compute()` o `Isolate.run()`.
5. **Ciclo de Vida**:
   - `AnimationController`, `TextEditingController`, `StreamSubscription` o `ScrollController` sin invocar `dispose()` en el `State`.

---

## 🧩 Patrones de Optimización Recomendados

### 1. Aislamiento de Repintado
```dart
// Aislar subárboles de alta frecuencia con RepaintBoundary
RepaintBoundary(
  child: AnimatedCounterWidget(value: dynamicValue),
)
```

### 2. Downsampling de Bitmaps
```dart
// Decodificar exactamente al tamaño de despliegue
Image.asset(
  'assets/images/banner.png',
  cacheWidth: 800, // Escalar en decode para ahorrar heap
  fit: BoxFit.cover,
)
```

### 3. Offloading a Isolates
```dart
// Mover parsing pesado fuera del hilo UI
final items = await compute(parseLargePayloadJson, rawStringResponse);
```
