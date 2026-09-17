---
name: performance-agent-skill
description: Transversal Performance, Memory Profiling, Render Optimization, and Resource Budget Skill for AI Agents.
---

# ⚡ Performance Agent Skill Directive

## Bootstrap de la Habilidad

Al detectar `$performance`, `$performance:*`, o tareas relacionadas con caídas de FPS, lentitud, memory leaks, bundle bloating, startup slow o consumo de batería, cargar en orden:

1. `.skill/performance-agent-skill/SKILL.md` ← **Directiva principal y matriz operativa**
2. `.skill/performance-agent-skill/core/commands.md`
3. `.skill/performance-agent-skill/core/brain.md`
4. `.skill/performance-agent-skill/core/path_map.md`
5. `.skill/performance-agent-skill/adapters/<plataforma>.md` ← según el stack del proyecto

## Reglas Canónicas de Rendimiento Agnóstico

- **Cero Optimización por Intuición**: Queda terminantemente prohibido modificar código bajo supuestos teóricos sin evidencia previa de traza, muestreo de CPU o perfilado de memoria.
- **Medición Obligatoria Antes vs Después**: Toda optimización debe demostrar un delta cuantificable (ej. reducción de ms de render, MB de heap, bytes de bundle o número de rebuilds/layouts).
- **Prohibición de Micro-Optimizaciones Prematuras**: No comprometer la legibilidad ni la arquitectura por ganancias insignificantes (<2%) fuera del camino crítico (hot-path).
- **Inviolabilidad del Hilo Principal**: Toda tarea bloqueante de I/O, parsing masivo, cómputo criptográfico o decodificación pesada debe ejecutarse fuera del hilo de UI (offloading a workers, isolates o background threads).
- **No Duplicación de Gobernanza**: Esta skill no implementa ni redefine reglas de sesión de `*-agent-rules`. Persiste hallazgos exclusivamente en `overview/work/skill/performance.md`.
