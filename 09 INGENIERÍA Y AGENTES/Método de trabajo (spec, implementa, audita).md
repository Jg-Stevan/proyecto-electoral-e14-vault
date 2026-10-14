---
proyecto: Proyecto Electoral E-14
tipo: guia
estado: vigente
actualizado: 2026-10-13
tags: [ingenieria, metodo, trabajo, guia]
---

# 🧭 Método de trabajo (spec, implementa, audita)

> **El flujo obligatorio para tocar el repo.** Nada se implementa sin spec; nada se mergea sin auditoría.
> *"La correlación la hace la telemetría, no la percepción del dueño."*

---

## 1. El ciclo (3 fases, no negociable)

```
  ┌─────────────────────────────────────────────────────────┐
  │                                                         │
  │   1. SPEC          2. IMPLEMENTA          3. AUDITA     │
  │   ─────────        ──────────────         ──────────     │
  │   Especifica       Copia del lab          Auditoría E2E │
  │   antes de         (regla #0)             con telemetría│
  │   tocar código                                          │
  │                                                         │
  └─────────────────────────────────────────────────────────┘
```

| Fase | Qué se hace | Qué se produce |
|---|---|---|
| **1. SPEC** | Escribes qué vas a cambiar y por qué, **antes de programar** | Una nota `SPEC-*` con el problema, las opciones y la decisión |
| **2. IMPLEMENTA** | Copias del lab si tocas UI (`// Fuente: …`); escribes código | El PR con los cambios |
| **3. AUDITA** | Corres los E2E, lees la telemetría, comparas con la baseline | El veredicto del `analizador-diag-v4.js` |

> **Regla:** nunca se salta una fase. Si te saltas la spec, vuelves a empezar. Si te saltas la auditoría, no se mergea.

---

## 2. Fase 1 — SPEC (la especificación)

**Antes de escribir una línea de código**, abres (o creas) una nota `SPEC-*` con:

1. **El problema** (¿qué falla o qué falta?)
2. **Las opciones** evaluadas (con trade-offs honestos)
3. **La decisión** (qué vas a hacer y por qué)
4. **Los invariantes** (qué no se puede romper)
5. **La telemetría** que vas a mirar para confirmar que funcionó

> **Inspiración:** las SPEC del proyecto (`SPEC-cyber-neon-captura`, `SPEC-camara-funcional`, `SPEC-score-ocr`, `SPEC-guard-borde`, `SPEC-chrome-3-estados`, `SPEC-e14-auditoria-copias`) viven como evidencia histórica en el repo.

**Ejemplo de invariante (del LOTE 2):**
> *Con `maxLongSide = 4032` el comportamiento debe ser **byte a byte el de hoy**; el guardado (`store.ts` L303/462/587/713) queda **intocable**.* (ver [[Memoria, render y tiers]] §6)

---

## 3. Fase 2 — IMPLEMENTA (el código)

| Si tocas… | La regla |
|---|---|
| **UI** | Copia literal del lab con `// Fuente: apps/scanner-lab/...` (regla de oro #0, ver [[Arquitectura del motor (scanner-core)]]) |
| **Telemetría** | No renombres ni borres marks; los E2E dependen de los nombres (ver [[Telemetría, diagnóstico y baseline]]) |
| **El motor `scanner-core`** | Prohibido importar `react`, `next/*`, `@/components`. Contrato congelado vía subpaths |
| **El e14** | Recibe del lab **copiando** subsistemas probados, nunca reinventando |
| **El score** | Respeta la precedencia: completitud → señal → score (ver [[Doctrina del score y evaluarGate]]) |
| **La imagen** | Nunca degradar el dato (la constitución del proyecto) |

> El incidente que originó la regla de oro #0: al "simplificar" el arranque de cámara del lab, los Android capturaban con la gran angular (bug F-LENS en producción).

---

## 4. Fase 3 — AUDITA (la verificación)

**Tres niveles de auditoría:**

### 4.1 · Auditoría de copias (SPEC-e14-auditoria-copias)
Cuando se copia del lab al e14, se audita copia por copia. Hallazgos típicos:
- **Copias fieles** (literal con `// Fuente`)
- **Adaptaciones legítimas** (declaradas y justificadas)
- **Desviaciones** (⚠️ hay que justificar o revertir)

### 4.2 · Auditoría E2E (automatizada)
Los E2E dependen de los **nombres fijos de los marks** de telemetría. Si un test falla, es porque un comportamiento cambió.

### 4.3 · Auditoría con el juez objetivo
El **juez está fuera del repo**: `analizador-diag-v4.js` (node puro, umbrales fijos y públicos).

| Umbral | Valor |
|---|---|
| `TOL_ARITMETICA_MS` | 250 |
| `DATAURL_DOMINANTE` | 0.50 |
| `TRABAJO_REAL_BAJO` | 0.15 |
| `CONGESTION_ALTA` | 0.60 |
| `JOBS_FACTOR_CAP1` | 0.50 |
| `MURO_2000_RANGO` | 1500-2600 |

> *"Mismo JSON → mismo veredicto."* Una auditoría no depende de quién la lea. Ver [[Telemetría, diagnóstico y baseline]] §3.

---

## 5. El protocolo de crash-test (cuando se prueba algo arriesgado)

| Escenario | Qué hacer |
|---|---|
| **A** | 1 página con zoom |
| **B** | 3 páginas |
| **C** | 6-8 páginas |
| **D** | Export doble |

**Señales a distinguir:**
- **Banner rojo** = error de JS (lo captura el crash-reporter).
- **Pantalla blanca o recarga** = **OOM nativo**, sin banner.
- **Congelación >30 s** = fallo.

> En gama baja usar el **obturador MANUAL** (la auto-captura podía no disparar; lo arregla v9). Ver [[Telemetría, diagnóstico y baseline]] §4.

---

## 6. Protocolo nocturno (v13)

*"Punto atascado >45 min se documenta y se salta; 1 punto = 1 commit; sin force-push ni rebase."*

- Te atascaste >45 min → **documenta y salta** (no te quemes).
- 1 punto = 1 commit (atomismo: cada cambio es rastreable).
- **Sin force-push ni rebase** (preserva el historial).

---

## 7. Reglas de honestidad metodológica

1. **El dueño eliminó las pruebas A/B manuales** por sesgo de percepción: **solo valen los datos**.
2. Los números de **predicción** se marcan como tal: *"no citar como medidos"*.
3. Los lotes de QA (1 a 5) y sus JSON viven **fuera del vault** (en los tar).
4. **Antes de optimizar, medir**: la correlación la hace la telemetría, no la intuición.
5. *"Las long tasks >50 ms son una **cota inferior** de la congestión — es el punto ciego del observador"* (ver [[Trampas de iOS y WebKit]] §4).

---

## 8. La lista de comprobación antes de un PR

- [ ] ¿Hay una nota `SPEC-*` que describa el cambio?
- [ ] ¿Los invariantes están declarados y respetados?
- [ ] ¿Si toqué UI, copié del lab con `// Fuente: …`?
- [ ] ¿No renombré ni borré marks de telemetría?
- [ ] ¿Corrí los E2E y pasan?
- [ ] ¿Guardé el par TXT + JSON del diagnóstico?
- [ ] ¿Documenté el cambio en la nota del vault correspondiente?
- [ ] ¿Añadí el PR al [[Índice de Pull Requests]]?

---

**Ver también:** [[Reglas de trabajo con agentes]] · [[Telemetría, diagnóstico y baseline]] · [[Arquitectura del motor (scanner-core)]] · [[Estado del repositorio web-scanner]] · [[Índice de Pull Requests]]
