# 📋 Checklist de Auditoría Móvil (Mobile Audit Checklist)

Plantilla de verificación rápida para apps móviles (Flutter, Android, iOS):

## ⚡ 1. Renderizado y Fluidez (FPS)
- [ ] ¿El frame rate se mantiene en 60 FPS / 120 FPS durante scroll continuo de listas?
- [ ] ¿Las listas usan reciclaje / virtualización de celdas (`ListView.builder`, `LazyColumn`, `LazyVStack`)?
- [ ] ¿Las animaciones, canvases interactivos, gestos multitouch y elementos de alta frecuencia tienen fronteras de repintado (`RepaintBoundary`, `drawingGroup`)?
- [ ] ¿Se evitaron operaciones de `saveLayer`, filtros de desenfoque complejos o sombras dinámicas en elementos móviles?

## 🧠 2. Memoria y Bitmaps
- [ ] ¿Los assets usan formatos optimizados (`.webp` frente a `.png` pesados) y las imágenes se decodifican ajustadas a la resolución de pantalla?
- [ ] ¿El cache global de imágenes tiene un límite de memoria estricto configurado (`imageCache.maximumSizeBytes` o equivalente)?
- [ ] ¿Se limpian controladores, streams, listeners y timers en el método de desmontaje (`dispose`, `onCleared`, `deinit`)?
- [ ] ¿Se verificó la ausencia de fugas de memoria con diff de heap snapshots (10 ciclos de apertura/cierre de pantalla)?

## 🚀 3. Startup & Ciclo de Vida
- [ ] ¿El tiempo de Cold Start es ≤ 1.5 - 2.0 segundos?
- [ ] ¿Los SDKs no críticos (analíticas, chat, marketing) se inicializan de forma diferida post-primer frame?
- [ ] ¿La pantalla inicial muestra estado skeleton inmediato sin esperar llamadas bloqueantes de red?

## 🔋 4. Batería y Tareas en Segundo Plano
- [ ] ¿Se detienen timers y animaciones cuando la app pasa a segundo plano (`paused`/`background`)?
- [ ] ¿Las tareas periódicas usan los schedulers del sistema (WorkManager, BGTaskScheduler) en vez de loops infinitos?

## ⚙️ 5. Concurrencia (Offloading CPU)
- [ ] ¿Las operaciones intensivas en CPU (generación de PDFs/reportes, compresión de medios, serialización de datasets `> 1MB`) se ejecutan en Worker Isolates (`compute()` / background threads) sin bloquear el hilo UI?
