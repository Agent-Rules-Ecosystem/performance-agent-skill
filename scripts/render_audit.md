# 🎨 Guía de Script de Auditoría de Render (Render Audit)

> Procedimientos de auditoría visual para detectar layout shifts, sobre-dibujado y caída de FPS.

---

## 📱 Detección de Rebuilds en Flutter

```dart
// En main.dart para habilitar visualización de repintado
void main() {
  debugRepaintRainbowEnabled = true; // Colorea de arcoíris cada repintado
  runApp(const MyApp());
}
```

---

## 🌐 Detección de Long Tasks en Web (PerformanceObserver)

```javascript
// Script para registrar en consola cualquier Long Task (> 50ms)
const observer = new PerformanceObserver((list) => {
  for (const entry of list.getEntries()) {
    console.warn(`⚠️ [LONG TASK DETECTADA] Duración: ${entry.duration.toFixed(2)}ms`, entry);
  }
});

observer.observe({ entryTypes: ['longtask'] });
```

---

## 🤖 Detección de Jank Frames con `dumpsys` en Android

```bash
# Extraer estadísticas de gfxinfo para el paquete de la aplicación
adb shell dumpsys gfxinfo <package_name> framestats
```
