---
proyecto: Proyecto Electoral E-14
tipo: ingenieria
estado: vigente
actualizado: 2026-10-13
tags: [ingenieria, motor, memoria, tiers]
---

# Memoria, render y tiers

> Cómo se sostiene 4032 px de calidad en un teléfono de 4 GB sin crashear. Aquí está la apuesta de arquitectura y el experimento que la validó.

## 1. El experimento v11: fuente única (validado con tu crash-test)

**El problema histórico:** *"el editor se ve peor que la pantalla completa"*. El editor mostraba JPEG 2560 q0.92 mientras la presentación usaba PNG 4032.

**Las opciones que se evaluaron:**

| Opción | Qué hacía | Destino |
|---|---|---|
| **v10-A** Display adaptativo por RAM | `≥6 GB → 4032` · `≥3 → 3200` · `<3 → 2560` · iOS sin `deviceMemory` → 3200 | **Apagado por v11** |
| **v10-C** El editor usa la misma fuente | Fidelidad total | Nunca se aplicó |
| **v11** Fuente ÚNICA PNG 4032 en todos los tiers | Editor y presentación consumen `entry.url` | ✅ **Permanente** (`255db2d`) |

**Cómo se decidió:** el dueño dijo *"solo quiero probar si se crashea"*. Se instrumentó un **crash-reporter observe-only** (banner rojo + anillo en localStorage ≤10 + `__crashLog`; **prohibido** `preventDefault`, retry o fetch) y se corrió un **crash-test de 4 escenarios**. Resultado: **en gama baja no crasheó** ⇒ la fuente única se quedó para siempre.

`makeDisplayUrl` sobrevivió como *seam* con contador `__displayUrlCalls` (0 en todas las verificaciones) y es **reversible con 1 commit**.

## 2. El presupuesto de memoria (lo que sí importa)

- **LRU de previews: 12 → 6 entradas.** Cada entrada son **PNG data URLs de ~10-25 MB** (×2 en memoria, porque las cadenas JS cuentan doble). *"Mismo presupuesto total."* **Prohibido eliminar el LRU.**
- **Fuga real detectada:** `createObjectURL` sin liberar ≈ **49 MB decodificados por página de 4032**. En v13 se verificó que `revokeObjectURL` **ya existía** en main (`finally`, `image-processor.ts:214-215`) — el agente solo añadió el comentario del invariante. Honestidad bien aplicada.
- **4 categorías de uso de `PROCESSED_MAX_LONG_SIDE` — intocables:** `EDITOR_DISPLAY 2048`, miniaturas, `pdf-export`, `ocr`.

## 3. Los tiers (y por qué se eliminó el benchmark)

**v3 eliminó el benchmark de dispositivo completo** (`device-capability.ts`, `ensureDeviceCapability`, `measureDeviceCapability`, `getCachedDeviceCapability`, `DeviceSection` de Ajustes, key `escaner-device-cap-v1`).

**Motivo:** el benchmark **penalizaba a iOS** (`deviceMemory` no existe → tier medium/low) y **re-escalaba el original de 12 MP a 3200**, es decir, degradaba el dato — justo lo que la constitución prohíbe.

**Sí sobrevive la protección de sensores gigantes** (`overCap`, `buildCappedPhotoSettings`, `clampBlobToSafeCap`): eso no degrada, evita decodificar blobs imposibles.

**El tier correcto (Plan A / `device-tier.ts`):**

| Elemento | Definición |
|---|---|
| Umbral | `deviceMemory ≤ 4 → low` (el A13 con `deviceMemory = 4` cae exacto en el umbral) |
| Fallback por timing | Degrada si **≥2 de 3 guardados > 3000 ms** |
| Override de QA | `localStorage scannerlab-qa-force-tier` + `__tierTools` |
| Persistencia | `sessionStorage` — sin persistir entre sesiones |
| **Invariante** | **El tier NO cambia captura ni análisis.** Prohibido bajar calidad como "optimización" |

**v13** añadió `device-profile.ts` (tier bajo/medio/alto por micro-benchmark + `scannerlab.deviceProfile.v1` + `__deviceProfile` y tier en el crash-reporter), **también sin cambiar comportamiento**.

