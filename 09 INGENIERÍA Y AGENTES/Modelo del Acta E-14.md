---
proyecto: Proyecto Electoral E-14
tipo: ingenieria
estado: vigente
actualizado: 2026-10-13
tags: [ingenieria, e14, modelo, divipol, anatomia]
---

# 🧠 Modelo del Acta E-14

> **La anatomía real del formulario y de DIVIPOL** — la base sobre la que se construyen el clasificador, el guard de completitud y el score. Si no entiendes el papel, no entiendes el código.

---

## 1. El escenario electoral real

| Dato | Valor |
|---|---|
| **Contienda** | Presidente y Vicepresidente 2026 |
| **Vuelta** | **Segunda vuelta** |
| **Ámbito** | **Exterior** (consulados y embajadas) |
| **Departamento DIVIPOL** | **88 = CONSULADOS** |
| **Duración** | 7 días (lunes a domingo), cierre diario 16:00 h local |
| **Volumen** | 1.489 mesas (lun-sáb), hasta 2.181 el domingo pico |

> ⚠️ El vault tenía conflicto de escenario (ver [[Contradicciones detectadas]] punto 3). **Este es el escenario vigente**, confirmado por la implementación.

---

## 2. El ejemplar y las páginas

```
1 acta = 2 copias × 2 páginas = 4 hojas por mesa
```

| Copia | Dígito (barcode) | Prioridad | Uso |
|---|---|---|---|
| **DELEGADOS** | `2` | 🔴 **ALTA** | Escrutinio oficial con valor probatorio |
| **TRANSMISIÓN** | `3` | 🟡 SECUNDARIA | Preconteo informativo |
| ~~CLAVEROS~~ | `1` | — | **NO entra al pipeline digital** (va al arca triclave) |

> RN-01 (Priorización de Ingesta): el pipeline **da prioridad estricta** al ejemplar Delegados; Transmisión se encola en segundo plano (ver [[Especificación de Requerimientos (ERS)]]).

---

## 3. La estructura del formulario (4 zonas)

```
  [■ Fiducial]                                          [■ Fiducial]
  ┌──────────────────────────────────────────────────────────────────┐
  │ [QR]  EJEMPLAR: DELEGADOS (2)   [Barcode 15D]   Ver:01 Pág:1 de 2│
  │                  REGISTRADURÍA NACIONAL DEL ESTADO CIVIL          │
  │ CONSULADO/DEPTO: 88   PAÍS/MUN: 335   ZONA:05  PUESTO:02  MESA:001│
  │                         X 7-23-10-19 X  (código de imprenta)       │
  ├──────────────────────────────────────────────────────────────────┤
  │ [1] NIVELACIÓN DE MESA  (E-11, urna, incinerados)                │
  ├──────────────────────────────────────────────────────────────────┤
  │ [2] CUERPO DE VOTACIÓN  (candidatos, blanco, nulos, no marcados) │
  ├──────────────────────────────────────────────────────────────────┤
  │ [3] FIRMAS, CÉDULAS Y CONSTANCIAS  (última página)               │
  ├──────────────────────────────────────────────────────────────────┤
  │ No. Form: 399          KIT: 399          Civ: 797                │
  └──────────────────────────────────────────────────────────────────┘
  [■ Fiducial]                                          [■ Fiducial]
```

Detalle completo en [[Que datos contiene un Acta E-14]] y [[Estructura y Significado del Código de Barras]].

---

## 4. DIVIPOL — la estructura geopolítica

La identificación de cada acta sigue la jerarquía **DIVIPOL** oficial:

```
Departamento/Consulado  >  Municipio/País  >  Zona  >  Puesto  >  Mesa
       (88)                (335 = Egipto)    (05)     (02)       (001)
```

| Nivel | Dato | Ejemplo |
|---|---|---|
| **Departamento/Consulado** | `88` = Consulados (ámbito exterior) | `88` |
| **Municipio/País** | El país donde está el consulado | `335` = Egipto |
| **Zona** | Agrupación consular | `05` |
| **Puesto** | El consulado concreto | `02` = El Cairo - Consulado |
| **Mesa** | Mesa de votación dentro del consulado | `001` |

