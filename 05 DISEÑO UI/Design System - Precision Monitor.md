---
proyecto: Proyecto Electoral E-14
tipo: diseno
estado: vigente
actualizado: 2026-10-13
tags: [diseno, design-system, ui, precision-monitor]
---

# 🎨 Design System — Precision Monitor

> **El sistema visual del proyecto.** Filosofía *Dark Mode First* orientada a **alta densidad de datos** para supervisión en tiempo real.
> Todo componente nuevo se construye sobre estas reglas. No hay excepciones de color.

---

## 1. Filosofía: "Precision Monitor"

El panel de supervisión es un **command center**. El operador pasa jornadas extenuantes mirando datos en vivo. La interfaz debe:

1. **Reducir la fatiga visual** con fondo oscuro y alto contraste funcional.
2. **Comunicar estado de un vistazo** con colores semánticos (no decorativos).
3. **Priorizar lo numérico** (mesas, horas, scores) con tipografía monoespaciada.
4. **Ser táctil y grande** para operadores de 25-50 años en hardware restringido.

---

## 2. Paleta de colores

### 2.1 · Base (Dark Mode First)

| Token | Hex | Uso |
|---|---|---|
| `bg-base` | **`#0E1414`** | Fondo principal del sistema |
| `bg-surface` | `#161D1D` | Tarjetas y paneles elevados |
| `bg-surface-2` | `#1E2727` | Inputs, filas alternas |
| `border-subtle` | `#2A3434` | Bordes discretos |
| `text-primary` | `#E8F0F0` | Texto principal |
| `text-secondary` | `#8A9999` | Texto de apoyo, metadata |

### 2.2 · Colores funcionales (semánticos, no decorativos)

| Token | Hex | Significado | Dónde aparece |
|---|---|---|---|
| `success` | **`#00C853`** | 🟢 ÓPTIMO / validado / completado | Score ≥8, mesa completa, envío exitoso |
| `warning` | **`#FFD600`** | 🟡 Advertencia / offline / pendiente | Score 6.5-7.9, cola offline, en proceso |
| `danger` | **`#FF5252`** | 🔴 Error crítico / rechazado / mora | Score <6.5, rescaneo, mora crítica |
| `info` | `#00B8D4` | ℹ️ Informativo / enlaces | Hora Colombia, IDs |

> ⚠️ **Regla dura:** el rojo `#FF5252` **solo** para error crítico o mora. Nunca para "decorar". El usuario debe poder mirar la pantalla y saber el estado global por el color.

### 2.3 · Estados del acta (mapeo cromático)

| Estado | Color | Hex | Origen |
|---|---|---|---|
| **Pendiente** | Gris | `#5A6B6B` | [[Estados del Acta E-14 en la PWA]] |
| **Validado / OK** | Verde | `#00C853` | Score ≥ 9 (ERS) / ≥ 8.0 (implementado) |
| **Con Advertencia** | Naranja | `#FF9100` | Score 6-8 (ERS) / 6.5-7.9 (implementado) |
| **Rechazado** | Rojo | `#FF5252` | Score ≤ 5 (ERS) / < 6.5 (implementado) |
| **Offline** | Amarillo | `#FFD600` | Cola de sincronización |

> Ver [[Estados del Acta E-14 en la PWA]] y [[Doctrina del score y evaluarGate]] §6 para la reconciliación de umbrales.

### 2.4 · Fases SLA (control de mora)

| Fase | Color | Hex |
|---|---|---|
| **Fase 1 — Ritmo normal** (0-40 min) | Verde | `#00C853` |
| **Fase 2 — Advertencia preventiva** (40-60 min) | Amarillo | `#FFD600` |
| **Fase 3 — Mora crítica** (>2 h) | Rojo | `#FF5252` |

> Ver [[Centro de Notificaciones (SLA)]] §2.

---

## 3. Tipografía

### 3.1 · Familias

| Fuente | Uso | Por qué |
|---|---|---|
| **Hanken Grotesk** | Estructura, navegación, body, botones | Legible en tamaños pequeños, geométrica, moderna |
| **JetBrains Mono** | Números, tablas, código de barras, timestamps, scores | Monoespaciada → alineación perfecta de cifras; legible en datos densos |

### 3.2 · Escala tipográfica

| Token | Tamaño | Peso | Fuente | Uso |
|---|---|---|---|---|
| `display-xl` | 32 px | 700 | Hanken Grotesk | KPIs grandes, títulos de panel |
| `display-lg` | 24 px | 700 | Hanken Grotesk | Títulos de sección |
| `heading` | 18 px | 600 | Hanken Grotesk | Encabezados de tarjeta |
| `body` | 14 px | 400 | Hanken Grotesk | Texto base |
| `caption` | 12 px | 400 | Hanken Grotesk | Metadata, captions |
| `mono-data` | 14-16 px | 500 | JetBrains Mono | Números, scores, horas |
| `mono-code` | 13 px | 400 | JetBrains Mono | Código de barras, IDs |

### 3.3 · Reglas numéricas

- **Todos los números operativos van en JetBrains Mono** (scores, horas, conteos de mesas, IDs de sesión, códigos de barras).
- **Alineación tabular** (`font-variant-numeric: tabular-nums`) en tablas para que las cifras cuadren.
- **El score siempre con 1 decimal** (`8.0`, `7.5`, `4.3`) — ver [[Doctrina del score y evaluarGate]] §4.

---

## 4. Componentes clave

### 4.1 · KPIs superiores (dashboard)
- Tarjeta cuadrada con borde sutil.
- Número grande (`display-xl` en JetBrains Mono) + etiqueta (`caption`).
- Color del número = estado (verde/amarillo/rojo).

