---
proyecto: Proyecto Electoral E-14
tipo: mapa
estado: vigente
actualizado: 2026-10-15
tags: [ingenieria, backlog, decisiones, pendientes]
---

# 📋 Backlog y decisiones pendientes

> **La lista viva de lo que falta.** Cada item abierto aquí está enlazado al detalle en su nota de origen.
> Reglas de trabajo: nada se cierra sin justificación; cada cierre se registra en el diario (`07 DIARIO`).

---

## 🔴 Bloqueantes (no se puede avanzar sin resolverlos)

### D-1 · El estado `ADVERTENCIA` quedó muerto
- **El problema:** al subir `PISO_IDENTIFICADA` a 10, toda captura identificada da **10.0 → ÓPTIMA → auto-enviada**. La banda media (`6.5-7.9`) **solo existe en simulación**.
- **Opciones:**
  - **A.** Aceptar el código defensivo (piso AUTO solo se re-aplica si `scoreSimulado >= 8`).
  - **B.** Retunear el piso (bajar `PISO_IDENTIFICADA`).
- **Origen:** [[Doctrina del score y evaluarGate]] §7 y [[Contradicciones detectadas]] punto 4.

### D-2 · §9.12 — "papel completo sin votos"
- **El problema:** una hoja con **DIVIPOL + nivelación** (legible, sin matriz de votos) pasa el guard y podría salir ÓPTIMA.
- **Diseño preliminar listo**, con advertencia de calibración crítica: la evidencia mínima debe ser **un marcador de sección** (si no, mataría el score 7.0 del caso MEDIO).
- **Origen:** [[Guard de completitud (borde)]] §6 y [[Contradicciones detectadas]] punto 5.

### D-3 · Flujo de 2 páginas sin implementar
- **El problema:** el modelo es **1 acta = 1 captura**, pero el acta real dice "**Pag. 1 de 2**".
- **Decisión:** definir cómo se maneja la segunda página (¿captura encadenada? ¿selector de folio?).
- **Origen:** [[Contradicciones detectadas]] punto 6 y [[Modal de Auditoría - Revisión de Acta]].

### D-4 · Emitir la v2.0 del ERS
- **El problema:** el ERS se declaró "congelado" pero su **RN-02** (binario) contradice el modelo implementado (precedencia + bandas) y su **RNF-02** (4-8 GB) contradice el de tiers.
- **Solución:** emitir v2.0 con el modelo real de decisión. La fuente está en [[Doctrina del score y evaluarGate]].
- **Origen:** [[Contradicciones detectadas]] punto 7.

### D-5 · Plan A vs Plan B (futuro de memoria/render)
| Plan | Idea | Estado |
|---|---|---|
| **A** | Blob-first + pirámide de 2048 + single-encode | **VIVA** (rebase programado tras fix del burst) |
| **B** | WebCodecs + WebGPU/WGSL con fallback WASM; supersampling 6000→4032 opt-in | **CONDICIONADA** |
- **Decisión:** decidir el orden de integración. Ambos comparten la sonda `__perfProbe`.
- **Origen:** [[Memoria, render y tiers]] §5.

---

## 🟡 Contenido y documentación

### C-1 · Revisar la cuarentena `99 REVISAR`
- ~~CERRADO~~ → la carpeta no existe en el repo; referencia eliminada en esta pasada de limpieza.
- `ELECCIONES CONGRESO 2026.md` → **puedes borrarlo** (es otro escenario, confirmado).
- El resto: ver el `LEEME` de esa carpeta.
- **Origen:** [[Tablero de próximos pasos]].

### C-2 · Matriz de contingencias en nota propia
- Hoy vive dentro del [[Flujo de Validación y Aprobación Operativa]]. Merece su propia nota.

### C-3 · Nota por módulo con checklist de aceptación
- Un checklist de aceptación por cada módulo del sistema.

### C-4 · Formatos de exportación del módulo de informes
- Definir PDF / Excel / CSV para entrega a entes de control.

### C-5 · Recuperar o descartar el PDF faltante
- `EL CAIRO D - DOMINGO.pdf` (citado en [[Estructura y Significado del Código de Barras]]) **no está en el vault**.
- **Acción:** subir el archivo o enlazar otro (convertido a texto mientras tanto).

### C-6 · Las capturas de diseño (`05 DISEÑO UI`)
- La carpeta `05 DISEÑO UI` con `code.html` + `screen.png` de cada pantalla **no está en este vault**.
- Las 8 referencias en [[Interfaz Gráfica (Digitalizador)]] aparecen como enlaces rotos.
- **Acción:** re-incorporar las capturas o declarar la sección como referencia externa.

### C-7 · Design System
- [[Design System - Precision Monitor]] requiere consolidar colores, tipografía y componentes.
- **Origen:** [[Especificación de Requerimientos (ERS)]] RNF-04.

