---
proyecto: Proyecto Electoral E-14
tipo: ingenieria
estado: vigente
actualizado: 2026-10-13
tags: [ingenieria, motor, diagnostico, telemetria]
---

# Telemetría, diagnóstico y baseline

> *"La correlación la hace la telemetría, no la percepción del dueño."* Esta nota es el sistema de medición que hizo posible todo lo demás.

## 1. Los marks del pipeline

El motor emite marcas de tiempo con nombre fijo. **Regla dura: no renombrar ni borrar telemetrías** (los E2E de auditoría dependen de esos nombres).

| Mark | Qué mide |
|---|---|
| `boot_ready` | Arranque de la app |
| `camera_open` / `camera_ready` | Apertura de cámara |
| `fps_camera {early\|late}` | FPS; la 2.ª muestra si la cámara dura >30 s (**efecto térmico**) |
| `capture_fired` / `capture_stable` / `capture_shot` | La ventana del burst: `{burstExitReason, burstFrames, heapProbe?, pipe{sniffMs,dataUrlMs,toMarkMs}, bgJobsAtShot}` |
| `processed` / `write` / `page_saved` | Procesado y guardado (`{bytes, pxLong}`) |
| `editor_open` / `editor_close` | Entrada al editor |
| `editor_first_render {previewTier}` / `editor_preview_upgraded` | Primer pintado y upgrade escalonado |
| `filter_applied {id, inputPx}` | Filtros |
| `fps_auto` | Primer pan/zoom, 1× por sesión |
| `export_start` / `export_end` | Export (png + pdf con pages/px) |
| `ocr_start` / `ocr_done` | OCR (4 sitios de llamada) |
| `longtask` | Tareas largas (buffered) |

Ring de **`MAX_MARKS` 600**, noop en SSR.

## 2. Las sondas en `window` (para QA sin instrumentar nada)

| Sonda | Para qué |
|---|---|
| `__perfProbe` | Idéntica en Plan A y Plan B ⇒ **comparabilidad 1:1** |
| `__diagTools {snapshot, bench, report, txt, download, share, copy}` | Informe completo (Web Share API con fallback + manejo de `AbortError`) |
| `__burstTools {set, get, reset}` / `__lastBurst` | Presupuesto del burst (palanca sin código: `set({timeBudgetMs: 6000})`) |
| `__lastShutter` | Decisión de la auto-captura |
| `__streamUpgrade` / `__displayMax` / `__displayUrlCalls` | Upgrades y fuga del display |
| `__cameraChoice` | Qué cámara eligió F-LENS |
| `__crashLog` | Crash-reporter (observe-only) |
| `__tierTools` / `__deviceProfile` | Tiers y perfil de dispositivo |
| `__captureToEditor` / `__captureToPage` / `__analysisW` / `__analysisMs` / `__prewarm` | Fluidez de la captura |
| `__techTools` | Plan B (WebCodecs/WebGPU) |

## 3. El informe de diagnóstico (JSON v1 → v5)

- Formato evolutivo: **v1 → v2 → v3 → v4 → v4.1 → v4.2 → v4.3 → v5**, con plantilla TXT exacta.
- Salida doble: **TXT (cómodo de compartir por WhatsApp) + JSON (para comparar)**.
- 🔐 **Privacidad estricta (§1.5): sin imágenes, sin documentos, sin `ocrText`** — verificado en el DoD.
- El **juez objetivo está fuera del repo**: `analizador-diag-v4.js` (node puro) con **umbrales fijos y públicos**:

| Umbral | Valor |
|---|---|
| `TOL_ARITMETICA_MS` | 250 |
| `DATAURL_DOMINANTE` | 0.50 |
| `TRABAJO_REAL_BAJO` | 0.15 |
| `CONGESTION_ALTA` | 0.60 |
| `JOBS_FACTOR_CAP1` | 0.50 |
| `MURO_2000_RANGO` | 1500-2600 |