**Clave de mesa (canónica en el sistema):**
```
${dep}-${mun}-${zon}-${std}-${mesa}  →  88-335-05-02-001
```

**Base cargada:** `divipol.json` contiene **14.438 puestos** contra los que se hace el rescate global (ver [[Clasificación DIVIPOL]] regla 4b).

---

## 5. El código de barras de 15 dígitos

Es la **cédula única de cada hoja**. Se imprime antes de la jornada (no contiene votos, solo identificación):

| Tramo | Dígitos | Campo | Ejemplo |
|---|---|---|---|
| Tipo de Elección | 1-2 | `71` = Presidencia | `71` |
| Kit / Formulario | 3-8 | Secuencial de la mesa | `000399` |
| Tipo de Ejemplar | 9 | `2`=Delegados · `3`=Transmisión | `2` |
| Versión | 10-11 | Diagramación aprobada | `01` |
| Página Actual | 12-13 | Hoja leída | `01` |
| Total Páginas | 14-15 | Hojas del acta | `02` |

**Ejemplo real (El Cairo, Delegados P1 de 2):** `710003992010102`

---

## 6. Los fiduciales (las 4 esquinas negras)

Cuadrados negros macizos en los vértices del papel. Sirven para:

1. **Detectar orientación** automática (evitar lecturas al revés).
2. **Corrección de perspectiva** (_deskew_).
3. **Mapear coordenadas (X, Y)** exactas de las casillas de votación.

Son la entrada del motor de visión (worker propio con Sobel + DFS, `QUAD_MIN_AREA_RATIO = 0.25` → ver [[Arquitectura del motor (scanner-core)]]).

---

## 7. Las tres secciones (lo que importa para el guard)

| # | Sección | Qué contiene | Relevancia para el guard |
|---|---|---|---|
| **1** | Cabecera y metadatos | QR, barcode 15D, DIVIPOL, imprenta | Señal de contenido (`senalActa`) — la cabecera se lee por OCR |
| **2** | Nivelación de mesa | Votantes E-11, urna, incinerados | Evidencia de que es un acta diligenciada |
| **3** | Cuerpo de votación | Candidatos, blanco, nulos, no marcados, total | La **matriz de votos** — lo que define §9.12 (ver [[Guard de completitud (borde)]]) |

> ⚠️ **Caso abierto (§9.12):** una hoja con **solo cabecera + nivelación** (sin matriz de votos) pasa el guard de completitud. Diseño preliminar listo, decisión pendiente en [[Backlog y decisiones pendientes]].

---

## 8. El nombre del archivo de exportación

Cuando el acta se archiva en la mesa real, el sistema genera:

```
E14_{KIT}_{dep}_{mun}_{zon}_{puesto}_{mesa}_{TIPO}-{pag}.jpg
Ej: E14_399_88_335_05_02_001_DELEGADOS-1.jpg
```

La vista ACTAS muestra un **acordeón con "MESAS CLASIFICADAS"** antes del seed. Los duplicados en el mismo hueco **reemplazan y avisan** (ver [[Clasificación DIVIPOL]] §6).

---

## 9. Por qué este modelo resolvió las contradicciones del vault

Antes del modelo, el vault tenía **3 contradicciones bloqueantes** (ver [[Contradicciones detectadas]]):

1. **Criterio de calidad** (¿binario o 3 bandas?) → resuelto: **precedencia + bandas + pisos** ([[Doctrina del score y evaluarGate]])
2. **Hardware objetivo** (¿2-4 GB o 4-8 GB?) → resuelto: **tiers adaptativos** ([[Memoria, render y tiers]])
3. **Escenario** (¿Congreso o Presidencial?) → resuelto: **Presidenciales 2026, 2ª vuelta, exterior** (esta nota)

**La verdad vive en el código del [repositorio](https://github.com/Jg-Stevan/web-scanner), no en las especificaciones.** Este modelo es la traducción de esa verdad al vault.

---

**Ver también:** [[Que datos contiene un Acta E-14]] · [[Estructura y Significado del Código de Barras]] · [[Clasificación DIVIPOL]] · [[Guard de completitud (borde)]] · [[Doctrina del score y evaluarGate]] · [[Digitalización en el Exterior]]
