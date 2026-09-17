# 📦 Guía de Script de Análisis de Paquete (Bundle Report)

> Comandos y herramientas para generar reportes visuales del tamaño de los binarios y dependencias.

---

## 📱 Análisis de Tamaño en Flutter

```bash
# Generar análisis de tamaño detallado de APK / AAB
flutter build appbundle --analyze-size --target-platform=android-arm64

# Generar archivo JSON de análisis para inspección en DevTools
flutter build apk --analyze-size --json=build/bundle_analysis.json
```

---

## 🌐 Análisis de Bundle en Web (Webpack / Vite / Rollup)

```bash
# Generar visualizador de bundle con Rollup Visualizer (Vite)
npm run build -- --mode analyze

# O utilizando Source Map Explorer
npx source-map-explorer 'dist/assets/*.js'
```

---

## 🤖 Análisis de APK / AAB en Android

```bash
# Usar APK Analyzer desde CLI
apkanalyzer -h apk summary app-release.apk

# Inspeccionar desglose de archivos DEX y recursos
apkanalyzer dex packages app-release.apk
```
