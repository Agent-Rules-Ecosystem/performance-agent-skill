# 🔬 Metodología Científica de Perfilado (Profiling Methodology)

> **Guía técnica de referencia para el diagnóstico riguroso de rendimiento sin conjeturas.**

---

## 1. El Ciclo de Medición Rigurosa (M-O-V)

El perfilado de software debe tratarse como un proceso científico estructurado en tres fases:

```
[1. Medición Base (Antes)] ───► [2. Optimización Quirúrgica] ───► [3. Validación (Después)]
         │                                                               │
         └─────────────────── Delta Cuantificable ◄──────────────────────┘
```

1. **Medición Base (Baseline)**: Registrar la métrica exacta en condiciones controladas antes de tocar cualquier línea de código (ej. FPS promedio en scroll, heap ocupado tras 10 transiciones, tiempo de Cold Start en ms).
2. **Optimización Quirúrgica**: Modificar exclusivamente el componente identificado como cuello de botella (evitando refactorizaciones colaterales no relacionadas).
3. **Validación Comparativa**: Repetir la misma prueba bajo condiciones idénticas y calcular el **Delta**:
   $$\Delta = \text{Métrica}_{\text{después}} - \text{Métrica}_{\text{antes}}$$

---

## 2. Trampas Comunes y Antipatrones de Diagnóstico

- ❌ **Perfilado en Modo Debug**: Las herramientas de depuración introducen aserciones, comprobaciones de tipos en runtime e instrumentalización que distorsionan los tiempos reales hasta en un 300%. **Regla**: Perfilado SIEMPRE en `--profile` o `Release con símbolos`.
- ❌ **Muestreo en Hardware de Desarrollo (Host)**: Las CPUs de desarrollo (M3, i9, Ryzen) enmascaran cuellos de botella que colapsan dispositivos móviles de gama media/baja. **Regla**: Probar en el dispositivo representativo de menor especificación del mercado objetivo.
- ❌ **Optimización por "Sentimiento"**: Frases como *"Se siente más fluido"* o *"Debería ser más rápido así"* quedan invalidadas. Toda conclusión requiere trazas y cifras.

---

## 3. Técnicas de Aislamiento de Cuello de Botella

1. **Muestreo Estadístico vs Instrumentación Completa**:
   - *Sampling (Muestreo)*: Ideal para producción y CPU call trees con bajo overhead (<2%).
   - *Instrumentation*: Necesario para conteo exacto de llamadas y seguimiento de reconstrucciones de componentes.
2. **Análisis Top-Down y Bottom-Up en Flame Graphs**:
   - **Top-Down**: Permite identificar la rama de ejecución que consume mayor tiempo total.
   - **Bottom-Up (Invert Call Tree)**: Identifica las funciones terminales (*leaf functions*) que retienen la mayor cantidad de ciclos de CPU de forma individual.
