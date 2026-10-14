---
proyecto: Proyecto Electoral E-14
tipo: ingenieria
estado: vigente
actualizado: 2026-10-13
tags: [ingenieria, motor, camara, burst]
---

# Cámara, autocaptura y burst

> La parte más peleada del proyecto: conseguir que la foto salga **a resolución completa** sin colgar el teléfono. Cada letra aquí costó un PR y una auditoría.

## 1. Regla E3 de `getUserMedia`

**Solo constraints `ideal`, nunca `exact` ni `min`** — rechazan y rompen la apertura. Única excepción: el `facingMode: {exact}` preexistente, con su cadena de fallback documentada.

## 2. Dual pipeline de captura

| Capa | Resolución | Para qué |
|---|---|---|
| Preview | 960×540 @ 30 fps (lab) · 1920×1080 (e14) | Visor fluido y análisis de bordes (se remuestrea a 400 px) |
| Foto | **Resolución del sensor** vía `ImageCapture.takePhoto()` (12-48 MP) | El dato que se guarda |

**F-SENSOR-PROFILER**: mide el sensor nativo y pide la foto **dentro de un tope seguro por gama** (4032 alta · 3200 media/baja). El blob gigante **jamás se decodifica** → adiós al crash de pestaña por fotos de 48/108 MP. Tres capas: `photoSettings` al ISP, `clampBlob`, decode único.

## 3. F-LENS v4: elegir la cámara correcta

Sondeo **secuencial** de todas las cámaras (cerrando cada una), `chooseMainProbe` por autofoco real y mayor resolución, con **regex que expulsa la gran angular**:

```
/ultra|gran angular|wide|angular|tele|teleobjetivo/i
```

Incluye fix de zoom 0.5× → 1× y telemetría `window.__cameraChoice`. La cascada `facingMode` queda **solo como red de seguridad**.

> Este fue el **bug de producción** que originó la regla de oro #0: al "simplificar", los Android capturaban con la gran angular.

## 4. El caso iOS (el más traicionero)

**Hallazgo clave:** iOS 17+/26 **sí expone `ImageCapture`** → el iPhone entraba al mismo régimen que Android… que estaba roto para iOS (toast "alta resolución" + `takePhoto` colgado → pantalla negra).

| Problema | Causa real | Fix |
|---|---|---|
| "iPhone se ve de muy baja calidad" | `canTakePhotoRef` solo era `true` con `ImageCapture`; en iOS Safari era `false` ⇒ se usaban **frames de `<video>` de 960×540 (0,5 MP)** | Régimen por `IS_ANDROID` + `CAN_TAKEPHOTO` |
| Cámara negra permanente | El watchdog solo se recuperaba vía `onended`; un **track muerto en silencio** nunca lo dispara y el status seguía "live" | **Latido `requestVideoFrameCallback`** + `visibilitychange` + `isStreamDead() \|\| stale > 4 s` en foreground |
| Auto-captura muerta | El stream se quedaba en 960×540 | Subida progresiva por `applyConstraints` a **4032×3024 tras 2000 ms** (solo no-Android + auto ON), con `__streamUpgrade` y **sin toasts** |

**Límite de plataforma aceptado:** en iOS, **12 MP sin gesto del usuario es imposible**. El techo de la auto-captura es la resolución del stream. El disparo **manual** abre la cámara nativa (`<input capture>`) — *"no tocar"*.

**Verificado empíricamente:** Safari iOS → 1920×1440 + upgrade · Chrome iPhone (CriOS) → idéntico a iOS · Pixel 7 → 960×540 sin upgrades · Chrome desktop → 1920×1440.

## 5. F-ZSL: anti tap-shock

Buffer **Best-Shot** de los últimos 8 frames: si la foto de alta resolución no responde, el frame **más nítido capturado 80-450 ms ANTES del toque** sustituye a la foto sacudida (patrón Zero Shutter Lag de las cámaras nativas).

**Ranking (v7.3):** `SHARPNESS_KEEP_RATIO = 0.85` — una foto 4K solo gana si conserva ≥85% de la nitidez. **Bug previo:** se elegía por `lapVar` puro y un frame del ring (**cap 1280 px**) podía ganarle a un snapshot full-res.

## 6. Autocaptura invariante al FPS (v9)

**El bug:** `F-STAB` exigía 6 muestras ≥260 ms — **imposible a ≥24 fps**, así que la auto-captura no disparaba en los equipos buenos.

