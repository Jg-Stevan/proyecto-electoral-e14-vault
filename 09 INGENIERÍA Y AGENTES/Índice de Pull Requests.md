---
proyecto: Proyecto Electoral E-14
tipo: mapa
estado: vigente
actualizado: 2026-10-13
tags: [ingenieria, pull-requests, indice, git]
---

# 🔀 Índice de Pull Requests

> **El mapa de todos los PRs que importan.** Cada uno con su tema, estado y la nota donde vive el detalle.
> Para saber en qué rama está la verdad, abre [[Estado del repositorio web-scanner]].

---

## 1. Convenciones

| Símbolo | Significado |
|---|---|
| ✅ | Mergeado |
| 🟡 | Aprobado, pendiente de merge |
| 🔴 | Abierto, requiere acción |
| ❌ | Cerrado sin merge / revertido |
| ⚠️ | Revertido por otro PR |

---

## 2. PRs mergeados (historial consolidado)

| PR | Tema | Qué resolvió | Nota de origen |
|---|---|---|---|
| **#16** | `contrasteEfectivoDe` (piso 80) + `PISO_IDENTIFICADA = 8.2` + clasificar antes de puntuar | El acta de El Cairo (KIT 399) se identificaba bien pero quedaba a 0,2 del rechazo: **6.7 → 8.5** | [[Doctrina del score y evaluarGate]] §8 |
| **#17** | `PISO_IDENTIFICADA = 10` | *"¿cómo subimos a 10/10?"* — 1 constante, misma tubería | [[Doctrina del score y evaluarGate]] §8 |
| **#38** | Modelo `score = f(OCR + completitud)` | El score por métricas de imagen no distinguía un acta de una bolsa de papas | [[Doctrina del score y evaluarGate]] §8 |

---

## 3. PRs aprobados, pendientes de merge 🟡

| PR | Tema | Qué desbloquea | Detalle |
|---|---|---|---|
| **#39** | Burst (saga v1→v5, `photoGraceMs = 2000`) | El **LOTE 2** (preview escalonado 2048→4032) | [[Cámara, autocaptura y burst]] §7 |
| **#43** | F2 (filtros: `processImage` sin re-OCR) | El **LOTE 2** | [[El puente - scanner-core-bridge]] §5 |

> **Acción:** mergear #39 y #43 → desbloquea el LOTE 2 (ver [[Backlog y decisiones pendientes]] B-2).

---

## 4. PRs abiertos, requieren acción 🔴

| PR | Tema | Estado | Acción |
|---|---|---|---|
| **#47** | Revert del guard-borde | 🔴 Abierto — **riesgo mayor** | **Cerrar (30 s)** + limpiar rama `revert-44` |

> Ver [[Backlog y decisiones pendientes]] B-1.

---

## 5. PRs revertidos ⚠️

| PR | Tema | Qué pasó | Nota |
|---|---|---|---|
| **#44** | Guard de borde (original) | Revertido por #47 | El guard sigue vigente en el modelo (ver [[Guard de completitud (borde)]]) |

---

## 6. La saga del burst (referencia rápida)

El burst pasó por 5 versiones; cada una costó un PR y una auditoría:

| Versión | Cambio clave |
|---|---|
| **v1** | Muro de tiempo sin límite global (A13: 16,8-17,2 s) |
| **v2-v3** | Muro de 2000 ms determinista + `performance.now()` monotónico |
| **v4** | Sampler 64×64 de luma, cadencia 750 ms, umbral 2.5 |
| **v5** | `photoGraceMs = 2000` (clamp 0-5000) + sonda de heap |

> Detalle y el hallazgo forense central en [[Cámara, autocaptura y burst]] §7.

---

## 7. Reglas para abrir un PR nuevo

1. **Especifica primero** (ver [[Método de trabajo (spec, implementa, audita)]])
2. **Copia del lab** con `// Fuente: …` si tocas UI (regla de oro #0)
3. **No renombres telemetría** (los E2E dependen de los nombres)
4. **Documenta el cambio** en la nota correspondiente del vault
5. **Añade el PR aquí** con su símbolo de estado

---

## 8. Mapa de dependencias (qué desbloquea qué)

```
   #47 (cerrar)  ──────────────────────────> limpia revert-44
        │
        ▼
   #39 (merge)  ┐
                 ├──> LOTE 2 (preview escalonado)  ──> Plan A (rebase)
   #43 (merge)  ┘
        │
        ▼
   Rediseño Stitch (rebase sobre main + auditoría E2E)
```

> El orden de integración (`v12b → rebase v13 → re-auditoría`) está sin decidir (ver [[Backlog y decisiones pendientes]] B-3).

---

**Ver también:** [[Estado del repositorio web-scanner]] · [[Backlog y decisiones pendientes]] · [[Método de trabajo (spec, implementa, audita)]] · [[Reglas de trabajo con agentes]]
