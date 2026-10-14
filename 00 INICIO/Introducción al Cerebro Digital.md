---
proyecto: Proyecto Electoral E-14
tipo: guia
estado: vigente
actualizado: 2026-10-13
tags: [obsidian, guia, introduccion, cerebro-digital]
---

# 🧠 Introducción al Cerebro Digital

> **Bienvenido a tu segundo cerebro.** Este vault no es una carpeta de archivos: es una **red de ideas conectadas** que piensa contigo.
> Esta es la nota que lees **la primera vez** y a la que vuelves cuando dudes *"¿dónde guardo esto?"*.

---

## 1. La idea de fondo (30 segundos)

Tu cerebro **no archiva** ideas por carpeta. Tu cerebro **conecta** una idea con otra, y al volver a una, recuerdas las demás. **Obsidian imita eso**: cada nota es una neurona, cada enlace `[[ ]]` es una sinapsis.

| Cerebro biológico | Este vault |
|---|---|
| Una idea = una neurona | Una idea = **una nota** |
| Se conecta con otras | Se enlaza con `[[nombre de la otra nota]]` |
| Al activar una, trae a sus vecinas | Al abrir una, ves los **backlinks** |
| Se fortalece con uso | Se cultiva con 5 min al día |

> **La regla suprema:** *enlazar > archivar*. Antes de crear una carpeta, pregúntate si basta un enlace.

---

## 2. ¿Cómo está organizado este cerebro?

Una sola capa de carpetas numeradas. El número solo ordena; no significa importancia.

| # | Carpeta | Qué vive ahí | Cuándo entrar |
|---|---|---|---|
| `00` | **INICIO** | Mapas, guías, pendientes, **este resumen** | Siempre que abras el vault |
| `01` | **CONTEXTO ELECTORAL** | Cómo funciona el negocio electoral (el "por qué") | Para entender el dominio |
| `02` | **ARQUITECTURA Y REQUISITOS** | El "qué" y el "cómo" técnico (ERS, reglas, flujo) | Para construir o decidir |
| `03` | **PWA DIGITALIZADOR** | El producto móvil (estados e interfaz) | Para trabajar en el móvil |
| `04` | **SISTEMA DE SUPERVISIÓN** | El producto web (panel, anomalías, SLA, auditoría) | Para trabajar en el panel |
| `05` | **DISEÑO UI** | Design system y capturas de pantalla | Para diseñar pantallas |
| `06` | **RECURSOS** | PDF de actas reales de ejemplo | Como material de referencia |
| `07` | **DIARIO** | Notas diarias: decisiones, avances, dudas | **Rutina diaria** |
| `08` | **PLANTILLAS** | Plantillas reutilizables | Al crear notas repetitivas |
| `09` | **INGENIERÍA Y AGENTES** | El desarrollo real: motor, lógica, PRs, backlog | Para construir o entender el código |
| `99` | **REVISAR** | Cuarentena: lo dudoso antes de borrar | Solo cuando quieras limpiar |

> ⚠️ **Dentro de una carpeta no se crean subcarpetas nuevas** (salvo adjuntos de un mismo tema). Si necesitas otra cosa, usa enlaces y etiquetas.

---

## 3. Las 3 notas que son la puerta de entrada

No empieces por cualquier lado. **Estas tres** son los mapas maestros:

| Nota | Para qué sirve |
|---|---|
| 🏠 [[Inicio - Proyecto Electoral]] | El mapa del **negocio** y del vault completo |
| 📑 [[Resumen de Cambios Implementados]] | El resumen de **qué se construyó y qué cambió** |
| ⚙️ [[Inicio - Ingeniería y Agentes]] | La puerta de entrada a la **ingeniería** y el código |

> **Tu rutina al abrir el vault:** abre [[Inicio - Proyecto Electoral]] → mira [[Tablero de próximos pasos]] → revisa [[Backlog y decisiones pendientes]]. En 2 minutos sabes qué tocar hoy.

---

## 4. Los 4 movimientos del cerebro (el método)

Inspirado en **PARA** (Tiago Forte) y **Zettelkasten** (Niklas Luhmann), adaptado a este vault:

```
  1. CAPTURAR          2. PROCESAR          3. CONECTAR          4. CREAR
  ───────────          ───────────          ───────────          ───────────
  Todo lo que         Reescribe la         Añade al menos       Usa las notas
  aprendes o          idea CON TUS         un enlace a una      conectadas para
  decides va a        palabras. Si no      nota existente.       producir algo:
  una nota.           puedes explicarla,   Una nota sin          un ERS, una
  Sin                 no la entendiste.    enlaces es una        decisión, una
  perfeccionismo.                          nota muerta.          pantalla.
```

