---
proyecto: Proyecto Electoral E-14
tipo: ingenieria
estado: vigente
actualizado: 2026-10-13
tags: [ingenieria, e14, guard, completitud, borde]
---

# 🛡️ Guard de completitud (borde)

> **"La geometría sospecha, la tinta condena."**
> Este guard es el **paso 1 del pipeline de aprobación** (el veto). Decide si el acta está entera en el encuadre *antes* de medirle la calidad a la imagen.

---

## 1. Dónde vive

```
apps/digitalizador-e14/src/lib/e14/completitud.ts
```

Función canónica: **`esActaCompleta(quad)`** — recibe el cuadrilátero detectado por el motor de bordes y responde `true` / `false` con motivo.

Forma parte del **bridge real** ([[El puente - scanner-core-bridge]]) y **corre en todas las rutas**: cámara, archivo importado y recorte manual.

---

## 2. La precedencia (por qué es el paso 1)

```
1. COMPLETITUD  (veto)   → ¿el acta está entera en el encuadre?  ← ESTA NOTA
2. SEÑAL        (techo)  → ¿hay contenido real de acta?
3. SCORE        (gradúa) → ¿qué calidad tiene la imagen?
```

**Orden obligatorio** (ver [[Doctrina del score y evaluarGate]]): la completitud *veta*, la señal *limita el techo*, el score *gradúa*. Cambiar el orden cambia el desenlace.

---

## 3. Qué pasa cuando falla

| Condición | Resultado |
|---|---|
| Acta **cortada** (no entera en el encuadre) | Rechazo con motivo **`INCOMPLETA`** |
| Techo impuesto al score | **6.4** (no puede llegar a ÓPTIMA aunque la imagen sea nítida) |
| Al agotar intentos | Contingencia automática → `EN_REVISION_HUMANA` |

> El techo de 6.4 es un **techo honesto**: existe para que un acta cortada no pueda mentir su camino hasta ÓPTIMA con un buen score de nitidez.

---

## 4. La doctrina: "la geometría sospecha, la tinta condena"

Esta frase resume la **filosofía del guard**:

- **La geometría sospecha** → `esActaCompleta(quad)` detecta el recorte geométrico. Es una *sospecha*: si el borde está mal, hay que mirar el contenido.
- **La tinta condena** → solo la **señal de contenido** (paso 2) y el **score** (paso 3) pueden *condenar* (rechazo definitivo) o *absolver* (envío).

**El guard no condena solo**: impone un techo (6.4) y deja que la señal y el score terminen de decidir. Un acta cortada pero legible puede pasar a `ADVERTENCIA` (6.5-7.9) o rescate, **nunca** a `ÓPTIMA`.

---

## 5. El recorte manual también pasa por aquí

**Regla dura:** el recorte manual del operador **no es un bypass**. Al ajustar los vértices del encuadre, la nueva geometría se reenvía a `esActaCompleta(quad)` y al pipeline completo (ver [[Estados, intentos y contingencias]]).

> Eso es lo que hace del recorte un **mecanismo de rescate** legítimo: si el operador recorta bien, el guard **aprueba** y el acta puede escapar de la contingencia (incluso pasar a ÓPTIMA si la clasificación es AUTO y el score ≥ 8).

---

## 6. El caso abierto: §9.12 — "papel completo sin votos"

**El problema:** una hoja que contiene **solo DIVIPOL + nivelación** (cabecera y cuadre de mesa, legible al 100%, pero **sin la matriz de votos**) **pasa el guard** de completitud (está entera) y podría salir **ÓPTIMA**.

**Por qué importa:** un acta sin votos no es un acta válida para escrutinio, pero el guard geométrico no lo distingue de un acta completa real.

**Diseño preliminar (con advertencia de calibración crítica):**
- La evidencia mínima para **no** rechazar debe ser **un marcador de sección** (la cabecera de la matriz de votación).
- Si se pide menos, el umbral mataría el score **7.0** del caso MEDIO de simulación (que sí tiene votos).
- **Decisión pendiente** → ver [[Backlog y decisiones pendientes]].

---

## 7. La regla de oro #0 (de dónde viene la honestidad)

El guard de completitud es **una de las consecuencias** de la constitución del proyecto (ver [[Arquitectura del motor (scanner-core)]):

> *"El rendimiento y la estabilidad se ganan con memoria, CPU de análisis y CSS — NUNCA degradando el dato de imagen."*

El guard no degrada la imagen: **la mide**. Si está cortada, la *retiene* (techo 6.4) en vez de *recortarla* para salvarla. Es la geometría la que se ajusta al acta, no al revés.

---

**Ver también:** [[Doctrina del score y evaluarGate]] · [[Estados, intentos y contingencias]] · [[El puente - scanner-core-bridge]] · [[Clasificación DIVIPOL]] · [[Modelo del Acta E-14]]
