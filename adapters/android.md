# 🤖 Android / Kotlin Performance Adapter (`$performance:android`)

> **Guía de perfilado, diagnóstico y optimización para aplicaciones Android (Jetpack Compose & Android Views).**

---

## 🛠️ Herramientas de Perfilado
- **Android Studio Profiler**:
  - *CPU Profiler*: Call Chart, Flame Chart, Top Down, System Trace (Perfetto integrado).
  - *Memory Profiler*: Heap Dump (`.hprof`), Allocation Tracking, Native Memory Tracking.
  - *Energy Profiler*: WakeLocks, AlarmManagers, Location requests.
- **Perfetto / Systrace**:
  - Trazas a nivel de kernel, SurfaceFlinger y VSYNC pulses.
- **Herramientas en Dispositivo**:
  - *Profile GPU Rendering* (Barras en pantalla con línea de 16ms).
  - *Show Layout Bounds* y *Debug GPU Overdraw*.
- **Macrobenchmark & Microbenchmark**:
  - Medición de Startup time (`Cold`, `Warm`, `Hot`) y jank en scroll continuo.

---

## 📊 Métricas Críticas
- **Frame Drop Rate (Jank Percentage)**: Porcentaje de frames que superan los 16ms/8ms.
- **Time to Initial Display (TTID) & Time to Full Display (TTFD)**: ≤ 1.5s para Cold Start.
- **Heap Size & GC Pause Duration**: Frecuencia y duración de pausas de Garbage Collector (Concurrent Mark-Sweep / Generational GC).
- **Overdraw Factor**: ≤ 2x (máximo 2 capas de color superpuestas).
- **APK / AAB Download Size**: Optimización mediante R8, ProGuard y App Bundles.

---

## 🎯 Qué Auditar en el Proyecto
1. **Jetpack Compose Recompositions**:
   - Recomposiciones innecesarias por parámetros inestables (ej. colecciones `List<T>` estándar sin `@Immutable` o `ImmutableList`).
   - Falta de `remember` o `derivedStateOf` en transformaciones de estado costosas.
   - Modifiers creados inline en listas sin reciclaje eficiente (`LazyColumn` con keys estables).
2. **Concurrencia con Coroutines**:
   - Uso de `Dispatchers.Main` para operaciones de I/O, BD (Room), o parsing JSON (debe usarse `Dispatchers.IO` o `Dispatchers.Default`).
   - Coroutines lanzadas en scopes no acotados (`GlobalScope` en lugar de `viewModelScope` o `lifecycleScope`).
3. **Fugas de Memoria**:
   - Referencias estáticas o de larga vida reteniendo `Activity`, `Context` o `View`.
   - Uso de `LeakCanary` para detección automatizada en entornos de prueba.
4. **Baseline Profiles**:
   - Ausencia de Baseline Profiles para pre-compilar rutas críticas con AOT y eliminar jank en primer uso.

---

## 🧩 Patrones de Optimización Recomendados

### 1. Claves Estables y Derivación de Estado en Compose
```kotlin
// Usar keys y derivedStateOf para evitar recomposiciones en scroll
LazyColumn {
    items(items = userList, key = { it.id }) { user ->
        UserCard(user = user)
    }
}

val showScrollToTopButton by remember {
    derivedStateOf { listState.firstVisibleItemIndex > 5 }
}
```

### 2. Concurrencia Fuera del Main Thread
```kotlin
// Offload de procesamiento masivo
suspend fun processLargeDataset(raw: String): List<DomainModel> = withContext(Dispatchers.Default) {
    jsonParser.decodeFromString(raw)
}
```

### 3. Baseline Profiles
```kotlin
// Generación de perfil base para optimizar arranque y scroll
@RunWith(AndroidJUnit4::class)
class BaselineProfileGenerator {
    @get:Rule val baselineRule = BaselineProfileRule()

    @Test fun generate() = baselineRule.collect(packageName = "com.example.app") {
        startActivityAndWait()
        device.scrollDown()
    }
}
```
