# 📊 Plantilla de Reporte Antes vs Después (Before & After Report)

Plantilla obligatoria para validar optimizaciones aplicadas:

```markdown
# 📈 Reporte de Validación de Rendimiento (Antes vs Después)

- **Tarea / ID**: `[PERF-XX]`
- **Módulo / Archivo**: `<ruta/del/archivo>`
- **Dispositivo / Entorno de Prueba**: `<ej. Pixel 6a / iPhone 13 / Chrome CPU 4x slowdown>`

---

## 🎯 Tabla Comparativa de Métricas

| Métrica Evaluada | Medición Antes | Medición Después | Delta / Mejora (%) | Cumple Presupuesto |
|---|:---:|:---:|:---:|:---:|
| **Frame Time UI (ms)** | `32.4 ms` | `8.1 ms` | `-75.0%` | ✅ SÍ (≤16.6ms) |
| **Heap Memory (MB)** | `168 MB` | `54 MB` | `-67.8%` | ✅ SÍ (≤60MB) |
| **Rebuilds por Scroll** | `142` | `8` | `-94.3%` | ✅ SÍ |
| **Bundle Size (KB)** | `340 KB` | `185 KB` | `-45.5%` | ✅ SÍ (≤200KB) |

---

## 🔬 Evidencia de Profiling
- **Captura / Traza Antes**: `<descripción de la traza de DevTools / Instruments / Flamegraph>`
- **Captura / Traza Después**: `<descripción de la traza verificada post-cambio>`

---

## 📝 Conclusión
`<Resumen conciso en 1-2 líneas del impacto final en la experiencia de usuario>`
```
