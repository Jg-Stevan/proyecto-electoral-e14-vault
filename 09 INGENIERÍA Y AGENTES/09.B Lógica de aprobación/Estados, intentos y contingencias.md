---
proyecto: Proyecto Electoral E-14
tipo: ingenieria
estado: vigente
actualizado: 2026-10-13
tags: [ingenieria, e14, estados, contingencias]
---

# Estados, intentos y contingencias

> Qué puede pasarle a un acta desde que se fotografía hasta que se envía, y por qué el sistema **insiste en repetir la foto** antes de rendirse.

## 1. Los estados

| Estado | Significado |
|---|---|
| 🟢 **`OPTIMA`** | Calidad plena → envío automático |
| 🟡 **`ADVERTENCIA`** | Legible pero con observación → requiere confirmación |
| 🔴 **`RECHAZADA`** | No apta → **repetir obligatorio** |
| ⚪ **`ENVIADA`** | Transmitida |
| 🟠 **`EN_REVISION_HUMANA`** | Estado terminal de contingencia |

**Umbrales (`statusDeScore`):** `≥8.0 OPTIMA` · `6.5-7.9 ADVERTENCIA` · `<6.5 RECHAZADA`.

## 2. La política de intentos (el cambio de filosofía)

**Antes:** capturas con score bajo iban **directo a revisión humana**. **Ahora: repetir primero, contingencia al final.**

| Constante | Valor | Comentario |
|---|---|---|
| `maxIntentos` | **4** (antes 2, hardcodeado) | "Más de 3 veces" del dueño = 4 capturas / 3 repeticiones |
| `UMBRAL_REVISION_HUMANA` | **3** | El botón **REVISIÓN HUMANA con candado** *aparece* (no se muestra deshabilitado) cuando `intento ≥ 3` |
| Contingencia | Automática al **intento 4** | `EN_REVISION_HUMANA` + fila en historial + toast **"ENVIADA A CONTINGENCIA"** + badge en ACTAS |

**Se eliminó el CTA manual** de "enviar a revisión humana" (`enviarRevisionHumana` → 0 usos, higiene). Solo sobrevive la acción aditiva `escalarRevisionHumana()` como excepción declarada a "0 cambios de lógica".

## 3. ⭐ El recorte como mecanismo de rescate

| Acción | Qué hace | ¿Consume intento? |
|---|---|---|
| **RECORTAR** | **Re-ejecuta el pipeline completo**: imagen + OCR + clasificación + gate | **NO** |
| ROTAR 90° | Local: "hornea" la rotación | NO |
| PANTALLA COMPLETA | Solo visor | NO |

**Toolbar por estado:**
- **3 botones** (Recortar · Rotar 90° · Pantalla completa) en **todos los estados de corrección** (ADVERTENCIA, RECHAZADA y ambas fases de EN_REVISION_HUMANA).
- **5 botones** (Recortar · Rotar · **Filtros** · **Texto** · Pantalla) **solo en OPTIMA-rescate**.

**Por qué el recorte es la clave:** es la **única** acción que re-evalúa todo. Si un recorte manual produce un acta identificada (`AUTO` + score ≥8), la pantalla pasa a `OPTIMA` y **permite escapar de la contingencia**. Si no lo logra, permanece en la misma fase con el **intento intacto**.

**Y no es un bypass:** el recorte manual **también pasa por el guard de completitud**.

## 4. La pantalla partida de `EN_REVISION_HUMANA`

El mismo estado se comporta distinto según el intento:

| Fase | Qué muestra |
|---|---|
| `intento < max` | Banner **REPETIR** + **sin** panel de corrección |
| `intento >= max` | **Contingencia**: con **panel de corrección** (cascada con sugerencia precargada) |

## 5. La simulación: 3 chips, 3 desenlaces

Los chips sobre el obturador **son botones de simulación** con mapeo fijo a las fotos reales del dueño:

| Chip | Asset | Desenlace |
|---|---|---|
| **ÓPTIMA [8.2]** | `acta-e14-real.jpg` (pipeline completo) | **10.0 OPTIMA** auto-enviada |
| **MEDIO [7.0]** | `acta-sim-intermedia.jpg` (b/n **as-is**, `sinRecorte`, `QUAD_IDENTITY`, filtro `original`) | **7.0 ADVERTENCIA** + clasificación AUTO real + banner |
| **BAJA [4.3]** | `acta-sim-baja.jpg` (borrosa original as-is) | **RECHAZADA** |

**Guardas críticas de la simulación:**
1. La completitud **se omite solo** con `simular` (las capturas reales jamás).
2. **El piso AUTO solo se re-aplica si `scoreSimulado >= 8`** → así el chip MEDIO **no se convierte en un segundo ÓPTIMA**.
3. Arranque por defecto en **CÁMARA** (no en simulación).

## 6. `evaluarGate` con historial

```ts
evaluarGate(score, ocrTexto, clasificacion, intento, maxIntentos)
```

- `score ≥ 6.5` + SUGERIDA/MANUAL → **EN_REVISION_HUMANA** (repetir primero)
- `score < 6.5` → **RECHAZADA** (solo repetir)
- Al agotar intentos → **contingencia automática**
- Desviación **aprobada y documentada:** al re-importar sin parámetro, los intentos **continúan** (previa no resuelta → `previo.intento + 1`; ciclo nuevo → 1). **Necesario para que los AC de repetir sean alcanzables.**

## 7. Las notificaciones (tema abierto)

El rediseño final (Stitch) **elimina el sistema de notificaciones flotantes** por orden del dueño. Hay que garantizar **reemplazos persistentes** para cada aviso, sin perder ninguno:

| Aviso | Reemplazo previsto |
|---|---|
| Acta enviada | Píldora/banner de estado |
| Envío automático | Nube de transmisión |
| Acta rechazada | Franja/banner en el visor |
| Enviada a contingencia | Fila en historial |
| Filtro aplicado | Feedback en el botón |
| Texto copiado | Feedback en el botón |
| Actas incompletas | Aviso persistente |

---

**Ver también:** [[Doctrina del score y evaluarGate]] · [[Guard de completitud (borde)]] · [[Interfaz Gráfica (Digitalizador)]] · [[Estados del Acta E-14 en la PWA]]
