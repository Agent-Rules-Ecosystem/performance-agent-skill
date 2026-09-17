# 🚀 Tiempo de Arranque y Costo de Red (Startup & Network Cost)

> **Guía técnica sobre reducción de Cold Start, Time to Interactive y optimización de llamadas de red.**

---

## 1. Tipos de Arranque (Startup States)

1. **Cold Start (Arranque en Frío)**:
   - El proceso de la app no está en memoria. El sistema operativo debe asignar memoria, crear el proceso, inicializar el runtime, cargar dependencias e invocar el punto de entrada.
   - *Presupuesto*: **≤ 1.5 - 2.0 s** hasta el primer render interactivo.
2. **Warm Start (Arranque Templado)**:
   - El proceso está en memoria pero la actividad/vista principal fue destruida.
   - *Presupuesto*: **≤ 1.0 s**.
3. **Hot Start (Arranque en Caliente)**:
   - La app regresa desde el segundo plano al primer plano.
   - *Presupuesto*: **≤ 500 ms**.

---

## 2. Eliminación de Bloqueos en la Fase de Inicialización

### Antipatrones de Arranque:
- ❌ **Inicialización Eager de Todos los SDKs**: Inicializar analíticas, crash reporters, motores de pago, bases de datos y clientes de chat simultáneamente en `main()` o `Application.onCreate()`.
  - *Solución*: Mover SDKs no esenciales a inicialización diferida (*Lazy Initialization*) post-primer frame.
- ❌ **Llamadas de Red Bloqueantes antes de Renderizar**: Esperar respuesta de un endpoint remoto antes de pintar la UI base (usar skeletons / placeholders locales).
- ❌ **Lecturas Síncronas de Disco en Main Thread**: Cargar archivos de preferencias o SQLite masivo durante el boot.

---

## 3. Optimización de Red y Latencia

1. **Reutilización de Conexiones (Keep-Alive & HTTP/2 / HTTP/3)**:
   - Evitar el costo de handshakes TLS repetitivos manteniendo sesiones abiertas con multiplexación de streams.
2. **Batching y Dataloaders**:
   - Agrupar múltiples peticiones pequeñas en una sola solicitud estructurada.
3. **Estrategia de Cache Offline-First**:
   - Servir datos inmediatamente desde cache local (Stale-While-Revalidate) y actualizar en segundo plano para eliminar pantallas de carga vacías.
