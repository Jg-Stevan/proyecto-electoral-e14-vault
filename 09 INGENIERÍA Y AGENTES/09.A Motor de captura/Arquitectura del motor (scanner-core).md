---
proyecto: Proyecto Electoral E-14
tipo: ingenieria
estado: vigente
actualizado: 2026-10-15
tags: [ingenieria, motor, scanner-core]
---

# Arquitectura del motor (scanner-core)

> El escáner se construyó **una vez** como motor reutilizable y luego se le pusieron dos caras: el laboratorio y la app electoral.

## 1. Dónde vive el motor (post-split 2026-10-15)

El monorepo original se dividió en 2 repos + 1 archivo (ver [[Estado del repositorio web-scanner]]):

```
web-scanner/                    ← github.com/Jg-Stevan/web-scanner
├── packages/scanner-core/      # motor HEADLESS (sin UI) — 20 módulos
│     └── se publica como librería npm: @jg-stevan/scanner-core
└── apps/scanner-lab/           # laboratorio: la app real y banco de pruebas

digielect14/                    ← github.com/Jg-Stevan/digielect14
└── (app electoral en la raíz)  # consume @jg-stevan/scanner-core@^0.1.0 desde npm

web-scanner-monorepo-viejo/     ← archivo histórico (NO borrar): historia, PRs, tags
```

| Pieza | Rol | Detalle |
|---|---|---|
| `packages/scanner-core` | Motor | Prohibido importar `react`, `next/*`, `@/components`. Contrato congelado vía subpaths. Se publica con `bun run publish:stage` |
| `apps/scanner-lab` | Laboratorio | Dueño de `public/scanner/detection-worker.js` y `public/vendor/opencv-4.5.5*.js`. Fuente de verdad (regla de oro #0). Consume el core por workspace (src, transpilePackages) |
| `digielect14` | Producto electoral | Recibe el MOTOR por versión npm (`^0.1.0`); las mejoras de UI siguen exigiendo copia manual probada en el lab |

**Cambio de modelo (2026-10-15):** el motor ya no viaja por workspace al producto: se publica a npm y el e14 sube la versión. El flujo de publicación usa staging (`.publish/`) porque ni `bun publish` ni `npm publish` aplican `publishConfig.exports` (pnpm-only).

## 2. Módulos del core (los que importan)

| Módulo | Qué resuelve |
|---|---|
| `image-processor.ts` | **`PROCESSED_MAX_LONG_SIDE = 4032`** (L58), `IMPORT_MAX_LONG_SIDE`, `processImagePrecise`, warp |
| `detector-client.ts` | Enlace con el worker de detección de bordes |
| `sensor-profiler.ts` | `overCap`, `buildCappedPhotoSettings`, `clampBlobToSafeCap` — evitan decodificar blobs de 48/108/200 MP |
| `quality.ts` | Gates de captura (`F-STAB`, `SHUTTER_SUSTAIN_MS`) |
| `frame-loop.ts` | Bucle de frames y análisis |
| `ocr.ts` | Tesseract local, `downscaleForOcr` a 1600 px, `getOcrWorker` singleton |
| `pdf-export.ts` | `ATTEMPTS_STANDARD`, `pageForAttempt`, `budgetBytes` |
| `store.ts` | `view/setView`, `addCapturePage`, `saveSessionToDocument`, `processedMeetsTarget` |
| `types.ts`, `page-store.ts` | Tipos y almacén de páginas |

**Motor de visión:** worker propio (Sobel + DFS) con **OpenCV.js self-hosted (8,6 MB)** opcional → `precision.engine: "worker" | "canvas"`. Detección en cascada multi-umbral; `QUAD_MIN_AREA_RATIO = 0.25`.

## 3. ⭐ La constitución del proyecto

Repetida en casi todos los documentos, y es la regla que explica **todas** las decisiones técnicas:

> **"El rendimiento y la estabilidad se ganan con memoria, CPU de análisis y CSS — NUNCA degradando el dato de imagen."**

De ahí se derivan las reglas duras:

| Regla | Por qué |
|---|---|
| **Tope único 4032 px** | 4032×3024 ≈ 12,2 MP está justo bajo el límite del canvas de iOS (~16,7 MP). *"Un tope mayor rompe iPhone"* |
| **"Máximo del lente" = nativo hasta 4032** | Sensores de 48/108/200 MP siguen topados: límite del canvas iOS y del heap de OpenCV WASM |
| **WYSIWYG estricto** | Preview y guardado usan **la misma constante**: se procesa una vez. *"Lo que ves ES la imagen guardada"* |
| **Export siempre a máxima** | El chip "Se guardará…" se eliminó (v10-B); cualquier valor ≠ máxima migra a máxima |
| **Downscale sí, recorte no** | El piso de 3200 px de gama baja es **post-captura**, jamás un recorte del encuadre |
| **R-10: data URLs en PNG** | *"No convertir a JPEG"* por decisión del repo |
| **Error #22 del repo** | Nunca `toDataURL` síncrono en canvas grande → usar `toBlob` |

## 4. 🔑 Regla de oro #0: el lab es la fuente de verdad

Está escrita en el `AGENTS.md` de la raíz del repo. Lo que ya existe y funciona en el laboratorio **se copia literalmente** al e14, con un comentario que declara el origen:

```ts
// Fuente: apps/scanner-lab/src/components/scanner/CameraView.tsx L116-L223
```

**El incidente que la originó:** durante la fase lógica alguien "simplificó" el arranque de cámara del lab y dejó solo la cascada `facingMode`. Resultado: **en muchos Android el acta se capturaba con la gran angular** (bug F-LENS en producción). De ahí sale la regla y la obligación de auditar copia por copia (`SPEC-e14-auditoria-copias`, hallazgos H1-H6: 13 copias fieles, 3 adaptaciones legítimas, 5 desviaciones).

## 5. Datos que validan el diseño

| Dato medido | Valor |
|---|---|
| Downscale 4032 → 3200 en gama baja | **~1 ms** (jamás recortar en captura: el downscale es gratis) |
| Downscale 2048 | 5-7 ms |
| Pantallas Android reales | **1080 px físicos de ancho** ⇒ 2048 ya oversamplea en fullscreen |
| Canvas máximo verificado | 16384 px en los 3 dispositivos |
| Lo caro de verdad | **encode/decode**: 730 ms / 105 ms en el A13 vs 371 / 33 en el A56 |

---

**Ver también:** [[Cámara, autocaptura y burst]] · [[Memoria, render y tiers]] · [[El puente - scanner-core-bridge]] · [[Estado del repositorio web-scanner]]
