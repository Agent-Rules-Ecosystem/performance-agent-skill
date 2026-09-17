# ⚠️ Plantilla de Regresión de Rendimiento (Performance Regression Template)

Plantilla para documentar y aislar regresiones de rendimiento detectadas tras un cambio o release:

```markdown
# 📉 Reporte de Regresión de Rendimiento

- **Fecha**: YYYY-MM-DD
- **ID de Incidencia**: PERF-REG-01
- **Componente / Módulo afectado**: `<ruta o nombre del módulo>`
- **Commit / PR causante**: `<hash o número de PR>`

---

## 🔍 Síntoma y Métrica Afectada
- **Métrica**: `<ej. Frame Time en Lista de Productos / Tiempo de Cold Start / Memory Spike>`
- **Valor Anterior (Línea Base)**: `<ej. 12ms / 1.2s / 65MB>`
- **Valor Actual (Regresión)**: `<ej. 38ms / 3.4s / 180MB>`
- **Delta de Degradación**: `<ej. +216% de tiempo de render>`

---

## 🛠️ Causa Raíz Identificada
- `<Descripción técnica del código introducido que provocó la pérdida de rendimiento>`

---

## 🩹 Plan de Mitigación / Corrección
1. `<Acción 1: Revertir / Modificar componente>`
2. `<Acción 2: Añadir benchmark preventivo a CI>`
```
