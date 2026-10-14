---
proyecto: Proyecto Electoral E-14
tipo: guia
estado: vigente
actualizado: 2026-10-13
tags: [revision, calidad, decisiones]
---

# Contradicciones detectadas

> Resultado de leer las 19 notas del vault (2026-10-13) y de **contrastarlas con la implementación real**.
> ✅ **Las 3 contradicciones que bloqueaban el desarrollo están RESUELTAS**: la verdad está en el código del [repositorio](https://github.com/Jg-Stevan/web-scanner), no en las especificaciones.

---

## ✅ RESUELTAS por la implementación real

### 1. ✅ Criterio de calidad: **ni binario ni "3 bandas" genérico**

**Lo que decía el vault (en conflicto):**

| Fuente | Lo que decía |
|---|---|
| [[Flujo de Validación y Aprobación Operativa]] | `≥9` automático · `6-8` advertencia · `≤5` rechazado |
| [[Estados del Acta E-14 en la PWA]] | Las mismas 3 bandas |
| [[Especificación de Requerimientos (ERS)]] RN-02 | **Binario**: `≥9` pasa · `≤8` no pasa |

**La verdad implementada** (→ [[Doctrina del score y evaluarGate]]):

```
1. COMPLETITUD (veto)    → acta cortada: INCOMPLETA + techo 6.4
2. SEÑAL (techo)         → sin contenido de acta: techo 4.9
3. SCORE (gradúa)        → (sharpness×0.4 + brightness×0.3 + contEff×0.3)/10
   Techos por level: fair 7.9 · poor 6.4
   Pisos: contraste legible 80 · identificada 10  (Math.max puro)
Bandas: ≥8.0 OPTIMA · 6.5-7.9 ADVERTENCIA · <6.5 RECHAZADA
```

⇒ **El ERS RN-02 (binario) estaba desactualizado.** El modelo vigente es **precedencia + bandas + pisos**.

### 2. ✅ Hardware: **tiers adaptativos**, no un rango fijo

| Fuente                                            | Decía         |
| ------------------------------------------------- | ------------- |
| [[Perfil operativo y arquitectura del sistema]]   | 2-4 GB de RAM |
| [[Especificación de Requerimientos (ERS)]] RNF-02 | 4-8 GB de RAM |

**La verdad implementada**: **tiers medidos con datos reales** (→ [[Memoria, render y tiers]]):

| Dispositivo | Tier | Resolución |
|---|---|---|
| Galaxy A13 (4 GB) | `low` (`deviceMemory ≤ 4`) | **3200 px** (piso, *solo como downscale, jamás recorte*) |
| Galaxy A56 (8 GB) | `high` | **4032 px** |
| iPhone | sin `deviceMemory` | 4032 (fuente única desde v11) |

⇒ **Ninguno de los dos rangos era correcto:** el diseño es **adaptativo**, con el **tope único de 4032** como techo no negociable.

### 3. ✅ Escenario: **Presidenciales 2026, segunda vuelta, exterior**

| Fuente | Decía |
|---|---|
| `ELECCIONES CONGRESO 2026` (en cuarentena) | 52 páginas por ejemplar → 104 por mesa |
| `Volumen de páginas (Presidenciales 2026)` | 2-3 páginas por ejemplar |

**La verdad implementada** (→ [[Modelo del Acta E-14]]): el proyecto es **PRESIDENTE Y VICEPRESIDENTE 2026 — SEGUNDA VUELTA**, ámbito **exterior** (departamento **88 = CONSULADOS**), con **2 copias × 2 páginas**.

⇒ **La cuarentena de `ELECCIONES CONGRESO 2026.md` fue la decisión correcta:** es otro escenario (factor ~15x en volumen). Puedes eliminarlo con tranquilidad.

---

## ⚠️ ABIERTAS (de producto)

### 4. `ADVERTENCIA` es un estado muerto
Con `PISO_IDENTIFICADA = 10`, toda captura identificada da **10.0 → OPTIMA auto-enviada**. La banda media solo existe en **simulación**.
**Decisión pendiente:** aceptar el código defensivo o retunear el piso. → [[Backlog y decisiones pendientes]]

### 5. §9.12 — "papel completo sin votos"
Una hoja de **DIVIPOL + nivelación** (legible al 100%, sin matriz de votos) **pasa el guard** y podría salir ÓPTIMA.
**Diseño preliminar listo**, con una advertencia de calibración crítica (la evidencia mínima debe ser **un marcador de sección**, o mataría el 7.0). → [[Guard de completitud (borde)]]

### 6. Flujo de 2 páginas sin implementar
El modelo es **1 acta = 1 captura**, pero el acta real dice "**Pag. 1 de 2**".

### 7. El ERS se declaró "congelado" con inconsistencias
[[Especificación de Requerimientos (ERS)]] termina con *"queda congelado y aprobado para el inicio del Sprint 1"*, pero su RN-02 contradice el modelo implementado (ver punto 1) y su RNF-02 el de tiers (punto 2).

✅ **Recomendación clara: emitir la v2.0 del ERS** con el modelo real de decisión. La nota [[Doctrina del score y evaluarGate]] tiene todo lo necesario.

### 8. Enlace roto a un PDF inexistente
`EL CAIRO D - DOMINGO.pdf` (citado en [[Estructura y Significado del Código de Barras]]) **no está en el vault**; solo existen las actas de ejemplo de El Cairo. **Convertido a texto** para que no aparezca como enlace roto. → Pendiente: subir el archivo o enlazar otro.

---

## 🆕 NUEVAS: contradicciones entre especificaciones

Al reconstruir el historial aparecieron conflictos **internos del proyecto** (documentados en las bitácoras, ya resueltos por la implementación o flaggeados):

| Tema | Conflicto | Quién ganó |
|---|---|---|
| **`ISO: 1600`** | `SPEC-cyber-neon-captura` lo fijaba como HUD canónico; `SPEC-camara-funcional` ordenaba **eliminarlo** | Se eliminó |
| **Botón "Auto"** | `SPEC-camara-funcional` lo usaba; `SPEC-score-ocr` Rev. 4 lo **elimina de la fila** (sobrevive vía HUD); `SPEC-guard-borde` D9 **revoca el default OFF** | Auto fuera de la fila + **armado por defecto** |
| **"CLASIFICACIÓN PENDIENTE"** | `SPEC-score-ocr` D3 lo **prohíbe**; `SPEC-chrome-3-estados` y el mock lo **mantienen** | Prohibido en RECHAZADA; el conflicto D2/D3 vs AC4 se resolvió **a favor del AC4** |
| **Scores del mock** | Stitch dibuja 9.8/7.8/4.2 y "INTENTO 1 DE 3"; la lógica real produce 10.0/7.0/4.3 con **4 intentos** | **Manda la lógica** (flaggeado, no bloqueante) |
| **Tres sistemas visuales** | Precision Monitor → cyber-neón → Stitch; el último **prohíbe** el anterior (`grep = 0`) | Stitch es el único vigente; hay que re-vestir lo no cubierto |

---

## 🟢 Cerradas sin acción

| Punto | Estado |
|---|---|
| **Duplicado exacto del design system** | Se conservó una copia canónica en `05 DISEÑO UI`; el duplicado está en cuarentena |
| **Datos de contexto de prensa/redes** | Antes de dimensionar, validar contra la Registraduría y el CNE |
| **Residuo conversacional de IA** | Eliminado de [[Panel de Control y Supervisión Global]] |
| **Nombres de carpetas de diseño** | Provienen de exportaciones automáticas; se conservaron por trazabilidad |

---

*Última actualización: 2026-10-13, tras contrastar el vault con el repositorio real.*
