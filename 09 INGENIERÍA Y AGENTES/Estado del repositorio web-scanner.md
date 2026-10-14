---
proyecto: Proyecto Electoral E-14
tipo: ingenieria
estado: vigente
actualizado: 2026-10-13
tags: [ingenieria, repositorio, estado, git]
---

# 📊 Estado del repositorio web-scanner

> **Dónde está cada cosa, qué se desplegó y en qué rama vive la verdad.**
> Abre esta nota antes de tocar el repo para no pisar trabajo en curso.

---

## 1. El repositorio

| Dato | Valor |
|---|---|
| **Repo** | [github.com/Jg-Stevan/web-scanner](https://github.com/Jg-Stevan/web-scanner) |
| **Estructura** | Monorepo: `packages/scanner-core` + `apps/scanner-lab` + `apps/digitalizador-e14` |
| **App desplegada** | [jg-stevan.github.io/web-scanner](https://jg-stevan.github.io/web-scanner/) (app E-14) |
| **Laboratorio** | [jg-stevan.github.io/web-scanner/lab/](https://jg-stevan.github.io/web-scanner/lab/) |
| **Regla suprema** | El lab es la fuente de verdad (regla de oro #0, ver [[Arquitectura del motor (scanner-core)]]) |

---

## 2. Ramas relevantes

| Rama | Qué contiene | Estado |
|---|---|---|
| `main` | La base estable | ✅ Vigente (con rediseño Stitch por integrar) |
| `qa-v11` / `qa-v12` / `qa-v12b` | Versiones de QA con URLs estables de Vercel | 🟡 Por recrear |
| `revert-44` | El revert del guard-borde (PR #47) | 🔴 **Por cerrar/limpiar** |
| `v13-post-N` (35 commits) | Fuera de alcance | 🟡 QA físico o descartar |

> ⚠️ Las ramas `qa-*` se quieren recrear para tener URLs estables de Vercel y comparar versiones en el iPhone real (ver [[Backlog y decisiones pendientes]] DEV-6).

---

## 3. Pull Requests (mapa de alto nivel)

| PR | Tema | Estado |
|---|---|---|
| **#16** | `contrasteEfectivoDe` (piso 80) + `PISO_IDENTIFICADA = 8.2` + clasificar antes de puntuar | ✅ Mergeado |
| **#17** | `PISO_IDENTIFICADA = 10` | ✅ Mergeado |
| **#38** | Modelo `score = f(OCR + completitud)` | ✅ Mergeado |
| **#39** | Burst | 🟡 **Aprobado, pendiente de merge** |
| **#43** | F2 (filtros) | 🟡 **Aprobado, pendiente de merge** |
| **#44** | Guard de borde (original) | ⚠️ Revertido por #47 |
| **#47** | Revert del guard-borde | 🔴 **Abierto — cerrar ya (30 s)** |

> Detalle y trazabilidad en [[Índice de Pull Requests]].

---

## 4. El orden de integración pendiente

```
v12b  →  rebase v13  →  re-auditoría
```

- **Decisión abierta:** definir la secuencia de rebases (ver [[Backlog y decisiones pendientes]] B-3).
- **Plan A:** rebase tras el burst (viva).
- **Plan B:** condicionada (WebCodecs/WebGPU).

Ambos planes comparten la sonda `__perfProbe` para comparabilidad 1:1 (ver [[Memoria, render y tiers]] §5 y [[Telemetría, diagnóstico y baseline]] §2).

---

## 5. Lo desplegado y funcional (alto nivel)

| Subsistema | Estado |
|---|---|
| Motor de captura (gama alta) | ✅ A56 y iPhone funcionando |
| Motor de captura (gama baja, A13) | 🟡 Piso aceptado ≈18 s/captura (congestión interna del navegador, sin arreglo de app) |
| Lógica de aprobación | ✅ Precedencia + bandas + pisos consolidada |
| Clasificación DIVIPOL | ✅ Con rescate global (regla 4b) |
| Guard de completitud | ✅ Vigente (pero #47 quiere revertirlo — **cerrar**) |
| Rediseño Stitch (visual) | 🟡 Pendiente de rebase sobre `main` con auditoría E2E |
| LOTE 2 (preview escalonado) | 🟡 Spec entregada, esperando merge de #39 y #43 |
| Export PDF del e14 | ❌ Eliminado por decisión del dueño (el lab lo conserva) |
| Notificaciones flotantes | ❌ Eliminadas ("cero mensajes"); reemplazos pendientes |

---

## 6. La baseline de rendimiento (medición 2026-10-12)

| Dispositivo | RAM / heap | CPU bench | Encode JPEG 3200 | Refresh |
|---|---|---|---|---|
| **Galaxy A56** | 8 GB / 1965 MB | 21-25 ms | 371-428 ms | 60 Hz |
| **Galaxy A13** (caso crítico) | 4 GB / 961 MB | 82 ms (~3,5× más lento) | 730 ms | 43 Hz |
| PC (Windows) | 8 GB / 4128 MB | 60 ms | 209 ms | 60 Hz |
| **iPhone** (iOS 26.6 Safari) | — | 7 ms | — | — 🥇 el más rápido |

> Detalle y los 3 hallazgos que orientaron el trabajo en [[Telemetría, diagnóstico y baseline]] §5.

---

## 7. Qué hacer antes de tocar el repo

1. Lee esta nota y [[Backlog y decisiones pendientes]].
2. Verifica que tu cambio **no duplique** trabajo aprobado sin mergeear (#39, #43).
3. Si tocas UI, **copia literal del lab** con `// Fuente: …` (regla de oro #0).
4. Si tocas telemetría, **no renombres ni borres marks** (los E2E dependen de ellos).
5. Documenta la decisión en el diario (`07 DIARIO`) y enlázala aquí.

---

**Ver también:** [[Índice de Pull Requests]] · [[Backlog y decisiones pendientes]] · [[Arquitectura del motor (scanner-core)]] · [[Inicio - Ingeniería y Agentes]]
