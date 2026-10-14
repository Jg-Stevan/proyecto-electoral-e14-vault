---
proyecto: Proyecto Electoral E-14
tipo: ingenieria
estado: vigente
actualizado: 2026-10-13
tags: [ingenieria, lecciones, trampas, aprendizaje]
---

# 💡 Lecciones y trampas

> **Todo lo que costó un bug en producción.** Esta nota es el **oro del proyecto**: cada línea es una cicatriz que no hay que volver a abrir.
> Antes de tocar cualquier subsistema, pásate por aquí.

---

## 1. Lecciones de cámara (no olvidar)

1. **El "toast" no es la solución:** el dueño pidió **cero mensajes**; la app debe resolver sola.
2. **Un track muerto no avisa:** hay que **latir** (`requestVideoFrameCallback` + `visibilitychange` + `isStreamDead()`) para detectarlo.
3. **`takePhoto` no se puede cancelar:** hay que dejarla huérfana y decidir con gracia (`photoGraceMs = 2000`).
4. **Los frames de preview no son el dato:** son 0,5 MP; el acta ilegible viene de ahí.
5. **La promesa tarde no es una promesa fallida:** distinguir "llegó tarde" (`wallCapped`) de "falló" (timeout).

> Detalle en [[Cámara, autocaptura y burst]] §9.

---

## 2. Lecciones de iOS y WebKit (el capítulo más doloroso)

| Trampa | La lección |
|---|---|
| **`navigator.deviceMemory` no existe en iOS** | Los tiers de display penalizaban al iPhone; usar `IS_ANDROID` + `CAN_TAKEPHOTO` |
| **iOS 17+/26 SÍ expone `ImageCapture`** | El iPhone entraba al régimen Android, que estaba roto para iOS |
| **`will-change: transform` permanente** | iOS deja la capa a baja resolución → imagen borrosa. Dinámico: `zoomed ? "transform" : "auto"` |
| **`max-height: 100%` en padre `auto`** | WebKit no lo resuelve → la foto desborda. Wrapper `h-full w-full` |
| **`touch-action`** | Se respeta solo en el elemento tocado; `touch-none` en `<img>` **y** wrapper |
| **`pointerleave` espurio** | iOS lo dispara a mitad del pan. **Fix: `onPointerLeave` solo con ratón** |
| **`touchmove` pasivo** | Sin `{ passive: false }` el `preventDefault` no sirve |
| **Safe areas** | PWA instalada y navegador recortan distinto: `viewport-fit=cover` + `dvh` |
| **`navigator.vibrate` no existe en iOS** | No usar vibración como feedback |
| **Chrome en iPhone (CriOS)** | Se comporta como iOS; `IS_ANDROID` no alcanza |

> Detalle completo en [[Trampas de iOS y WebKit]].

---

## 3. Lecciones de memoria y render

1. **El "editor se ve peor que la pantalla completa"** era un problema de **fuente**: el editor mostraba JPEG 2560 q0.92, la presentación PNG 4032. Solución: **fuente única v11** (ver [[Memoria, render y tiers]] §1).
2. **El benchmark penalizaba a iOS** (`deviceMemory` no existe → tier medium/low) y **degradaba el original de 12 MP a 3200**. Por eso se eliminó: el tier correcto es por `deviceMemory ≤ 4 → low` + fallback por timing.
3. **El LRU de previews no se toca:** cada entrada son PNG data URLs de ~10-25 MB (×2 en memoria). Se bajó de 12 → 6 entradas; *mismo presupuesto total*.
4. **El crash-reporter es observe-only:** `preventDefault`, retry o fetch **prohibidos**. Su trabajo es contar la verdad, no salvar la app.
5. **La congestión del A13 no es culpa de JS:** la sonda de heap dio serie plana → no es GC de JS → es congestión **interna del navegador** (decode/codec/ISP del 12 MP fuera del heap JS). *"El piso del A13 ≈ 18 s/captura, sin arreglo de app."*

> Detalle en [[Memoria, render y tiers]] y [[Trampas de iOS y WebKit]] §4.

---

## 4. Lecciones de lógica de aprobación

1. **"Una bolsa de papas lo pasó con texto ficticio"** → prohibido usar `ocrTextIsValid` a secas. La **señal de contenido** es la que veta, no el OCR crudo. Ver [[Doctrina del score y evaluarGate]] §2.
2. **"El OCR manda sobre el score"**: al subir `PISO_IDENTIFICADA` a 10, toda captura identificada da 10.0 → ÓPTIMA → auto-enviada. **Consecuencia no cerrada:** el estado `ADVERTENCIA` quedó muerto (ver [[Backlog y decisiones pendientes]] D-1).
3. **El recorte no es un bypass:** pasa por el guard de completitud. Por eso es un **rescate** legítimo (ver [[Guard de completitud (borde)]] §5).
4. **`ADVERTENCIA` solo revive en simulación**, gracias a la guarda explícita (`scoreSimulado >= 8`).
5. **El acta identificada es, por definición, una digitalización plena** → 10.0/10. La doctrina llevada a su conclusión lógica.