**El rediseño:** ventana **temporal pura de 1200 ms** (no de muestras):

| Elemento | Valor |
|---|---|
| Ventana | 1200 ms |
| K dinámico | `max(2, ceil(0.7 × ventana))` |
| Tramo continuo | ≥400 ms, con `gapTolerance = max(1.6 × mediana, 350)` |
| `fpsEff` | Por mediana, clamp 1.5-60 |
| Historia | `MAX_HISTORY` 24 → **36** |

Regresión crítica bloqueada: barrido rápido (U5). Disparan bien fluido (U1) y gama baja a 3 fps (U2).

## 7. Burst con presupuesto de tiempo (la saga v1→v5)

**El problema:** el A13 tardaba **16,8-17,2 s por captura**, sin límite global.

**El diseño (zona sancionada: solo `capture_fired → capture_shot`):**

| Pieza | Decisión |
|---|---|
| Muro de tiempo | **2000 ms** determinista desde `capture_fired` |
| Reloj | `performance.now()` **monotónico — nunca `rAF`** (el rAF se pausa) |
| Tick | 100 ms |
| Salida por estabilidad | Sampler **64×64** de luma, cadencia 750 ms, umbral 2.5, 2 deltas consecutivos → salida mínima ~1,5 s |
| Coste del sampler | **0,4 ms/muestra** (prohibido comparar a 4032 px) |
| Techo | Dispatch ≤3 candidatos, ring ≤8 |
| Cancelación | Real: `FileReader.abort()` con deadline, `finish()` idempotente |
| `enabled: false` | = **legacy exacto** |

**🔬 Hallazgo forense central:** la promesa de `takePhoto` **se descarga en cuanto el presupuesto libera el hilo**. La foto huérfana llegaba 3/3 completa (3,2-3,7 MB) **entre 18 y 42 ms DESPUÉS del corte**. Conclusión: **subir el muro mueve el corte y la foto juntos ⇒ jamás puede hacer ganar la foto.** Por eso hizo falta la **Fase D: `photoGraceMs = 2000`** (clamp 0-5000), que da gracia en ambas ramas de corte y **no blacklistea** la foto recuperada.

**Resultado físico de la gracia:** páginas a **4032 px** (antes 960), PDF 3159 px / 271 KB nítido (antes 709 px / 55 KB), OCR 1383 chars (antes 202).

**Cierre de la saga (v5):** el muro por defecto bajó a 2000 y se añadió una **sonda de heap** (`performance.memory`). Resultado: **serie plana 10,7 MB, sierra 0,0 MB** ⇒ **GC de JS refutado** ⇒ la congestión es **interna del navegador** ⇒ **piso del A13 ≈ 18 s/captura, sin arreglo de app**.

## 8. Coste real por dispositivo (captura → guardada, mediana)

| Dispositivo | Resultado |
|---|---|
| **iPhone (iOS 26.6 Safari)** | 🥇 **El más rápido**: cpuBench 7 ms, captura 519 ms @4032, editor 1,78 s |
| **Galaxy A56** (8 GB) | ~1,0-1,17 s · páginas 4000 px · gap mediano 887 ms |
| **PC** | `frame_cap` ×3, gap 251 ms, páginas 4032 px |
| **Galaxy A13** (4 GB) | 🔴 **piso ≈18 s/captura** con calidad 4032 sostenida |

**Congestión del A13:** cpuBench 82 ms (3,5× el A56), heap 961 MB, `fpsLate` 5, **435 long tasks en 57 s**, `windowCongestion` 8,8-9,7 s.

## 9. Lecciones de cámara que no hay que olvidar

1. **El "toast" no es la solución:** el dueño pidió **cero mensajes**; la app debe resolver sola.
2. **Un track muerto no avisa:** hay que **latir** para detectarlo.
3. **`takePhoto` no se puede cancelar:** hay que dejarla huérfana y decidir con gracia.
4. **Los frames de preview no son el dato:** son 0,5 MP; el acta ilegible viene de ahí.
5. **La promesa tarde no es una promesa fallida:** distinguir "llegó tarde" de "falló" (`wallCapped` vs timeout).

---

**Ver también:** [[Arquitectura del motor (scanner-core)]] · [[Memoria, render y tiers]] · [[Trampas de iOS y WebKit]] · [[Telemetría, diagnóstico y baseline]]
