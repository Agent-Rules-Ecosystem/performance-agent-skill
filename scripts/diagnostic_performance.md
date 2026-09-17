# 🩺 Guía de Script de Diagnóstico de Rendimiento (Diagnostic Performance)

> Script y comandos explicados para ejecutar diagnósticos rápidos de rendimiento según el entorno.

---

## 📱 Diagnóstico en Flutter

```bash
# Ejecutar la aplicación en modo Profile con DevTools activo
flutter run --profile -d <device_id>

# Abrir DevTools en el navegador para profiling de Timeline y Memory
flutter run --profile --trace-startup
```

---

## 🤖 Diagnóstico en Android

```bash
# Grabar traza de Perfetto en dispositivo conectado (duración 10 segundos)
adb shell perfetto \
  -c - --txt \
  -o /data/misc/perfetto-traces/trace.perfetto-trace <<EOF
buffers: {
    size_kb: 63488
    fill_policy: RING_BUFFER
}
data_sources: {
    config {
        name: "linux.ftrace"
        ftrace_config {
            ftrace_events: "sched_switch"
            ftrace_events: "power/cpu_frequency"
            ftrace_events: "power/cpu_idle"
            atrace_categories: "view"
            atrace_categories: "gfx"
            atrace_categories: "am"
        }
    }
}
duration_ms: 10000
EOF

# Descargar la traza al host
adb pull /data/misc/perfetto-traces/trace.perfetto-trace ./trace.perfetto-trace
```

---

## 🌐 Diagnóstico en Web con Lighthouse CLI

```bash
# Ejecutar auditoría automatizada de Core Web Vitals en modo headless
npx lighthouse-ci collect --url="http://localhost:3000" --numberOfRuns=3
```
