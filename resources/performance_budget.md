# 🎯 Presupuesto de Rendimiento del Proyecto (Performance Budget)

Plantilla para fijar los límites infranqueables de rendimiento en un proyecto:

```markdown
# 🛡️ Presupuestos Operativos del Proyecto

## 1. Presupuestos de Tiempo de Render (Frame Timing)
- **Objetivo 60 FPS**: Máximo `16.0 ms` por frame en scroll y animaciones.
- **Objetivo 120 FPS**: Máximo `8.0 ms` por frame en pantallas ProMotion/High-Refresh.
- **Umbral de Jank Crítico**: Ningún frame individual debe superar los `32.0 ms`.

## 2. Presupuestos de Arranque (Startup Budget)
- **Cold Start (TTFP)**: `< 1.8 segundos` en conexión estándar.
- **Time to Interactive (TTI)**: `< 2.5 segundos`.

## 3. Presupuestos de Memoria (Memory Budget)
- **Baseline Idle en RAM**: Máximo `80 MB`.
- **Crecimiento Neto por Pantalla**: Máximo `10 MB` tras recolección de basura.
- **Límite de Cache de Imágenes**: Máximo `40 MB` o `15% de la RAM disponible`.

## 4. Presupuestos de Distribución (Bundle Budget)
- **Peso inicial JS (Web gzip)**: `< 200 KB`.
- **Descarga total APK / AAB / IPA**: `< 30 MB`.
```