**Regla de oro:** *una nota = una idea*. Si una nota habla de tres cosas, son tres notas enlazadas.

---

## 5. Enlazar (lo más importante que vas a aprender)

```markdown
[[Nombre exacto de la nota]]                  → enlace normal
[[Nombre de la nota|texto que se ve]]         → enlace con alias
![[Nombre de la nota]]                        → incrusta el contenido completo
![[captura.png]]                              → muestra una imagen
[[Nota#Sección]]                              → enlace a un encabezado
```

**Truco:** escribe `[[` y Obsidian autocompleta. Si el nombre no existe, al hacer clic lo crea.

> **Regla del mínimo:** al terminar de escribir una nota, enlaza al menos **una** nota existente. Una nota sin enlaces es invisible para el grafo.

---

## 6. ¿Carpeta, etiqueta o propiedad? (la regla práctica)

| Usa… | Para… | Ejemplo |
|---|---|---|
| **Carpeta** | Dónde *vive* la nota (una sola vez) | `02 ARQUITECTURA Y REQUISITOS` |
| **Etiqueta** | Temas transversales (varios a la vez) | `#e14`, `#pendiente`, `#duda` |
| **Propiedad** | Datos estructurados y filtrables | `tipo: producto`, `estado: vigente` |
| **Enlace** | Relaciones entre ideas | `[[Especificación de Requerimientos (ERS)]]` |

**Error típico del principiante:** crear 40 carpetas anidadas. Este vault usa **una capa** y el resto se resuelve con enlaces y propiedades.

---

## 7. Propiedades (el frontmatter que ya tiene todo el vault)

Cada nota empieza así:

```yaml
---
proyecto: Proyecto Electoral E-14
tipo: contexto        # contexto | requisito | producto | diseno | mapa | guia | decision | idea | ingenieria
estado: vigente       # vigente | en-revision | obsoleto | aprobado
actualizado: 2026-10-13
tags: [e14, pwa, ui]
---
```

**Para qué sirve:** con propiedades puedes **filtrar, ordenar y construir tableros automáticos** (con el plugin Dataview). Es lo que convierte una carpeta de notas en una **base de datos**.

---

## 8. Atajos que te ahorran horas

| Atajo | Qué hace |
|---|---|
| `Ctrl + O` | **Quick switcher**: salta a cualquier nota por su nombre |
| `Ctrl + P` | **Paleta de comandos**: todo lo que Obsidian sabe hacer |
| `Ctrl + Shift + F` | Buscar en **todo** el vault |
| `Ctrl + N` | Nota nueva |
| `Ctrl + E` | Alternar edición / lectura |
| `Ctrl + clic` | Abrir el enlace en una pestaña nueva |
| `Alt + ←` | Volver atrás (historial de navegación) |

> **Panel derecho siempre abierto:** Backlinks (quién me enlaza), Enlaces salientes y Esquema. Es tu radar.

---

## 9. Buscar como un pro

```
tag:#e14                    → solo notas con esa etiqueta
path:"02 ARQUITECTURA"      → solo dentro de esa carpeta
file:ERS                    → solo notas cuyo nombre contiene ERS
line:(score 9)              → líneas que contienen ese texto
tag:#e14 -tag:#obsoleto     → combinar y excluir
```

Guarda búsquedas frecuentes con **Marcadores** (panel izquierdo) y las tendrás a un clic.

---

## 10. El diario (el hábito que sostiene todo)

En `07 DIARIO` creas una nota por día (`Ctrl + P` → "Nota diaria: abrir la nota de hoy"). Escribes:

- **🚀 Avancé:** qué hiciste hoy
- **🧭 Decidí:** qué decidiste y **por qué** (esto vale oro dentro de 3 meses)
- **❓ Dudo:** qué te quedó trabado
- **🔗 Notas que toqué:** enlaces a las notas del proyecto

> **Rutina recomendada (5 minutos):** abre la nota de hoy → escribe 3 bullets → enlaza al menos una nota existente.

Con el tiempo, el diario se convierte en la **línea de tiempo del proyecto** y los backlinks te muestran **dónde nació cada idea**.

---

## 11. Plantillas (en `08 PLANTILLAS`)

| Plantilla | Cuándo usarla |
|---|---|
| **Nota diaria** | Cada día de trabajo |
| **Reunión** | Cuando hables con alguien del proyecto |
| **Decisión** | Cuando elijas entre opciones (¡clave para no repetir discusiones!) |
| **Idea** | Cuando se te ocurra algo suelto que no sabes dónde poner |

Para usarlas: `Ctrl + P` → **Insertar plantilla** → elige la plantilla.

---

## 12. Cinco reglas de oro

