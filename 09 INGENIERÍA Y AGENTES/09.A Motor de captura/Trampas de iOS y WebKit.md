---
proyecto: Proyecto Electoral E-14
tipo: ingenieria
estado: vigente
actualizado: 2026-10-13
tags: [ingenieria, motor, ios, webkit]
---

# Trampas de iOS y WebKit

> Todo lo que funciona en Chrome y **rompe solo en iPhone**. Esta lista vale oro: cada línea costó un bug en producción.

## 1. Detección de dispositivo

| Trampa | Consecuencia |
|---|---|
| **`navigator.deviceMemory` NO existe en iOS** | Los tiers de display penalizaban al iPhone (café para todos) |
| **iOS 17+/26 SÍ expone `ImageCapture`** | El iPhone entraba al régimen Android, que estaba roto para iOS → negro |
| **`navigator.vibrate` no existe en iOS** | No usar vibración como feedback |
| **Chrome en iPhone (CriOS) se comporta como iOS** | Probar con `IS_ANDROID` no alcanza: hay que mirar `CAN_TAKEPHOTO` |

## 2. CSS que no se comporta

| Trampa | Por qué pasa | Fix |
|---|---|---|
| **`will-change: transform` permanente** | iOS puede dejar la capa rasterizada a **baja resolución** → la imagen se ve suave/borrosa al ampliar | Dinámico: `willChange: zoomed ? "transform" : "auto"` (y `zoomPct > 100` en la presentación) |
| **`max-height: 100%` dentro de un padre de altura `auto`** | **WebKit no lo resuelve** (Chrome sí, para flex items) → la foto se maqueta a tamaño natural, desborda y el `overflow-hidden` la recorta | Wrapper `h-full w-full` |
| **`touch-action`** | **Se respeta solo en el elemento tocado**; un ancestro con `touch-none` no basta | `touch-none` en el `<img>` **y** el wrapper + `touchmove` **no pasivo** con `preventDefault` |
| **Safe areas** | La PWA instalada y el navegador recortan distinto | `viewport-fit=cover` + `dvh` + `pt-safe`/`pb-safe` propios |

## 3. Gestos (el capítulo más doloroso)

| Trampa | Detalle |
|---|---|
| **`pointerleave` espurio** | iOS lo dispara a mitad del pan al recomponer capas → mataba el gesto. **Fix: `onPointerLeave` solo con ratón** |
| **`touchmove` pasivo** | Sin `{ passive: false }` el `preventDefault` no sirve y el navegador se queda el gesto |
| **Fórmula de clamp incorrecta** | La vieja sobre-paneaba y dejaba huecos negros. **Correcta:** `bx = max(0, (img.offsetWidth × s − stage.clientWidth) / 2)` midiendo el `<img>` **y** el stage |
| **Tope pequeño no es bug** | Página de 200 px en stage de 390 px ⇒ tope 55 px. *"Matemática correcta, no bug"* |
| **Doble toque vs tap simple** | Tap sobre la imagen **diferido 320 ms** para no comerse el doble toque; tap fuera inmediato |
| **Semántica del tap** | `TAP_SLOP` 8 px, pinza 1-3× (modo card) y 6× (modal), doble toque 2,5× |
| **`closest("button")`** | El guard del visor obligó a convertir la tarjeta de `<button>` a **`<div role="button" tabIndex>`** (HTML interactivo anidado no es válido), conservando `aria-label` + Enter/Space |

## 4. Congestión y rendimiento (no es culpa del CSS)

- **El tramo opaco de ~14,7 s en el A13:** 0,3% trabajo real + 47,4% long tasks (>50 ms) + **52,7% invisible**. La sonda de heap dio **serie plana** ⇒ **no es GC de JS** ⇒ es congestión **interna del navegador** (decode/codec/ISP del 12 MP fuera del heap JS).
- **Corolario metodológico:** *"las long tasks >50 ms son una **cota inferior** de la congestión — es el punto ciego del observador"*.
- **El "misterio del editor" se resolvió con un experimento natural:** 4,2 s con red a 1,5 Mb/s → **1,3 s con 10 Mb/s**. En gama alta el piso era **descarga del chunk por red**; en el A13 es **CPU de imagen**.
- **El muro se retrasa solo:** configurado en 2000 ms disparó a 2592/2646/3232 ms porque `waitForPhotoBudget` usa `setInterval` y el tick se encola en el hilo congestionado. Coste asumido; `photoGraceMs` lo compensa.
- **La sonda de estabilidad se ahoga:** mide 130-190 ms/muestra en el A13 (13-24× el objetivo <10 ms) porque **sufre la misma congestión que mide**.

## 5. WYSIWYG y honestidad visual

- El editor y la pantalla completa deben consumir **la misma fuente** (v11) — si no, el usuario ve dos calidades distintas del mismo dato.
- **Los data URLs del filtro son PNG por decisión del repo** (R-10): *"no convertir a JPEG"*.
- **Nunca `toDataURL` síncrono en canvas grande** (error #22 del repo) → usar `toBlob`.

## 6. Reglas prácticas para la próxima vez

1. **Todo fix de UI se prueba primero en el lab y se copia verbatim** con `// Fuente: …`.
2. **Nunca `exact` en constraints** de cámara.
3. **Un track muerto no dispara `onended`:** hay que latir.
4. **En iOS, sin gesto no hay 12 MP.**
5. **Antes de optimizar, medir:** la correlación la hace la telemetría, no la percepción.
6. **Si el fix es "solo CSS" en iOS, sospecha de `will-change`, `touch-action` y alturas `auto`.**

---

**Ver también:** [[Cámara, autocaptura y burst]] · [[Memoria, render y tiers]] · [[Telemetría, diagnóstico y baseline]] · [[Lecciones y trampas]]
