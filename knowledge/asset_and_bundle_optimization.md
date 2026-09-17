# 📦 Optimización de Assets y Paquetes de Distribución (Asset & Bundle Optimization)

> **Guía técnica sobre reducción de tamaño de binarios, compresión de recursos y empaquetado eficiente.**

---

## 1. El Impacto del Tamaño del Paquete (Bundle Size)

El peso del paquete de distribución (APK/AAB, IPA, Bundle JS) impacta directamente en:
1. **Tasa de Conversión e Instalación**: Por cada 6 MB adicionales en una aplicación móvil, la tasa de conversión en tiendas se reduce entre un 1% y un 2%.
2. **Tiempo de Arranque y Parse Time**: En la Web y entornos móviles, el navegador y el runtime deben descargar, descomprimir y compilar/parsear el código JS/Bytecode antes de iniciar la ejecución.

---

## 2. Estrategias de Optimización de Assets

### Formatos de Imagen Recomendados:
- **Iconos e Ilustraciones Simples**: Vectorial (`SVG` minificado o fuentes de iconos).
- **Fotografías y Gráficos Complejos**:
  - `AVIF`: Máxima compresión con preservación de fidelidad visual (~50% menor que JPEG).
  - `WebP`: Estándar moderno ampliamente compatible (~30% menor que PNG/JPEG).
  - ❌ *Evitar*: Archivos PNG o BMP sin comprimir incrustados directamente en el paquete de la app.

### Subsetting de Fuentes:
- No incluir fuentes completas con glifos universales (chino, cirílico, árabe) si la aplicación solo se distribuye en idiomas específicos.
- Usar formatos de compresión `WOFF2` en la Web y fuentes estáticas optimizadas en móvil.

---

## 3. Minificación, Tree-Shaking y Dead Code Elimination

1. **Tree-Shaking**:
   - Asegurar que el sistema de compilación elimine funciones y clases exportadas pero nunca invocadas.
   - Usar imports modulares (ej. `import { debounce } from 'lodash-es'` en lugar de importar la librería completa).
2. **Ofuscación y Shrinking**:
   - **Android**: Activar `minifyEnabled true` y `shrinkResources true` en `build.gradle` (R8).
   - **Flutter**: Utilizar `--obfuscate --split-debug-info`.
   - **Web**: Bundlers modernos (Vite, Rollup, Webpack) con compresión Brotli / Gzip.
