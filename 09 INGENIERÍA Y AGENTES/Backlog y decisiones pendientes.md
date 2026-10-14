---
proyecto: Proyecto Electoral E-14
tipo: mapa
estado: vigente
actualizado: 2026-10-13
tags: [ingenieria, backlog, decisiones, pendientes]
---

# 📋 Backlog y decisiones pendientes

> **La lista viva de lo que falta.** Cada item abierto aquí está enlazado al detalle en su nota de origen.
> Reglas de trabajo: nada se cierra sin justificación; cada cierre se registra en el diario (`07 DIARIO`).

---

## 🔴 Bloqueantes (no se puede avanzar sin resolverlos)

### B-1 · Cerrar el PR #47 (revert del guard-borde)
- **Qué:** el revert del guard de borde está abierto y es el **riesgo mayor**.
- **Esfuerzo:** 30 segundos (cerrar + limpiar rama `revert-44`).
- **Detalle:** ver [[Tablero de próximos pasos]] y [[Guard de completitud (borde)]].

### B-2 · Merge de PR #39 (burst) y PR #43 (F2)
- **Qué:** ambos **aprobados**, esperando merge.
- **Desbloquea:** el **LOTE 2** (preview escalonado 2048→4032).
- **Ver:** [[Cámara, autocaptura y burst]] y [[Memoria, render y tiers]] §6.

### B-3 · Orden de integración
- **Opciones:** `v12b → rebase v13 → re-auditoría`.
- **Decisión pendiente:** definir la secuencia de rebases.

### B-4 · Rebasar el rediseño Stitch sobre `main`
- **Qué:** el rediseño Stitch es el sistema visual vigente, pero necesita rebase sobre `main` con **auditoría E2E**.
- **Dependencia:** notificaciones persistentes (ver [[Estados, intentos y contingencias]] §7).

---

## 🟠 Decisiones de producto abiertas

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

### DEV-1 · Cerrar PR #47 y limpiar `revert-44`
- (ver B-1)

### DEV-2 · Mergear #39 y #43 → desbloquea LOTE 2
- (ver B-2)

### DEV-3 · Implementar el LOTE 2 (preview escalonado 2048→4032)
- Especificación entregada (`F2-E`).
- `PREVIEW_FAST_LONG_SIDE = 2048` + upgrade en background con `cacheKey` condicional.
- Cancelación obligatoria si se desmonta / cambia `cacheKey` / la cámara dispara.
- Invariante: con `maxLongSide = 4032`, byte a byte el de hoy.
- **Ver:** [[Memoria, render y tiers]] §6.

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