## 4. v12: fluidez en gama baja (el "editor primero")

**El problema:** cámara lenta y editor tardío tras el disparo.

| Fix | Contenido |
|---|---|
| **12.1** Editor primero | Probe barato `createImageBitmap(blob, resize 400, low)` para `quality` + aspect → `addCapturePage` + `setView("editor")` inmediato → **un** decode pesado en background a los **2,5 s** |
| **12.2** Análisis adaptativo | **400 → 320 → 280 px** por mediana, con histéresis (1 abajo / 3 arriba, de a un nivel); `refScale` FIJO 400 para el historial de estabilidad |
| **12.2b** Precalentamiento | WASM con `requestIdleCallback` (fallback 1500 ms) + `loop.setIdle(true/false)` tras capturar |
| **12.3** Pill "Abriendo editor…" | 300 ms / 12 s, liberada en **todas** las rutas |

**Medido en E2E:** `__analysisW=320` con `__analysisMs=208` (bajó en vivo desde 400), `__prewarm={ok:true, ms:911}`, `__captureToEditor=2030 ms`.

**v12b (`capturePending`):** la cámara seguía visible durante la ventana del disparo → navegar al editor **en el disparo** con la cámara **montada y oculta** (una sola instancia `<video>`, stream vivo para la HAL y el ring del SUSTAIN v9), limpieza en 4 sitios + guard anti-carrera `captureEpochRef` + hardStop 12 s. Medido: `__captureToEditor` ≈31-157 ms, `__captureToPage` = 200 ms. **E2E 16/16 con UA Android.**

> **v12c fue RECHAZADA por el dueño** (frame congelado + flash + "mantén quieto"). En su lugar se entregó el instructivo de ramas congeladas para comparar versiones en el iPhone real.

## 5. Los dos planes de futuro

| Plan | Idea | Estado |
|---|---|---|
| **Plan A** — memoria/render | **Blob-first** + **pirámide de 2048** + *single-encode* (una sola codificación) + sonda `__perfProbe` | **VIVA** (rebase programado tras el fix del burst) |
| **Plan A addendum** | Tier-2: 4032 default / **3200 piso de gama baja**; OCR congelado; vectorización descartada | Vigente |
| **Plan B** — tech moderna Android | **WebCodecs** (`VideoDecoder`) + **WebGPU/WGSL** con fallback WASM; modo experimental **6000 → supersampling 4032** opt-in | **CONDICIONADA** |

Ambos comparten la **misma sonda `__perfProbe`** para comparabilidad 1:1 — comparar dos arquitecturas con el mismo instrumento.

## 6. LOTE 2: preview escalonado (lo más reciente, spec entregada)

**El problema:** el primer render del editor tardaba **38,4 s en el A13** porque el warp INTER_CUBIC de 12 MP corre en el worker.

**El diseño (F2-E):**

| Pieza | Decisión |
|---|---|
| Primer pintado | **`PREVIEW_FAST_LONG_SIDE = 2048`** — el warp escala ~cuadrático: `(2048/4032)² ≈ 0,26×`, y el downscale cuesta ~1 ms |
| Upgrade | Pasada FULL a 4032 en **background**, con reemplazo condicionado al `cacheKey` y mark `editor_preview_upgraded { ms, filterId }` |
| Tipo | `PreviewEntry.tier: "fast" \| "full"` |
| **Cancelación obligatoria** | Si se desmonta, cambia el `cacheKey` o la cámara dispara → **`bgJobsAtShot` debe quedar en 0** |
| F2-F | El fast path JPEG **solo en el primer intento** de la escalera de PDF (bug latente: todos los reintentos devolvían el mismo JPEG full-size) |
| F2-G | Diag `editor.firstRenderTier` + `editor.previewUpgrades` |
| **Invariante** | Con `maxLongSide = 4032` el comportamiento debe ser **byte a byte el de hoy**; el guardado (`store.ts` L303/462/587/713) queda **intocable** |

---

**Ver también:** [[Arquitectura del motor (scanner-core)]] · [[Cámara, autocaptura y burst]] · [[Telemetría, diagnóstico y baseline]] · [[Backlog y decisiones pendientes]]
