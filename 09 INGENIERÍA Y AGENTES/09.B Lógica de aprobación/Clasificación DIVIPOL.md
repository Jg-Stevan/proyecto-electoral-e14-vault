---
proyecto: Proyecto Electoral E-14
tipo: ingenieria
estado: vigente
actualizado: 2026-10-13
tags: [ingenieria, e14, clasificacion, ocr]
---

# Clasificación DIVIPOL

> Cómo la app pasa de *"una foto de un papel"* a *"ACTA de la MESA 001, El Cairo, Egipto, TRANSMISIÓN P1 de 2"*. Aquí está el fin del temido **"ACTA NO RECONOCIDA"**.

## 1. Los tres niveles de confianza

El clasificador (`clasificador.ts`) compara lo leído por OCR contra la base DIVIPOL con **trigramas + coeficiente de Dice**:

| Nivel | Umbral | Qué significa |
|---|---|---|
| **AUTO** | **≥ 0.82** | Identificación plena → score **10.0** y envío automático |
| **SUGERIDA** | **0.60 - 0.81** | Se propone y se precarga, pero **pasa por el panel de corrección** |
| **MANUAL** | **< 0.60** | Sin propuesta fiable → el operador resuelve |

## 2. Normalización y tolerancia a errores de OCR

- Solo se procesan **impresos** (los manuscritos están fuera de alcance).
- Normalización **NFKD + mayúsculas**.
- Mapa de confusión OCR **aplicado solo en tokens numéricos**:

```
O → 0    Q → 0    I → 1    L → 1    S → 5    B → 8
```

- **Anclas regex tolerantes:** departamento/consulado, país/municipio, zona, puesto, mesa, lugar, banda (tipo), `Ver/Pag/de`, pie (kit).
- **Ancla de mesa tolerante a la confusión M→N:** `/[MN]ESA…/` — así un OCR que lee `NESA. 001` **rescata `001`**.

## 3. Las reglas de decisión

| Regla | Contenido |
|---|---|
| **3** | Municipio sin departamento ⇒ se deduce **solo si hay un único candidato ≥ 0.82** |
| **4b** ⭐ | Si la cascada `dep → mun → puesto` **no resuelve**, se hace **rescate global por NOMBRE** contra los **14.438 puestos**, exigiendo mejor match ≥ `UMBRAL_SUGERIDA` **y un gap ≥ 0.05** sobre el 2.º. Si hay 2+ cercanos → **MANUAL con lista** |
| **5** | Mesa válida si `1 ≤ mesa ≤ countTable`; si no → SUGERIDA con aviso. **Nunca descartar** |

**AUTO exige la ruta completa + mesa válida + tipo leído.** Prohibiciones explícitas:
- ❌ Auto por nombre solo
- ❌ Asumir el tipo de copia
- ❌ Buscar contra departamento/municipio en el rescate global
- ❌ Sin tipo leído → **máximo SUGERIDA**

## 4. El caso real que originó la regla 4b

**Síntoma:** un chip clasificado como MEDIO mostraba **"CLASIFICACIÓN PENDIENTE"** y chips en `---`, aunque *"El Cairo - Consulado"* se veía perfectamente en el acta.

**Diagnóstico con el texto OCR real** (instrumentado):

```
«LADO: 88 - CONSULADOS / 35 - EGIPTO / 05 PUESTO: 02 NESA. 001 /
  R: El Cairo - Consulado / % mil A…»
```

**Causa raíz:** el recorte izquierdo destruyó las **palabras clave** (`CONSULADO→LADO`, `PAIS→∅`, `LUGAR→R:`), el OCR leyó `MESA→NESA`, y la banda `TRANSMISIÓN` salió basura → **todas las anclas fallan** → `MANUAL`.

**Pero el nombre del puesto estaba intacto.** Y el clasificador **jamás lo buscaba sin dep/mun resueltos**.

**La prueba (unit test con bun, clasificador real + `divipol.json` real + texto OCR real):**
- ANTES → `{nivel: MANUAL, todo null}` (bug reproducido 1:1)
- Similitud de *"R: El Cairo - Consulado"* → **0.92** (≥ UMBRAL_AUTO 0.82)
- Búsqueda global en 14.438 puestos → **mejor match ÚNICO 88/335/05/02 con 0.92** y gap claro frente a los otros "CAIRO" (SOCORRO, ALTO, RURAL, días de la semana)
- **Coste: 958 ms** en el peor caso en bun; **0** cuando la cascada resuelve

⇒ **Regla 4b creada.** El banner pasó de "CLASIFICACIÓN PENDIENTE" a **"EL CAIRO - CONSULADO" + MESA 001**, con nivel **SUGERIDA** (el tipo era ilegible: no se asume).

## 5. Del gate de texto al panel de corrección

**Antes:** `PATRON_CABECERA = /E\s*-\s*14|REGISTRADURIA|REGISTRADURÍA/i` decidía RECHAZADA.

**Ahora (cambio de modelo):**
- **RECHAZADA solo por calidad.**
- Cabecera no leída (SUGERIDA / MANUAL) → **`EN_REVISION_HUMANA`** con **panel de corrección en cascada**: `dep → mun/país → zona → puesto → mesa` + tipo + páginas, con **sugerencia precargada** y **degradación a texto libre** si no hay base.

Esto convirtió el fracaso de identificación en un **flujo de trabajo**, no en un callejón sin salida.

## 6. Archivado en mesas reales

| Elemento | Formato |
|---|---|
| Clave de mesa | `${dep}-${mun}-${zon}-${std}-${mesa}` → p.ej. `88-335-05-02-001` |
| Vista ACTAS | Acordeón con **"MESAS CLASIFICADAS"** antes del seed |
| Duplicado | En el mismo hueco → **reemplaza y avisa** |
| Nombre de export | `E14_{KIT}_{dep}_{mun}_{zon}_{puesto}_{mesa}_{TIPO}-{pag}.jpg` |

---

**Ver también:** [[Modelo del Acta E-14]] · [[Doctrina del score y evaluarGate]] · [[Estados, intentos y contingencias]] · [[Estados del Acta E-14 en la PWA]]
