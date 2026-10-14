---
proyecto: Proyecto Electoral E-14
tipo: guia
estado: vigente
actualizado: 2026-10-13
tags: [ingenieria, agentes, ia, reglas, guia]
---

# 🤖 Reglas de trabajo con agentes

> **Cómo delegar en agentes de IA sin perder control del proyecto.**
> Los agentes son productivos cuando **se les da contexto, límites y verificación**. Son peligrosos cuando se les deja improvisar.

---

## 1. La regla #0 (de dónde sale todo)

> **El laboratorio es la fuente de verdad.** Lo que ya existe y funciona en `scanner-lab` se **copia literalmente** al e14, con un comentario que declara el origen:

```ts
// Fuente: apps/scanner-lab/src/components/scanner/CameraView.tsx L116-L223
```

> Los agentes **no reinventan**. Si un agente te propone "simplificar" algo que ya funciona en el lab, **detente**: fue exactamente eso lo que originó el bug F-LENS en producción (los Android capturaban con la gran angular).

Ver [[Arquitectura del motor (scanner-core)]] §4 y [[Método de trabajo (spec, implementa, audita)]].

---

## 2. Las 5 reglas para trabajar con agentes

### R1 · Contexto antes de código
Nunca pidas "implementa X" sin dar contexto. Dale al agente:
- **La SPEC** (el problema y la decisión)
- **La nota del vault** donde vive el tema
- **Los invariantes** (qué no se puede romper)
- **La telemetría** que confirma el éxito

### R2 · Scope cerrado y atómico
- Un agente = una tarea = un PR.
- Prohibido "y de paso arregla esto otro". El caos llega con los cambios colaterales.
- *"1 punto = 1 commit; sin force-push ni rebase"* (protocolo nocturno, ver [[Método de trabajo (spec, implementa, audita)]] §6).

### R3 · Cero degradación del dato
La constitución del proyecto (ver [[Arquitectura del motor (scanner-core)]] §3):
> *"El rendimiento y la estabilidad se ganan con memoria, CPU de análisis y CSS — NUNCA degradando el dato de imagen."*

Si un agente propone "optimizar" bajando resolución o calidad de imagen → **rechazo automático**.

### R4 · Telemetría intocable
Los marks del pipeline tienen **nombre fijo**. Los E2E de auditoría dependen de ellos. Un agente **no renombra ni borra** telemetría. Ver [[Telemetría, diagnóstico y baseline]] §1.

### R5 · Verificación obligatoria
Nada se mergea sin auditoría E2E y el veredicto del `analizador-diag-v4.js` (juez objetivo fuera del repo). *"Mismo JSON → mismo veredicto."* Ver [[Método de trabajo (spec, implementa, audita)]] §4.

---

## 3. Qué pedirle a un agente (el prompt bueno)

```
CONTEXTO:
- Repo: github.com/Jg-Stevan/web-scanner (monorepo)
- Tarea: [describe la tarea, una sola]
- SPEC de origen: [enlace a la nota SPEC-*]
- Invariante: [qué no se puede rom]
- Telemetría a confirmar: [qué mark debe moverse]

REGLAS:
- Copia literal del lab con `// Fuente: …` si tocas UI.
- No renombres ni borres marks de telemetría.
- No degradues la imagen (tope 4032, WYSIWYG).
- 1 PR = 1 cambio atómico. Sin force-push ni rebase.

ENTREGABLE:
- PR con los cambios.
- Par TXT + JSON del diagnóstico.
- Actualización de la nota del vault correspondiente.
```

---

## 4. Qué NO pedirle a un agente

| ❌ No pidas | Por qué |
|---|---|
| "Simplifica el arranque de cámara" | Originó el bug F-LENS en producción (gran angular) |
| "Optimiza bajando la resolución" | Viola la constitución: no degradar el dato |
| "Renombra esta telemetría" | Rompe los E2E de auditoría |
| "Arregla esto y de paso lo otro" | Cambios colaterales = caos |
| "Reinventa este componente del lab" | El lab es la fuente de verdad; se copia, no se reinventa |
| "Decide tú el criterio de calidad" | La doctrina está en [[Doctrina del score y evaluarGate]]; no se delega |

---

## 5. El incidente que dio origen a estas reglas

> Durante la fase lógica, alguien "simplificó" el arranque de cámara del lab y dejó solo la cascada `facingMode`. Resultado: **en muchos Android el acta se capturaba con la gran angular** (bug F-LENS en producción).

**De ahí salieron:**
1. La **regla de oro #0** (el lab es la fuente de verdad).
2. La **obligación de auditar copia por copia** (`SPEC-e14-auditoria-copias`, hallazgos H1-H6: 13 copias fieles, 3 adaptaciones legítimas, 5 desviaciones).
3. La **obligación de declarar el origen** con `// Fuente: …`.

> Un agente sin estas reglas es exactamente el origen de ese bug.

---

## 6. La auditoría de copias (el control post-agente)

Después de que un agente copia del lab al e14, se audita copia por copia:

| Categoría | Qué significa | Acción |
|---|---|---|
| **Copias fieles** | Literal con `// Fuente: …` | ✅ Aceptar |
| **Adaptaciones legítimas** | Declaradas y justificadas | ✅ Aceptar |
| **Desviaciones** | Sin justificar | ⚠️ Revertir o justificar |

> La auditoría no es opcional: es la **red de seguridad** contra los agentes que "mejoran" lo que ya funciona.

---

## 7. El crash-reporter (observar, no actuar)

El crash-reporter es **observe-only**:
- Banner rojo + anillo en localStorage ≤10 + `__crashLog`.
- **Prohibido** `preventDefault`, retry o fetch.

> Su único trabajo es **contar la verdad**, no salvar la app. Si un agente lo "mejora" con retry, pierde su propósito. Ver [[Memoria, render y tiers]] §1.

---

## 8. La regla de la honestidad de la simulación

La simulación **nunca puede relajar una regla del flujo real**. Las guardas están explícitas en código:

> *El piso AUTO solo se re-aplica si `scoreSimulado >= 8`* → así el chip MEDIO **no se convierte en un segundo ÓPTIMA**.

Si un agente "simplifica" esta guarda, la simulación miente y el demo pierde valor. Ver [[El puente - scanner-core-bridge]] §6 y [[Estados, intentos y contingencias]] §5.

---

## 9. Lista de comprobación para un agente

Antes de aceptar el trabajo de un agente:

- [ ] ¿Declaró el origen con `// Fuente: …` (si tocó UI)?
- [ ] ¿Respetó los invariantes de la SPEC?
- [ ] ¿No renombró ni borró marks de telemetría?
- [ ] ¿No degradó la imagen (tope 4032, WYSIWYG, R-10)?
- [ ] ¿El PR es atómico (1 cambio = 1 PR)?
- [ ] ¿Corrió los E2E y pasan?
- [ ] ¿Guardó el par TXT + JSON del diagnóstico?
- [ ] ¿Documentó el cambio en la nota del vault?
- [ ] ¿Añadió el PR al [[Índice de Pull Requests]]?

> Si algo falla, **revisa la SPEC y vuelve a empezar**. No parches en caliente.

---

**Ver también:** [[Método de trabajo (spec, implementa, audita)]] · [[Arquitectura del motor (scanner-core)]] · [[Telemetría, diagnóstico y baseline]] · [[Estado del repositorio web-scanner]] · [[Lecciones y trampas]]
