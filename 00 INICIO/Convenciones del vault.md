---
proyecto: Proyecto Electoral E-14
tipo: guia
estado: vigente
actualizado: 2026-10-13
tags: [obsidian, convenciones]
---

# 🗂️ Convenciones del vault

> Reglas mínimas para que el vault siga siendo útil dentro de 6 meses.
> Si algo no encaja en estas reglas, cámbialas — pero escríbelo aquí.

---

## 1. Estructura de carpetas

Una sola capa de carpetas numeradas. El número solo sirve para ordenar; no significa importancia.

| Carpeta | Contenido |
|---|---|
| `00 INICIO` | Mapas, guías, pendientes. La puerta de entrada |
| `01 CONTEXTO ELECTORAL` | Cómo funciona el negocio electoral (el "por qué") |
| `02 ARQUITECTURA Y REQUISITOS` | El "qué" y el "cómo" técnico |
| `03 PWA DIGITALIZADOR` | Producto móvil |
| `04 SISTEMA DE SUPERVISIÓN` | Producto de escritorio/web |
| `05 DISEÑO UI` | Design system y capturas de pantalla |
| `06 RECURSOS` | Material de referencia (PDF, bocetos, ejemplos) |
| `07 DIARIO` | Notas diarias |
| `08 PLANTILLAS` | Plantillas reutilizables |
| `09 INGENIERÍA Y AGENTES` | El desarrollo real: motor, lógica de aprobación, agentes, PRs y backlog |
| `99 REVISAR - candidatos a eliminar` | Cuarentena: nada se borra sin pasar por aquí |

**Dentro de una carpeta no se crean subcarpetas nuevas** salvo que sean adjuntos de un mismo tema (como las capturas de `05 DISEÑO UI`). Si necesitas otra cosa, usa enlaces y etiquetas.

---

## 2. Nombres de archivos

- Español natural, con acentos y mayúsculas donde corresponda: `Flujo de Validación y Aprobación Operativa.md`
- Sin guiones bajos ni `camelCase`
- Sin fechas en el nombre (excepto las notas del diario, que usan `YYYY-MM-DD`)
- Sin numeración: el orden lo da el mapa, no el alfabeto
- Si el nombre se repite en dos ideas distintas, añade un calificador entre paréntesis: `Panel de Control (Digitalizador).md`

---

## 3. Propiedades estándar

Toda nota del proyecto empieza así:

```yaml
---
proyecto: Proyecto Electoral E-14
tipo: contexto
estado: vigente
actualizado: 2026-10-13
tags: [e14, tema]
---
```

**Valores permitidos de `tipo`:**

| Valor | Significa |
|---|---|
| `contexto` | Cómo funciona el dominio electoral |
| `requisito` | Reglas, especificaciones, restricciones |
| `producto` | Pantallas, módulos, comportamiento del sistema |
| `diseno` | Sistema de diseño y recursos visuales |
| `mapa` | Notas índice / MOC |
| `guia` | Cómo hacer algo |
| `decision` | Una decisión tomada y su justificación |
| `idea` | Algo sin madurar |
| `reunion` | Registro de una conversación |

**Valores permitidos de `estado`:** `vigente`, `en-revision`, `obsoleto`, `aprobado`.

---

## 4. Etiquetas (`tags`)

Pocas y estables. Se escriben en minúscula, sin acentos y con guiones.

| Etiqueta | Para qué |
|---|---|
| `#e14` | Toda nota del proyecto |
| `#pwa` / `#supervisor` | A qué producto pertenece |
| `#decision` / `#duda` / `#pendiente` | Estado mental de la idea |
| `#aprendizaje` | Notas de estudio |
| `#referencia` | Datos citados de fuentes externas |

**Nunca** crees una etiqueta con el nombre de una carpeta (`#contexto-electoral`): para eso ya está la carpeta.

---

## 5. Enlaces

- Siempre **wikilinks**: `[[Nota]]`, no `[Nota](ruta/Nota.md)`.
- Usa alias cuando el nombre largo rompa la frase: `[[Especificación de Requerimientos (ERS)|el ERS]]`.
- **Regla del mínimo:** al terminar de escribir una nota, enlaza al menos **una** nota existente.
- Cuando muevas una nota de carpeta **desde el explorador de Windows**, los enlaces con ruta se rompen. Muévelas **desde Obsidian** (arrastrando) y él los actualiza solo.

> ⚠️ Esto es importante: el vault tiene activada la opción *"Actualizar enlaces automáticamente"*, pero **solo funciona si el movimiento se hace dentro de Obsidian**, no desde el Explorador de Windows.

---

## 6. Qué va en cuarentena

A `99 REVISAR - candidatos a eliminar` va:

- Material de otro escenario o proyecto
- Duplicados
- Archivos sin nombre, corruptos o de origen desconocido
- Carpetas vacías

**Nunca** borres directo. Primero cuarentena, luego revisión, luego borrado. Y siempre con el backup al día.

---

## 7. Adjuntos

- Los PDF y capturas de un tema viven **junto a la carpeta de diseño del tema**, no sueltos en la raíz.
- Renombra los adjuntos al subirlos (nada de `RUID5a97d3f1e80f...`).
- Si un adjunto pesa más de ~10 MB, pregúntate si debe vivir en el vault o fuera de él (un vault no es un disco duro).

---

## 8. Rutina mínima

| Frecuencia | Qué hacer |
|---|---|
| Al empezar a trabajar | Abrir [[Inicio - Proyecto Electoral]] |
| Cada día | 3 bullets en la nota del día (`07 DIARIO`) |
| Al decidir algo | Nota con la plantilla **Decisión** |
| Cada semana | Revisar [[Tablero de próximos pasos]] y la cuarentena |
| Cada mes | Ver el grafo y buscar notas huérfanas (sin enlaces) |
