---
proyecto: Proyecto Electoral E-14
tipo: guia
estado: vigente
actualizado: 2026-10-13
tags: [obsidian, guia, aprendizaje]
---

# 🧠 Obsidian 101 — Tu cerebro digital

> Esta nota es tu manual. Léela una vez completa y vuelve a ella cuando dudes.
> Objetivo: que dejes de usar Obsidian como "carpetas con archivos" y empieces a usarlo como **una red de ideas que se conectan**.

---

## 1. Los 4 conceptos que lo cambian todo

| Concepto | Qué es | Por qué importa |
|---|---|---|
| **Nota** | Un archivo `.md` de texto plano | Es tuyo para siempre, sin programas de por medio |
| **Enlace** | `[[Nombre de otra nota]]` | Conectas ideas en vez de archivarlas |
| **Backlink** | La lista de notas que **te enlazan a ti** | Descubres conexiones que no recordabas |
| **Grafo** | Mapa visual de todos los enlaces | Ves la forma de tu conocimiento |

**La diferencia clave:** una carpeta te obliga a elegir *un* lugar. Un enlace te deja estar en *todos* los contextos a la vez. Por eso este vault ahora prioriza enlaces sobre carpetas.

---

## 2. Enlazar (lo más importante que vas a aprender)

```markdown
[[Nombre exacto de la nota]]                  → enlace normal
[[Nombre de la nota|texto que se ve]]         → enlace con alias
![[Nombre de la nota]]                        → incrusta el contenido completo
![[captura.png]]                              → muestra una imagen
[[Nota#Sección]]                              → enlace a un encabezado
[[Nota^bloque]]                               → enlace a un párrafo concreto (bloque)
```

**Truco:** escribe `[[` y Obsidian te autocompleta. Si el nombre no existe, al hacer clic lo crea.

---

## 3. Propiedades (el frontmatter YAML)

Es el bloque entre `---` al inicio de la nota. Ya lo aplicamos a todo el vault:

```yaml
---
proyecto: Proyecto Electoral E-14
tipo: contexto        # contexto | requisito | producto | diseno | mapa | guia | decision | idea
estado: vigente       # vigente | en-revision | obsoleto | aprobado
actualizado: 2026-10-13
tags: [e14, pwa, ui]
---
```

**Para qué sirve:** con propiedades puedes filtrar, ordenar y construir tableros automáticos (con el plugin Dataview). Es lo que convierte una carpeta de notas en una base de datos.

---

## 4. ¿Carpeta, etiqueta o propiedad? (regla práctica)

| Usa… | Para… | Ejemplo |
|---|---|---|
| **Carpeta** | Dónde *vive* la nota (una sola vez) | `02 ARQUITECTURA Y REQUISITOS` |
| **Etiqueta** | Temas transversales (varios a la vez) | `#e14`, `#pendiente`, `#duda` |
| **Propiedad** | Datos estructurados y filtrables | `tipo: producto`, `estado: vigente` |
| **Enlace** | Relaciones entre ideas | `[[Especificación de Requerimientos (ERS)]]` |

**Error típico del principiante:** crear 40 carpetas anidadas. Este vault usa **una capa de carpetas** y el resto se resuelve con enlaces y propiedades.

---

## 5. Atajos que te ahorran horas

| Atajo | Qué hace |
|---|---|
| `Ctrl + O` | **Quick switcher**: salta a cualquier nota escribiendo su nombre |
| `Ctrl + P` | **Paleta de comandos**: todo lo que Obsidian sabe hacer |
| `Ctrl + Shift + F` | Buscar en **todo** el vault |
| `Ctrl + N` | Nota nueva |
| `Ctrl + E` | Alternar edición / lectura |
| `Ctrl + clic` | Abrir el enlace en una pestaña nueva |
| `Ctrl + ,` | Ajustes |
| `Alt + ←` | Volver atrás (historial de navegación) |

**Panel derecho:** Backlinks (quién me enlaza), Enlaces salientes y Esquema. Tenlo siempre abierto: es tu radar.

---

## 6. Buscar como un pro

```
tag:#e14                    solo notas con esa etiqueta
path:"02 ARQUITECTURA"      solo dentro de esa carpeta
file:ERS                    solo notas cuyo nombre contiene ERS
line:(score 9)              líneas que contienen ese texto
tag:#e14 -tag:#obsoleto     combinar y excluir
```

