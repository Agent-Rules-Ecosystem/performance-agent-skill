# 🧠 Presupuesto de Memoria y Gestión del Heap (Memory & Resource Budget)

> **Guía técnica sobre detección de fugas, retención de referencias y presupuestos de memoria.**

---

## 1. Anatomía de una Fuga de Memoria (Memory Leak)

Una fuga ocurre cuando un objeto deja de ser necesario para la lógica de la aplicación pero permanece accesible a través de la cadena de referencias del Garbage Collector (GC Roots) o del sistema de conteo de referencias (ARC).

### Fuentes Habituales de Fugas:
1. **Suscripciones y Observadores Huérfanos**:
   - Event listeners (`window.addEventListener`), Streams/Flows o Notificaciones registrados en componentes sin desuscripción al desmontarse.
2. **Closures y Callbacks Retenidos**:
   - Funciones anónimas que capturan `self`, `this` o el contexto de la vista dentro de operaciones asíncronas o singletons de larga vida.
3. **Caches en Memoria sin Evicción (Unbounded Caches)**:
   - Colecciones (`Map<K, V>`, `List<T>`, diccionarios) que crecen indefinidamente sin política LRU (*Least Recently Used*) o TTL (*Time To Live*).
4. **Bitmaps Descomprimidos**:
   - Una imagen de 4000x3000 píxeles a 4 bytes por píxel (RGBA_8888) consume **48 MB de RAM** descomprimida en memoria, independientemente de que su archivo PNG o JPG pese 500 KB en disco.

---

## 2. Presupuestos de Memoria Recomendados

| Plataforma / Entorno | Baseline Idle | Límite Máximo por Pantalla | Umbral Crítico (Riesgo OOM) |
|---|---|---|---|
| **Móvil Gama Baja (2-3 GB RAM)** | ≤ 60 MB | + 15 MB por pantalla activa | > 180 MB |
| **Móvil Gama Alta (6-12 GB RAM)** | ≤ 120 MB | + 35 MB por pantalla activa | > 350 MB |
| **PWA / Web SPA** | ≤ 40 MB Heap | + 10 MB por ruta | > 150 MB Heap |
| **Microservicio Backend** | ≤ 128 MB RSS | Acotado al pool de concurrencia | > 80% límite de contenedor |

---

## 3. Protocolo de Inspección de Heap Snapshots

1. **Snapshot 1 (Línea Base)**: Tomar captura del heap en pantalla de inicio tras recolección forzada de basura.
2. **Interacción repetitiva (10 Ciclos)**: Abrir la pantalla bajo prueba, realizar acciones y cerrarla volviendo a la pantalla de inicio.
3. **Snapshot 2 (Post-Acción)**: Forzar recolección de basura y tomar segunda captura.
4. **Análisis de Diferencia (Diffing)**:
   - Si el número de instancias de la vista, de controladores o de modelos de datos es $> 0$, existe una fuga de referencia activa.
   - Seguir la ruta más corta hacia el GC Root (*Shortest Path to GC Root*) para identificar el objeto retenedor.
