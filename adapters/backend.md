# ⚙️ Backend Performance Adapter (`$performance:backend`)

> **Guía de perfilado, diagnóstico y optimización para servicios Backend (Node.js, Go, Python, Rust, Java/Kotlin).**

---

## 🛠️ Herramientas de Perfilado
- **Muestreo de CPU & Flame Graphs**:
  - `pprof` (Go), `async-profiler` / `JFR` (JVM), `py-spy` / `cProfile` (Python), `cargo-flamegraph` (Rust), `0x` / Node Clinic (Node.js).
- **Herramientas de Benchmarking & Carga**:
  - `k6`, `wrk`, `autocannon`, `locust` (Pruebas de estrés y límites de concurrencia).
- **Monitoreo de I/O y Base de Datos**:
  - `EXPLAIN ANALYZE` en PostgreSQL / MySQL.
  - Slow Query Logs y APM Tracing (OpenTelemetry, Datadog, Prometheus).

---

## 📊 Métricas Críticas
- **Latencia Percentil**: p50 ≤ 20ms, p95 ≤ 80ms, p99 ≤ 200ms.
- **Throughput**: Requests por segundo (RPS) sostenidos sin degradación de latencia.
- **Garbage Collection (GC) Pauses**: Duración acumulada de pausas de GC < 1% del tiempo de CPU.
- **Database Connection Pool Saturation**: Utilización del pool ≤ 75% bajo carga pico.
- **Memory RSS vs Heap**: Estabilidad de memoria sin crecimiento monotónico (Memory Leak).

---

## 🎯 Qué Auditar en el Proyecto
1. **Acceso a Datos y Base de Datos (N+1 Query Problem)**:
   - Queries ejecutadas dentro de bucles en lugar de `JOIN`, batch fetching o dataloaders.
   - Falta de índices en columnas utilizadas en cláusulas `WHERE`, `JOIN` y `ORDER BY`.
   - Tamaño de connection pool inadecuado (demasiado pequeño provoca encolamiento; demasiado grande satura la BD).
2. **I/O Bloqueante y Serialización**:
   - Llamadas síncronas a disco o APIs externas bloqueando el event loop o el pool de hilos de trabajo.
   - Serialización de payloads JSON masivos (considerar buffers binarios como Protocol Buffers / FlatBuffers / MessagePack).
3. **Manejo de Memoria y Concurrencia**:
   - Fugas en caches en memoria (`Map`/`dict` sin política de expiración LRU ni límite máximo de entradas).
   - Creación excesiva de objetos de vida corta saturando el recolector de basura.
   - Fugas de Goroutines / Tasks asíncronas no canceladas por timeout de contexto.

---

## 🧩 Patrones de Optimización Recomendados

### 1. Eliminación de Consultas N+1 con Batching
```sql
-- En vez de N queries individuales:
-- SELECT * FROM orders WHERE user_id = 1;
-- SELECT * FROM orders WHERE user_id = 2;

-- Usar IN con batching o Dataloaders:
SELECT * FROM orders WHERE user_id IN (1, 2, 3, 4, 5);
```

### 2. Cache LRU con Límite de Memoria Acotado
```python
# Uso de caches acotados con límite estricto de elementos
from functools import lru_cache

@lru_cache(maxsize=1024)
def compute_expensive_hash(input_data: str) -> str:
    # Retiene solo los últimos 1024 resultados, evitando leaks
    return hash_function(input_data)
```

### 3. Contexto con Timeout y Cancelación en Concurrencia
```go
// Cancelación oportuna para liberar recursos
ctx, cancel := context.WithTimeout(context.Background(), 2*time.Second)
defer cancel()

req, err := http.NewRequestWithContext(ctx, "GET", targetURL, nil)
```