1. **Enlazar > archivar.** Antes de crear una carpeta, pregúntate si basta un enlace.
2. **Escribe para tu yo del futuro.** Él no recordará el contexto de hoy.
3. **Una idea por nota.** Si habla de tres cosas, son tres notas enlazadas.
4. **Nada se borra sin pensar.** Lo dudoso va a `99 REVISAR`, no a la papelera.
5. **El vault no se organiza, se cultiva.** Dedica 5 minutos al día en vez de 5 horas una vez al año.

---

## 13. Plugins que valen la pena (cuando quieras subir de nivel)

Se instalan en *Ajustes → Plugins de la comunidad*:

| Plugin | Para qué |
|---|---|
| **Dataview** | Convertir propiedades en tablas y tableros automáticos |
| **Templater** | Plantillas con lógica (fechas, títulos automáticos) |
| **Tasks** | Gestionar pendientes con `- [ ]` en todo el vault |
| **Excalidraw** | Dibujar diagramas a mano alzada |
| **Kanban** | Tableros tipo Trello dentro de una nota |
| **Calendar** | Navegar las notas diarias |

> ⚠️ Los plugins de la comunidad son código de terceros: instala solo los conocidos y revisa qué permisos piden.

---

## 14. Ejercicio práctico (15 minutos para dominar el cerebro)

- [ ] Abre la nota de hoy en `07 DIARIO` y escribe 3 líneas sobre el proyecto
- [ ] Crea una nota nueva llamada `Mi primera idea sobre el E-14`
- [ ] Dentro, enlaza dos notas existentes: `[[Resumen de Cambios Implementados]]` y `[[Que datos contiene un Acta E-14]]`
- [ ] Añade la propiedad `tipo: idea` y la etiqueta `#idea`
- [ ] Abre el **grafo** (`Ctrl + P` → "Abrir grafo") y mira cómo aparecen tus conexiones
- [ ] Abre el panel de **Backlinks** de la nota enlazada y comprueba que apareces ahí

Cuando termines esto, ya sabes usar Obsidian mejor que el 90 % de la gente.

---

## 15. Tu flujo de trabajo diario (la receta)

```
  Abrir vault
     │
     ▼
  [[Inicio - Proyecto Electoral]]  ← mira el mapa y los pendientes
     │
     ▼
  [[Tablero de próximos pasos]]   ← qué toca hoy
     │
     ▼
  Trabajar en la tarea
     │
     ▼
  Nota del día en 07 DIARIO       ← 3 bullets: avancé / decidí / dudé
     │
     ▼
  Enlazar las notas que toqué     ← así el diario teje la red
     │
     ▼
  Cerrar vault                    ← mañana, lo retomas en 2 minutos
```

---

## 16. Qué hacer cuando…

| Situación | Qué hacer |
|---|---|
| **Aprendes algo nuevo** | Crea una nota `tipo: contexto`, enlázala con 1 existente |
| **Tomas una decisión** | Usa la plantilla **Decisión**; deja rastro del *por qué* |
| **Se te ocurre una idea suelta** | Usa la plantilla **Idea**; no la dejes volar |
| **Algo se vuelve obsoleto** | Cambia su `estado: obsoleto`; no lo borres |
| **No sabes si algo sirve** | Móvelo a `99 REVISAR`; revisa cada semana |
| **Un enlace está roto** (`[[nota]]` sin destino) | La creas o la quitas; las notas vacías confunden |
| **El vault se siente caótico** | Abre el grafo y busca **notas huérfanas** (sin enlaces) |

---

## 17. El grafo: el pulso del cerebro

Abre el grafo (`Ctrl + P` → "Abrir grafo"). Verás puntos (notas) y líneas (enlaces).

**Qué buscar:**
- 🌟 **Notas huérfanas** (sin conexiones) → están perdidas; enlázalas o cuarenténalas.
- 🕸️ **Clústeres** → grupos de notas que hablan de lo mismo (valida que la estructura tiene sentido).
- 🎯 **Notas-hub** (muchos enlaces entran) → son tus mapas; mantenlas actualizadas.

> Un cerebro sano **no es simétrico**: tiene hubs, clústeres y caminos largos. Lo que no debe tener es **neuronas solas**.

---

## Siguiente parada

- [[Obsidian 101 - Tu cerebro digital]] — el manual completo de Obsidian (profundiza lo de aquí)
- [[Convenciones del vault]] — las reglas concretas de este vault
- [[Inicio - Proyecto Electoral]] — el mapa del proyecto
- [[Resumen de Cambios Implementados]] — qué se ha construido hasta hoy

---

*Última actualización: 2026-10-13. Si mejoras tu flujo, actualiza esta nota: tu yo del futuro te lo agradecerá.*
