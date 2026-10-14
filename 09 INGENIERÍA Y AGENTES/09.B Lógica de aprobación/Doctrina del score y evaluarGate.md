---
proyecto: Proyecto Electoral E-14
tipo: ingenieria
estado: vigente
actualizado: 2026-10-13
tags: [ingenieria, e14, score, gate]
---

# Doctrina del score y evaluarGate

> **Esto resuelve la contradicción central del vault** ("¿3 bandas o binario?"). La respuesta real es una **precedencia de tres pasos** con umbrales concretos, y está viva en el código.

## 1. La precedencia (el corazón del modelo)

```
1. COMPLETITUD  (veto)   → ¿el acta está entera en el encuadre?
2. SEÑAL        (techo)  → ¿hay contenido real de acta?
3. SCORE        (gradúa) → ¿qué calidad tiene la imagen?
```

**Orden obligatorio:** la completitud veta, la señal limita el techo, y solo entonces el score graduúa. Cambiar el orden cambia el desenlace.

## 2. Paso 1 — Completitud (veto)

`esActaCompleta(quad)` → si el acta está cortada: rechazo con motivo **`INCOMPLETA`** y **techo 6.4**. Al agotar intentos → contingencia.
Detalle completo en [[Guard de completitud (borde)]].

## 3. Paso 2 — Señal de contenido (techo 4.9)

```ts
senalActa = PATRON_CABECERA
         || parciales.cabecera
         || parciales.mesa != null
         || parciales.tipo != null
```

**Sin señal → techo 4.9** + rechazo **`BAJO`** con el mensaje:
> *"SCORE BAJO — NO SE RECONOCE INFORMACIÓN DEL ACTA — REPITA LA FOTO"*

**Prohibido usar `ocrTextIsValid` a secas:** en palabras de la spec, **"una bolsa de papas lo pasó con texto ficticio"**.

## 4. Paso 3 — El score (fórmula real)

```
bruto = (sharpness × 0.4 + brightness × 0.3 + contrasteEfectivo × 0.3) / 10
```

- Redondeo a **1 decimal**.
- **Level recalculado** con `scoreCore = sharpness × 0.45 + brightness × 0.25 + contEff × 0.3`, con cortes **80 / 62 / 45**.
- **Techos por level:** `fair → 7.9` · `poor → 6.4`. (Techos "honestos": existen para que un piso no pueda mentir.)

## 5. Los dos pisos (y por qué existen)

| Piso | Valor | Doctrina |
|---|---|---|
| **`PISO_CONTRASTE_LEGIBLE`** | **80** | Una **foto de documento tiene contraste global bajo por diseño** (mucho papel blanco). Si el OCR es válido, el contraste del crudo **no puede castigarla** |
| **`PISO_IDENTIFICADA`** | **10** | Si el acta se **identificó** (`AUTO`), el score deja de medir la foto y mide **la certeza de la digitalización** → 10.0/10 |

**Ambos con `Math.max()` puro:** *un piso nunca reduce*. Y **SUGERIDA / MANUAL no reciben piso** — su camino es el panel de corrección.

> **La doctrina en una frase:** *"el OCR manda sobre el score"*. Se llevó a su conclusión lógica: un acta identificada es, por definición, una digitalización plena.

## 6. Las bandas de estado (statusDeScore)

| Score | Estado |
|---|---|
| **≥ 8.0** | 🟢 `OPTIMA` |
| **6.5 - 7.9** | 🟡 `ADVERTENCIA` |
| **< 6.5** | 🔴 `RECHAZADA` |

Y los estados de ciclo de vida: `ENVIADA`, `EN_REVISION_HUMANA` (terminal, ámbar).

**Así se reconcilia el vault:** no es "binario" ni "3 bandas" genérico —
**es precedencia (completitud → señal → score) + bandas + pisos.**

## 7. ⚠️ La consecuencia que nadie cerró: ADVERTENCIA quedó muerto

Al subir `PISO_IDENTIFICADA` a 10, **toda captura identificada da 10.0** → `OPTIMA` → **auto-enviada**.

⇒ El estado **`ADVERTENCIA` se volvió funcionalmente inalcanzable** por captura real. El mock de ADVERTENCIA quedó como ruta muerta.

**Solo revive en simulación**, gracias a una guarda explícita: *el piso AUTO solo se re-aplica si `scoreSimulado >= 8`* (si no, mataría la banda media).

**Decisión pendiente:** aceptar el código defensivo o retunear el piso. Ver [[Backlog y decisiones pendientes]].

## 8. Historial de por qué se llegó aquí

| PR | Cambio | Motivo |
|---|---|---|
| #16 | `contrasteEfectivoDe` (piso 80) + `PISO_IDENTIFICADA = 8.2` + clasificar ANTES de puntuar | El acta del dueño (El Cairo, KIT 399) se identificaba bien y aun así quedaba a 0,2 del rechazo: **6.7 → 8.5** |
| #17 | `PISO_IDENTIFICADA = 10` | *"¿cómo subimos a 10/10?"* — 1 constante, misma tubería |
| #38 | Modelo `score = f(OCR + completitud)` | El score por métricas de imagen no distinguía un acta de una bolsa de papas |

**Mapa de consumidores verificado antes del cambio:** `statusDeScore` (canónico), `ReviewView` (≥8 → ÓPTIMA ✅), `ResumenView`/`ActasView` (0 usos), y **el PDF no renderiza el score**. ⇒ Cambiar el número no tenía efectos colaterales.

---

**Ver también:** [[Guard de completitud (borde)]] · [[Clasificación DIVIPOL]] · [[Estados, intentos y contingencias]] · [[Flujo de Validación y Aprobación Operativa]] · [[Especificación de Requerimientos (ERS)]]
