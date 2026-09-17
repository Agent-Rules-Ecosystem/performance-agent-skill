# ⚡ Concurrencia y Descarga del Hilo Principal (Concurrency & Offloading)

> **Guía técnica sobre paralelismo, workers, isolates y operaciones no bloqueantes.**

---

## 1. El Rol Sagrado del Hilo Principal (UI Main Thread)

El hilo principal de ejecución es responsable directo de:
1. Atender las interacciones táctiles y eventos del usuario (Touch, Click, Keyboard).
2. Orquestar las fases de renderizado y sincronización con el pulso VSYNC del sistema operativo.

Cualquier operación sincrónica que retenga el hilo principal por más de **16 ms** provocará pérdida de frames; si la retención supera **200-500 ms**, el sistema operativo activará alertas de bloqueo (*ANR - Application Not Responding* en Android, *Hang Detection* en iOS o *Long Task* en Web).

---

## 2. Cuándo Descargar Tareas (Offloading Matrix)

| Tipo de Carga de Trabajo | Ejecutar en Main Thread | Descargar a Background / Worker |
|---|:---:|:---:|
| **Mutación simple de estado local** | ✅ Sí | ❌ No |
| **Parsing de JSON / XML (> 50 KB)** | ❌ No | ✅ Sí (Isolate / Worker / Default Dispatcher) |
| **Cálculo Criptográfico / Hashing** | ❌ No | ✅ Sí |
| **Manipulación / Compresión de Bitmaps** | ❌ No | ✅ Sí |
| **I/O a Base de Datos Local (SQLite/Room/Realm)** | ❌ No | ✅ Sí (Pool de I/O) |
| **Filtrado / Ordenamiento de Listas (> 500 items)** | ❌ No | ✅ Sí |

---

## 3. Primitivas de Concurrencia por Ecosistema

- **Dart / Flutter**: `Isolate.run()`, `compute()`, `ReceivePort / SendPort` para comunicación bidireccional.
- **Kotlin / Android**: `Dispatchers.Default` (cómputo intensivo CPU), `Dispatchers.IO` (I/O bloqueante de disco y red), `withContext()`.
- **Swift / iOS**: `actor` (aislamiento de estado mutable), `Task.detached(priority: .userInitiated)`, `TaskGroup`.
- **JavaScript / Web**: `Web Workers` (`Worker`), `SharedArrayBuffer`, `Comlink`, `scheduler.yield()`.
- **Rust**: `tokio::task::spawn_blocking`, `rayon` (paralelismo de datos por CPU).
- **Go**: `goroutines`, canales (`chan`), `sync.WaitGroup`, pools de buffers (`sync.Pool`).