---

## 🔵 Desarrollo (trabajo de código)
### DEV-8 · Crear README.md en `apps/digitalizador-e14`

### DEV-4 · Resolver los 35 commits `v13-post-N` fuera de alcance
- QA físico o descartar.

### DEV-5 · Gates físicos G1-G4 de F2 en el A13
- Correr la batería de pruebas físicas.

### DEV-6 · Recrear las ramas `qa-v11/v12/v12b`
- Para tener URLs estables de Vercel.

### DEV-7 · Pruebas físicas pendientes
- [ ] Cámara 50 MP
- [ ] HEIC
- [ ] Linterna
- [ ] Auto armada
- [ ] Foto real de El Cairo

---

## ✅ Cerradas (historial)
  - ~~B-1 Cerrar PR #47~~ → nunca se mergeó; guard-borde #44 vigente en main.
  - ~~B-2 Mergear #39 y #43~~ → mergeados.
  - ~~B-3 Orden de integración~~ → resuelto de facto por la cadena de merges.
  - ~~B-4 Rebasar Stitch~~ → mergeado como PR #50 en main (b8cfb01).
  - ~~DEV-1..DEV-3~~ → ídem.
  - ~~DEV-4 (v13-post-N)~~ → integrado vía PR #51 (qa-v13 modo rápido).

| Item | Cómo se cerró |
|---|---|
| ~~Contradicciones 1, 2 y 3~~ | Resueltas por la implementación real ([[Contradicciones detectadas]]) |
| ~~Criterio de calidad~~ | Precedencia + bandas + pisos ([[Doctrina del score y evaluarGate]]) |
| ~~Hardware objetivo~~ | Tiers adaptativos ([[Memoria, render y tiers]]) |
| ~~Escenario electoral~~ | Presidenciales 2026, 2ª vuelta, exterior ([[Modelo del Acta E-14]]) |
| ~~ISO 1600 como HUD canónico~~ | Eliminado |
| ~~Botón "Auto" en la fila~~ | Auto fuera de la fila + armado por defecto |
| ~~"CLASIFICACIÓN PENDIENTE"~~ | Regla 4b resolvió El Cairo ([[Clasificación DIVIPOL]]) |
| ~~Duplicado exacto del design system~~ | Copia canónica en `05 DISEÑO UI`; duplicado en cuarentena |
| ~~Residuo conversacional de IA~~ | Eliminado de [[Panel de Control y Supervisión Global]] |
| ~~Exportar PDF del e14~~ | Eliminado por decisión del dueño ([[El puente - scanner-core-bridge]] §4) |
| ~~CTA manual "enviar a revisión humana"~~ | Eliminado (0 usos); solo sobrevive `escalarRevisionHumana()` |

---

## 🧭 Cómo usar este backlog

1. **Al empezar a trabajar**, abre esta nota y [[Tablero de próximos pasos]].
2. **Al cerrar un item**, mueve la línea a la sección **✅ Cerradas** con la nota de origen.
3. **Al abrir un item nuevo**, añádelo a la sección que corresponda por color.
4. **Cada semana**, revisa esta lista y la cuarentena (`99 REVISAR`).
5. **Cada mes**, abre el grafo y busca notas huérfanas (sin enlaces).

> La regla de oro: **nada se cierra sin justificación**. Cada cierre se explica en el diario y se enlaza aquí.

---

**Ver también:** [[Tablero de próximos pasos]] · [[Contradicciones detectadas]] · [[Inicio - Ingeniería y Agentes]]

---

## 🔵 Infraestructura post-split (2026-10-15)

### I-1 · Reconexión de Vercel Previews
- **El problema:** el flujo «PR → Vercel Preview → aprobación explícita → merge» (política documentada, PRs #21/#24) quedó conectado al repo archivo: los PRs de los repos nuevos nacen sin preview y el QA físico (iPhone) no tiene entorno.
- **El plan:** V0 dueño (importar los 2 proyectos en Vercel; web-scanner con Root Directory `apps/scanner-lab`) → V1 agente (`vercel.json` con `deploymentEnabled main:false` — Vercel SOLO previews, Pages única producción) → V2 agente (PR de prueba) → V3 dueño (branch protection).
- **Nota técnica:** las configs ya son Vercel-safe sin cambios de código (basePath solo con `BUILD_STATIC=1`; `output: standalone` = la «fase B» histórica del worker real).

### I-2 · Limpieza del registro npm
- `@jg-stevan/scanner-core@0.0.0-stage` (placeholder vacía del publish de prueba): unpublish del dueño dentro de las 72h.

### I-3 · `.env.example` faltante en el nuevo web-scanner
- La plantilla de env (DATABASE_URL de scaffolding Prisma + ZAI_API_KEY para OCR) quedó en el archivo. Copiar ese 1 archivo.