### 4.2 · Matriz de monitoreo (tabla principal)
- Fondo `bg-surface`, filas alternas `bg-surface-2`.
- Hora dual: `HORA LOCAL` (país) vs `HORA COL` — ambas en JetBrains Mono.
- Barra de avance por fase: Delegados y Transmisión separadas.
- Columna **Acciones**: iconos ojo (visualizar) + campana (notificar).

### 4.3 · Estados del acta (PWA)
- Píldora/badge con color de estado + ícono + texto.
- Ej: 🟢 ÓPTIMA 9.2, 🟡 ADVERTENCIA 7.2, 🔴 RECHAZADA 4.8 (bandas ≥8.0 / 6.5–7.9 / <6.5).
- El score siempre visible en JetBrains Mono.

### 4.4 · Botones
| Tipo | Estilo | Cuándo |
|---|---|---|
| **Primario** | Fondo `success` + texto negro | Acción principal ("SEGUIR ESCANEANDO", "CONFIRMAR") |
| **Advertencia** | Fondo `warning` + texto negro | Acción con riesgo ("ENVIAR CON ADVERTENCIA") |
| **Peligro** | Fondo `danger` + texto blanco | Bloqueo obligatorio ("REPETIR FOTO") |
| **Fantasma** | Borde + transparente | Secundario, navegación |

> **Touch-friendly:** mínimo 44 px de altura en todos los interactivos (ver [[Perfil operativo y arquitectura del sistema]]).

---

## 5. Iconografía

- **Lucide** como librería base (lineal, consistente, ligera).
- Tamaño estándar: 20 px en UI, 24 px en navegación.
- El color del ícono hereda el color del texto o del estado.

---

## 6. Reglas de layout

### 6.1 · Panel de supervisión (desktop)
- **Sidebar fija** a la izquierda (navegación modular + badge de notificaciones).
- **Top bar** con doble reloj (Hora Colombia vs Hora Local del país).
- **Grid principal** de tarjetas KPI + tabla de monitoreo con fila expandible.

### 6.2 · PWA Digitalizador (móvil)
- **Top bar** con título + interruptor MODO MANUAL + flash.
- **Viewport central** con marco de alineación (esquinas verdes).
- **Bottom nav** de 3: ESCANEAR · ACTAS · RESUMEN.
- **Safe areas iOS:** `viewport-fit=cover` + `dvh` + `pt-safe`/`pb-safe` (ver [[Trampas de iOS y WebKit]] §2).

---

## 7. Estados de captura (PWA)

Las pantallas de revisión usan **paneles de color semántico**:

| Pantalla | Panel | Acción principal |
|---|---|---|
| **Envío automático** (≥9/10) | 🟢 Verde: "VALIDADO Y ENVIADO" | "SEGUIR ESCANEANDO" |
| **Con advertencia** (8/10) | 🟡 Naranja: "ADVERTENCIA: ESQUINAS RECORTADAS" | "REPETIR FOTO (RECOMENDADO)" + "ENVIAR CON ADVERTENCIA" |
| **Rechazada** (5/10) | 🔴 Rojo: "ERROR CRÍTICO: ILEGIBLE" | "OBLIGATORIO REPETIR FOTO" (única acción) |

> Detalle pantalla por pantalla en [[Interfaz Gráfica (Digitalizador)]].

---

## 8. Accesibilidad

- **Contraste mínimo AA** (4.5:1 texto normal, 3:1 texto grande) en todos los pares.
- `#E8F0F0` sobre `#0E1414` → ratio ~16:1 ✅
- `#00C853` sobre `#0E1414` → ratio ~9:1 ✅
- `#FFD600` sobre `#0E1414` → ratio ~13:1 ✅
- `#FF5252` sobre `#0E1414` → ratio ~5:1 ✅
- **Focus visible** en todos los interactivos (anillo `success` 2px).
- **Teclado:** todos los flujos navegables con Tab + Enter.
- **`aria-label`** en iconos-botón (ver el caso de `<div role="button" tabIndex>` en [[Trampas de iOS y WebKit]] §3).

---

## 9. Recursos visuales (en `05 DISEÑO UI`)

| Recurso | Qué es | Estado |
|---|---|---|
| `Boceto - Plantilla guía E-14.jpg` | Boceto del formulario para guía de encuadre | ⚠️ No en este vault (ver [[Backlog y decisiones pendientes]] C-6) |
| `Digitalizador/` | Capturas de cada pantalla (`code.html` + `screen.png`) | ⚠️ No en este vault |
| `Supervisor/` | Capturas del panel (`code.html` + `screen.png`) | ⚠️ No en este vault |

> Las 8 referencias a `[[05 DISEÑO UI/Digitalizador/...]]` en [[Interfaz Gráfica (Digitalizador)]] aparecen como enlaces rotos porque las capturas no se incluyeron en este vault. **Acción pendiente:** re-incorporarlas o declarar la sección como referencia externa.

---

## 10. Evolución visual (3 generaciones)

| Generación | Nombre | Estado |
|---|---|---|
| 1.0 | **Precision Monitor** | Base conceptual (este documento) |
| 2.0 | cyber-neón | ❌ Prohibido por Stitch (`grep = 0`) |
| 3.0 | **Stitch** (vigente) | 🟡 Pendiente de rebase sobre `main` con auditoría E2E |

> Stitch **prohíbe** los sistemas visuales anteriores. Hay que "re-vestir" lo no cubierto por Stitch. Ver [[Contradicciones detectadas]] §🆕 y [[Backlog y decisiones pendientes]] B-4.

---

**Ver también:** [[Especificación de Requerimientos (ERS)]] RNF-04 · [[Perfil operativo y arquitectura del sistema]] · [[Interfaz Gráfica (Digitalizador)]] · [[Panel de Control y Supervisión Global]] · [[Trampas de iOS y WebKit]]