Guarda búsquedas frecuentes con **Marcadores** (panel izquierdo) y las tendrás a un clic.

---

## 7. Notas diarias (el hábito que sostiene todo)

Están configuradas en la carpeta `07 DIARIO`. Cada día creas una nota y escribes ahí:

- Qué avanzaste hoy
- Qué decidiste (y **por qué**)
- Qué duda te quedó

Luego **enlazas** desde el diario a las notas del tema. Así, con el tiempo, el diario se convierte en la línea de tiempo del proyecto y los backlinks te muestran dónde nació cada idea.

**Rutina recomendada (5 minutos):** abre la nota de hoy → escribe 3 bullets → enlaza al menos una nota existente.

---

## 8. Plantillas

En `08 PLANTILLAS` tienes cuatro. Para usarlas:

1. `Ctrl + P` → **Insertar plantilla** → elige la plantilla
2. O copia y pega desde la carpeta

| Plantilla | Cuándo usarla |
|---|---|
| Nota diaria | Cada día de trabajo |
| Reunión | Cuando hables con alguien del proyecto |
| Decisión | Cuando elijas entre opciones (¡clave para no repetir discusiones!) |
| Idea | Cuando se te ocurra algo suelto que no sabes dónde poner |

---

## 9. El método: capturar → conectar → crear

Inspirado en **PARA** (Tiago Forte) y **Zettelkasten** (Niklas Luhmann), adaptado a este vault:

1. **Capturar** — todo lo que aprendes o decides va a una nota. Sin perfeccionismo.
2. **Procesar** — reescribe la idea **con tus palabras**. Si no puedes explicarla, no la entendiste.
3. **Conectar** — añade al menos **un enlace** a una nota existente. Una nota sin enlaces es una nota muerta.
4. **Crear** — usa las notas conectadas para producir algo: un ERS, una decisión, una pantalla, un informe.

**Regla de oro:** una nota = una idea. Si una nota habla de tres cosas, son tres notas enlazadas.

---

## 10. Plugins que valen la pena (cuando quieras subir de nivel)

Se instalan en *Ajustes → Plugins de la comunidad* (requiere conexión a internet):

| Plugin | Para qué |
|---|---|
| **Dataview** | Convertir propiedades en tablas y tableros automáticos |
| **Templater** | Plantillas con lógica (fechas, títulos automáticos) |
| **Tasks** | Gestionar pendientes con `- [ ]` en todo el vault |
| **Excalidraw** | Dibujar diagramas a mano alzada |
| **Kanban** | Tableros tipo Trello dentro de una nota |
| **Calendar** | Navegar las notas diarias |

⚠️ Los plugins de la comunidad son código de terceros: instala solo los conocidos y revisa qué permisos piden.

---

## 11. Cinco reglas de oro

1. **Enlazar > archivar.** Antes de crear una carpeta, pregúntate si basta un enlace.
2. **Escribe para tu yo del futuro.** Él no recordará el contexto de hoy.
3. **Una idea por nota.**
4. **Nada se borra sin pensar.** Lo dudoso va a `99 REVISAR - candidatos a eliminar`, no a la papelera.
5. **El vault no se organiza, se cultiva.** Dedica 5 minutos al día en vez de 5 horas una vez al año.

---

## 12. Ejercicio práctico (15 minutos)

- [ ] Abre la nota de hoy en `07 DIARIO` y escribe 3 líneas sobre este proyecto
- [ ] Crea una nota nueva llamada `Mi primera idea sobre el E-14`
- [ ] Dentro, enlaza dos notas existentes: `[[Especificación de Requerimientos (ERS)]]` y `[[Que datos contiene un Acta E-14]]`
- [ ] Añade la propiedad `tipo: idea` y la etiqueta `#idea`
- [ ] Abre el **grafo** (`Ctrl + P` → "Abrir grafo") y mira cómo aparecen tus conexiones
- [ ] Abre el panel de **Backlinks** de la nota enlazada y comprueba que apareces ahí

Cuando termines esto, ya sabes usar Obsidian mejor que el 90 % de la gente.

---

## Siguiente parada

- [[Convenciones del vault]] — las reglas de este vault en concreto
- [[Inicio - Proyecto Electoral]] — el mapa del proyecto
