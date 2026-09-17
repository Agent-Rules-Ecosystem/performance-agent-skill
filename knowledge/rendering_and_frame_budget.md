# 🎨 Presupuesto de Cuadros y Renderizado (Rendering & Frame Budget)

> **Guía técnica sobre renderizado fluido, prevención de jank, sobre-dibujado y cómputo de frames.**

---

## 1. El Presupuesto de Frame (Frame Budget)

Para garantizar una experiencia visual fluida sin tartamudeos (*jank*), el motor de renderizado debe completar el ciclo completo dentro de la ventana de refresco de la pantalla:

$$\text{Presupuesto por Frame} = \frac{1000\text{ ms}}{\text{Frecuencia de Refresco (Hz)}}$$

- **60 Hz (Estándar)**: **16.66 ms** por frame (Margen de seguridad de código: **≤ 10 ms**).
- **90 Hz**: **11.11 ms** por frame (Margen de seguridad: **≤ 7 ms**).
- **120 Hz (ProMotion / Smooth Display)**: **8.33 ms** por frame (Margen de seguridad: **≤ 5 ms**).

Si el procesamiento de CPU o GPU excede este margen, el frame se pierde (*dropped frame*) y el usuario percibe un salto en la animación o el desplazamiento.

---

## 2. Las Tres Fases del Pipeline de UI

```
[1. Build / Recompose] ──► [2. Layout / Reflow] ──► [3. Paint / Rasterize]
```

1. **Build / Recompose**: Construcción y actualización del árbol declarativo de componentes.
   - *Causas de Jank*: Reconstrucciones globales, falta de claves únicas en listas, cálculos de filtrado o formateo ejecutados dentro de la función de render.
2. **Layout / Reflow**: Cálculo de dimensiones geométricas, posiciones y restricciones de cada elemento en la jerarquía.
   - *Causas de Jank*: *Layout Thrashing* (lectura y escritura intercalada de dimensiones DOM/Views), jerarquías excesivamente profundas (>15 niveles de anidamiento).
3. **Paint & Rasterize**: Conversión de primitivas vectoriales a píxeles en buffers de GPU.
   - *Causas de Jank*: Sombras complejas en tiempo real, efectos de desenfoque (*blur/glassmorphism*) sin rasterizado en cache y sobre-dibujado (*Overdraw*).

---

## 3. Estrategias de Mitigación

1. **Fronteras de Repintado (Repaint Boundaries / Memoization)**:
   - Aislar componentes que cambian frecuentemente para que su repintado no invalide el resto de la pantalla.
2. **Virtualización de Listas**:
   - Renderizar únicamente los elementos visibles en el viewport más un pequeño buffer de precarga (windowing).
3. **Pre-cálculo y Cacheo de Formas**:
   - Evitar `saveLayer` o clipping complejo en elementos que se desplazan a alta velocidad.
