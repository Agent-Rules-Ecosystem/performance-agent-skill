# 🍏 iOS / Swift Performance Adapter (`$performance:ios`)

> **Guía de perfilado, diagnóstico y optimización para aplicaciones iOS (SwiftUI & UIKit).**

---

## 🛠️ Herramientas de Perfilado
- **Xcode Instruments**:
  - *Time Profiler*: Muestreo de CPU (CPU Call Tree, Invert Call Tree, Hide System Libraries).
  - *Allocations & Leaks*: Heap allocations, Reference cycles, Zombie objects.
  - *Core Animation*: Frame rate (FPS), Color Blended Layers, Color Offscreen-Rendered, Color Misaligned Images.
  - *Swift Concurrency Instrument*: Visualizar contención de Actors y Task suspension points.
- **MetricKit**:
  - Diagnóstico en producción de Hang rate, Launch time, Disk writes y Battery drain.
- **View Hierarchy & Memory Graph Debugger**:
  - Inspección visual de ciclos de retención (`strong reference cycles`) y dependencias de vistas.

---

## 📊 Métricas Críticas
- **Hang Rate**: Porcentaje de tiempo que la app no responde a eventos del usuario (hitch / freeze > 250ms).
- **Hitch Time Ratio**: Milisegundos de retraso en animación por segundo (ideal ≤ 5ms/s).
- **Memory Footprint & Dirty Memory**: Límite estricto antes de recibir advertencias del sistema (`Jetsam kill`).
- **App Launch Time**: Cold launch ≤ 400ms hasta el primer frame interactivo (Pre-main + Post-main).
- **Thermal State & Energy Impact**: Mantener estado térmico en `nominal` o `fair`.

---

## 🎯 Qué Auditar en el Proyecto
1. **SwiftUI Body Re-evaluations**:
   - Vistas con cálculo de estado complejo dentro de la propiedad `var body: some View`.
   - Uso de `ObservableObject` / `@Published` monolíticos provocando invalidación de pantallas completas (migrar al framework `@Observable` en Swift 5.9+).
   - Listas dinámicas con `ForEach` sin `id` estable o sin `LazyVStack` / `LazyHStack`.
2. **Ciclos Fuertes de Referencia (Retain Cycles)**:
   - Closures y delegados sin `[weak self]` o `[unowned self]`.
   - Controladores y servicios retenidos en singletons o notificaciones `NotificationCenter`.
3. **Manejo de Imágenes y Renderizado Offscreen**:
   - Sombras (`.shadow`), esquinas redondeadas (`.cornerRadius`) o máscaras sin rasterización/path precalculado (provocan offscreen rendering).
   - Descompresión síncrona de imágenes JPEG/PNG de alta resolución en el hilo de UI.
4. **Swift Concurrency**:
   - Bloqueo síncrono en `@MainActor` (I/O, serialización de datos pesados).
   - Falta de actores desacoplados (`actor DataProcessor`) para procesamiento en segundo plano.

---

## 🧩 Patrones de Optimización Recomendados

### 1. Framework `@Observable` y Vistas Granulares
```swift
// En Swift 5.9+, @Observable solo invalida las vistas que leen propiedades específicas
@Observable final class ProfileState {
    var username: String = ""
    var avatarURL: URL? = nil
}

struct UsernameView: View {
    let state: ProfileState
    var body: some View {
        // Solo se re-evalúa cuando username cambia, no avatarURL
        Text(state.username)
    }
}
```

### 2. Prevención de Retain Cycles en Concurrencia
```swift
final class FeedViewModel {
    func loadData() {
        Task { [weak self] in
            guard let self else { return }
            let results = await self.fetchRemoteFeed()
            await MainActor.run {
                self.items = results
            }
        }
    }
}
```

### 3. Aislamiento con Actores para Cómputo Pesado
```swift
actor HeavyPayloadProcessor {
    func process(payload: Data) -> [DomainRecord] {
        // Ejecución segura y no bloqueante fuera del hilo principal
        return JSONDecoder().decode([DomainRecord].self, from: payload)
    }
}
```