> *"Mismo JSON → mismo veredicto."* Así una auditoría no depende de quién la lea.

## 4. Protocolo de crash-test (experimento v11)

| Escenario | Qué hacer |
|---|---|
| A | 1 página con zoom |
| B | 3 páginas |
| C | 6-8 páginas |
| D | Export doble |

**Señales que hay que distinguir:**
- **Banner rojo** = error de JS (lo captura el crash-reporter).
- **Pantalla blanca o recarga** = **OOM nativo**, sin banner.
- **Congelación >30 s** = fallo.

En gama baja usar el **obturador MANUAL** (la auto-captura podía no disparar; lo arregla v9).

**Protocolo nocturno (v13):** *"punto atascado >45 min se documenta y se salta; 1 punto = 1 commit; sin force-push ni rebase"*.

## 5. 📊 La BASELINE (medición del 2026-10-12)

**Dispositivos medidos:**

| | **SM-A566E** (Galaxy A56) | **SM-A135M** (Galaxy A13) | PC (Windows) |
|---|---|---|---|
| Segmento | gama media-alta 2025 | **gama baja 2022 (el caso crítico)** | referencia |
| RAM / heap JS | 8 GB / 1965 MB | **4 GB / 961 MB** | 8 GB / 4128 MB |
| CPU bench (worker) | 21-25 ms | **82 ms (~3,5× más lento)** | 60 ms |
| Encode JPEG 3200 | 371-428 ms | **730 ms** | 209 ms |
| Decode 3200 → bitmap | 32-33 ms | **105 ms** | 54 ms |
| Refresh medido | 60 Hz | 43 Hz | 60 Hz |

**Del flujo real en el A56:** arranque 122 ms ✅ · cámara 57-59 fps ✅ · captura→guardada 1,19-1,26 s 🟡 · **primer render del editor 4,9-5,1 s 🔴** · **filtros 4,6-4,8 s 🔴** · OCR frío 5,26 s 🟡.

**Los 3 hallazgos que orientaron todo el trabajo posterior:**

1. **🔴 Editor primer render ~5 s** — **no era CPU** (decode 33 ms + downscale 1 ms ≈ 50 ms de trabajo real). Sospecha correcta: **chunk lazy por red**. *Cómo confirmarlo: abrir el editor 2ª vez sobre la misma página.*
2. **🔴 Filtros ~4,7 s** — CPU-bound real a 4032 px ⇒ objetivo exacto del Plan A (filtrar sobre resolución de trabajo baja, no sobre 4032).
3. **🟡 Captura→guardada ~1,2 s** — coherente con encode ≈600 ms; recortable con single-encode/Blob-first.

**Lo que ya estaba bien:** arranque 122 ms, cámara 57-59 fps, pan/zoom 65-72 fps, heap pico 10 MB, OCR frío 5,3 s.

**Acciones que quedaron pendientes de esa medición:**
- ⚠️ **Re-correr el diagnóstico en el A13 con flujo completo** (no abrió cámara: solo tenemos su hardware, no su flujo).
- Test del primer render: abrir el editor 2 veces seguidas y comparar (si cae a <1 s ⇒ era red/compile ⇒ solución de bundle, no de pipeline).
- Guardar siempre el **par TXT + JSON**; para métricas usar el JSON.
- Los benchmarks cachean 7 días (`benchAgeDays`).

## 6. Marca de agua metodológica

- El dueño **eliminó las pruebas A/B manuales** por sesgo de percepción: solo valen los datos.
- Los números de predicción se marcan **como predicción**: *"no citar como medidos"*.
- Los lotes de QA (1 a 5) y sus JSON viven **fuera del vault** (en los tar).

---

**Ver también:** [[Cámara, autocaptura y burst]] · [[Memoria, render y tiers]] · [[Trampas de iOS y WebKit]] · [[Método de trabajo (spec, implementa, audita)]]
