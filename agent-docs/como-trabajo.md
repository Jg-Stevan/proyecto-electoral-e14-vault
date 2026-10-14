# Cómo trabajo — Guía para agentes

> Léelo completo antes de proponer o ejecutar cualquier cosa.
> Si algo no está claro, pregunta. No asumas.
> (Versión 1.1 — base DeepSeek, ajustada con el flujo real validado en web-scanner.)

## 1. Quién soy

- Trabajo **solo** en este proyecto. Yo lidero.
- Dedicación: **full time**.
- Nivel técnico: **intermedio**.
- Idioma: **español (Colombia)**.
- Trabajo con **tres roles de agente** (ver `flujo-agentes.md`):
  - **Planificador** (modelo inteligente): piensa conmigo, genera el plan `.md`, no toca repos.
  - **Implementador** (agente de código): ejecuta el plan, abre PR con gates.
  - **Auditor** (orquestador): revisa el diff del PR antes de que yo gaste tiempo en la revisión física.

## 2. Entorno

- Editor: **VS Code**. Sistema operativo: **Windows**.
- Todo se maneja **en línea, por GitHub**. No uso git por terminal ni nada local.
- El agente **NO toca mi computador**. Solo GitHub.
- **Vercel** está conectado al repo de código: cada PR trae preview automática.
- Ojo: el **vault** (este repo) es documentación — los PR del vault **no tienen preview**; se auditan leyendo el diff en GitHub.

## 3. Cómo empiezo una tarea

1. Parte de una **idea suelta** mía.
2. El Planificador me ayuda a convertirla en plan (`TASK-###-slug.md`).
3. El plan queda en `tasks/backlog/` (o me lo entrego como `.md` descargable).
4. Lo paso al Implementador.
5. El Implementador ejecuta, corre gates, abre **PR draft**.
6. El **Auditor** revisa el diff: whitelist, inmutables, gates, veredicto escrito.
7. Yo reviso el resultado (PR + preview si es código; protocolo físico si toca la PWA).
8. Apruebo → Implementador mergea → tarea a `tasks/done/`.

## 4. Cómo quiero que trabajen

- **Primero el plan, después la ejecución.** Nunca al revés.
- Prefiero **bloques grandes**, no pasos diminutos.
- **No me expliques lo obvio.** Ve al grano, pero explica con claridad.
- Respuestas **en español, simples, no tan cortas**, sin relleno.
- Si algo no cuadra, avísame **antes** de seguir.
- No reinterpretes la tarea. Si el `.md` dice X, se hace X.
- En código: **el lab es la fuente de verdad** (regla #0 de `09 INGENIERÍA Y AGENTES/`) — lo que funciona en `scanner-lab` se copia literal con comentario de origen; los agentes no reinventan.

## 5. Cómo audito

- Reviso **solo el resultado**, no paso a paso.
- Miro el **PR** en GitHub y la **preview de Vercel** (código) o el diff (vault).
- En tareas de la PWA, la prueba es **física y con telemetría** (diag del dispositivo + gates de la tarea), no solo la preview.
- Antes de mí, el **Auditor** ya validó whitelist/inmutables/gates — yo no repito esa parte.
- Solo después de mi aprobación se hace merge.

## 6. Reglas no negociables

- ❌ El **Planificador no toca el repo**. Solo escribe `.md`.
- ❌ El **Implementador no mergea** sin mi aprobación explícita en el PR.
- ❌ **No crear carpetas nuevas** sin autorización. Si hace falta una, se propone en el PR y yo apruebo ahí.
- ❌ **No reorganizar** el vault (`00`–`09`) sin proponerlo antes.
- ❌ **No tocar temas electorales con opinión.** El sistema electoral se respeta como es.
- ❌ **No inventar requisitos.** Si falta contexto, preguntar.
- ✅ El repo es **público**. Cuida igual: nada de datos personales, credenciales ni contenido real de actas identificable.
- ✅ Todo cambio entra por **PR antes de tocar `main`**.
- ✅ Cada tarea debe **dejar registro** para futuros agentes (`worklog.md` + estado de la tarea).

## 7. Tecnología y dependencias

- Soy **abierto** a cambiar tecnologías o dependencias si funciona y es viable.
- No tengo preferencias cerradas. Si algo mejora el resultado, se propone.
- No conozco CI. Si hace falta, explicarlo antes de proponerlo.

## 8. Cómo quiero que cierren una tarea

Al terminar, el Implementador entrega:

1. **Resumen** de lo que hizo.
2. **Checklist de cómo probarlo yo mismo** (protocolo físico/telemetría si aplica).
3. **Link de la preview de Vercel** (si la tarea es de código; si es del vault, no aplica).
4. **Archivos tocados** (diff exacto contra la whitelist de la tarea).
5. **Próximos pasos** o cosas pendientes.
6. **Actualización del diario/cerebro digital**, avisándome antes.

## 9. Cerebro digital (vault)

- El vault se actualiza con decisiones y cambios del proyecto.
- **Antes de actualizarlo, me avisa.** Si apruebo, se hace (por PR, como todo).
- Esto sirve para no repetir contexto a futuros agentes.

## 10. Continuidad

- Si la ventana de contexto se reinicia, se cierra la sesión o cambia el agente, se debe poder **continuar sin problemas**.
- Ver `continuidad.md` para el protocolo.
- Toda tarea en curso debe estar documentada en `tasks/` y registrada en `worklog.md`.

## 11. Qué me molesta

- Que un agente no cumpla la tarea por falta de contexto (y reconozco que a veces es culpa mía por no explicar bien).
- Respuestas largas sin acción.
- Cambios fuera de alcance.
- «Ya está» sin evidencia.
- Que se asuma algo sin preguntar.

## 12. Qué valoro

- Precisión sobre velocidad.
- Que me avisen cuando algo no cuadra.
- Trazabilidad: qué se hizo, dónde y por qué.
- Que se respete el contexto electoral.
- Que el trabajo quede documentado para el siguiente agente.

## 13. Frases útiles para el agente

- **Antes de empezar:** «Voy a leer `AGENTS.md`, `como-trabajo.md` y `flujo-agentes.md` antes de proponer nada.»
- **Al proponer:** «Este es el plan en N pasos. ¿Apruebas o ajusto?»
- **Al dudar:** «No tengo contexto suficiente para X. ¿Me lo aclaras?»
- **Al cerrar:** «Resumen, checklist, archivos tocados y pendientes listos.»
- **Al reiniciar contexto:** «Reviso `continuidad.md`, `tasks/` y `worklog.md` para retomar.»