> Detalle en [[Doctrina del score y evaluarGate]] y [[Estados, intentos y contingencias]].

---

## 5. Lecciones de identificación DIVIPOL

1. **El recorte izquierdo destruye las palabras clave** (`CONSULADO→LADO`, `PAIS→∅`, `LUGAR→R:`) y el OCR lee `MESA→NESA`. Pero el **nombre del puesto** puede quedar intacto.
2. **El clasificador jamás buscaba sin dep/mun resueltos.** Por eso el chip MEDIO mostraba "CLASIFICACIÓN PENDIENTE" aunque "El Cairo - Consulado" se veía perfecto.
3. **La regla 4b** (rescate global por nombre contra los 14.438 puestos, con gap ≥ 0.05 sobre el 2.º) lo resolvió: de `MANUAL` a `SUGERIDA`. Coste: 958 ms en el peor caso; 0 cuando la cascada resuelve.
4. **Nunca se asume el tipo de copia.** Sin tipo leído → máximo `SUGERIDA`. Auto exige la ruta completa + mesa válida + tipo leído.

> Detalle en [[Clasificación DIVIPOL]] §4.

---

## 6. Lecciones de telemetría (metodológicas)

1. **El dueño eliminó las pruebas A/B manuales** por sesgo de percepción: **solo valen los datos**.
2. **Los números de predicción se marcan como tal:** *"no citar como medidos"*.
3. **Las long tasks >50 ms son una cota inferior de la congestión:** es el **punto ciego del observador**.
4. **El "misterio del editor" se resolvió con un experimento natural:** 4,2 s con red a 1,5 Mb/s → 1,3 s con 10 Mb/s. En gama alta el piso era la **descarga del chunk por red**; en el A13 es **CPU de imagen**.
5. **La sonda de estabilidad se ahoga:** mide 130-190 ms/muestra en el A13 (13-24× el objetivo <10 ms) porque **sufre la misma congestión que mide**.

> Detalle en [[Telemetría, diagnóstico y baseline]].

---

## 7. Lecciones de gestión del repo

1. **El lab es la fuente de verdad** (regla de oro #0). El incidente F-LENS lo demostró: al "simplificar", los Android capturaban con la gran angular.
2. **El juez objetivo está fuera del repo** (`analizador-diag-v4.js`, node puro). *"Mismo JSON → mismo veredicto."* Una auditoría no depende de quién la lea.
3. **Nada se mergea sin auditoría de copias.** `SPEC-e14-auditoria-copias`: 13 copias fieles, 3 adaptaciones legítimas, 5 desviaciones.
4. **Protocolo nocturno:** punto atascado >45 min se documenta y se salta; 1 punto = 1 commit; sin force-push ni rebase.

> Detalle en [[Método de trabajo (spec, implementa, audita)]] y [[Reglas de trabajo con agentes]].

---

## 8. Las frases que resumen el proyecto

> *'El rendimiento y la estabilidad se ganan con memoria, CPU de análisis y CSS — NUNCA degradando el dato de imagen.'* (la constitución)

> *'La geometría sospecha, la tinta condena.'* (el guard de completitud)

> *'El OCR manda sobre el score.'* (la doctrina del piso)

> *'La correlación la hace la telemetría, no la percepción del dueño.'* (la metodología)

> *'Un tope mayor rompe iPhone.'* (el 4032)

> *'Lo que ves ES la imagen guardada.'* (WYSIWYG)

> *'Mismo JSON → mismo veredicto.'* (el juez objetivo)

> *'El lab es la fuente de verdad.'* (regla de oro #0)

---

## 9. Reglas prácticas para la próxima vez

1. **Todo fix de UI se prueba primero en el lab y se copia verbatim** con `// Fuente: …`.
2. **Nunca `exact` en constraints** de cámara.
3. **Un track muerto no dispara `onended`:** hay que latir.
4. **En iOS, sin gesto no hay 12 MP.**
5. **Antes de optimizar, medir:** la correlación la hace la telemetría, no la percepción.
6. **Si el fix es "solo CSS" en iOS, sospecha de `will-change`, `touch-action` y alturas `auto`.**
7. **Nada se implementa sin spec; nada se mergea sin auditoría.**
8. **Una nota sin enlaces es una nota muerta** (aplica también al vault).

---

**Ver también:** [[Trampas de iOS y WebKit]] · [[Cámara, autocaptura y burst]] · [[Memoria, render y tiers]] · [[Doctrina del score y evaluarGate]] · [[Clasificación DIVIPOL]] · [[Telemetría, diagnóstico y baseline]] · [[Reglas de trabajo con agentes]]
