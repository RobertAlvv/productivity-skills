# Mode: Task Update — Template Reference

Este reference contiene las plantillas completas para el modo Task Update. Usar cuando el usuario quiere registrar progreso sobre una tarea que ya existe en ClickUp.

---

## Principios del Task Update

Un Task Update es un **registro de progreso**, no una reescritura de la tarea. Debe:

- Ser aditivo — se agrega al historial de la tarea, no lo reemplaza
- Tener fecha — cada update es un snapshot temporal
- Ser autocontenido — otro developer puede entender qué pasó sin leer updates anteriores
- Escalar con la complejidad — un bugfix trivial necesita 3 líneas, un refactor arquitectónico necesita secciones completas

---

## Template por nivel de complejidad

### Trivial (bugfix de una línea, cambio de config, typo)

```markdown
## Update — [YYYY-MM-DD]

**Qué se hizo:** [descripción en una oración]

**Archivos:** `path/to/file.ext`
```

Ejemplo:

```markdown
## Update — 2026-03-10

**Qué se hizo:** Se corrigió typo en el label del botón de login ("Inicar" → "Iniciar").

**Archivos:** `lib/features/auth/presentation/widgets/login_button.dart`
```

---

### Estándar (feature simple, bugfix con diagnóstico, refactor de un módulo)

```markdown
## Update — [YYYY-MM-DD]

### Contexto
[Qué se estaba resolviendo o implementando. 1–3 oraciones.]

### Qué se hizo
- [bullet 1 — acción concreta]
- [bullet 2 — acción concreta]
- [bullet 3 — acción concreta]

### Decisiones técnicas
- **[decisión]** — [razón tal como se discutió]

### Archivos modificados
- `path/to/file.dart` — [qué cambió en este archivo]
- `path/to/other_file.dart` — [qué cambió]

### Commits
- `hash` — `mensaje del commit`

### Pendientes
- [ ] [pendiente concreto 1]
- [ ] [pendiente concreto 2]

### Riesgos / Notas
- [riesgo o nota relevante]
```

Ejemplo:

```markdown
## Update — 2026-03-10

### Contexto
La pantalla de detalle de producto crasheaba al rotar el dispositivo en Android. El error aparecía como `StateError: Cannot emit new states after calling close`.

### Qué se hizo
- Se diagnosticó que el `ProductDetailBloc` seguía emitiendo estados después de `dispose()` porque el stream de WebSocket no se cancelaba
- Se agregó `cancelOnDispose` del package `bloc_concurrency` al Bloc
- Se escribió un unit test que verifica que no se emiten estados después de `close()`

### Decisiones técnicas
- **Usar `cancelOnDispose` en lugar de `autoDispose`** — `autoDispose` destruye el Bloc al salir del widget, pero necesitamos preservar el cache cuando el user navega entre tabs

### Archivos modificados
- `lib/features/product/bloc/product_detail_bloc.dart` — agregado `cancelOnDispose` en el constructor
- `test/features/product/bloc/product_detail_bloc_test.dart` — nuevo test `emits nothing after close`

### Commits
- `a1b2c3d` — `fix(product): cancel stream on bloc dispose to prevent StateError`

### Pendientes
- [ ] Verificar que el mismo pattern aplica al `CartBloc` (tiene un stream similar)
- [ ] Agregar golden tests para la pantalla de detalle en modo landscape

### Riesgos / Notas
- Si el backend cambia a SSE en lugar de WebSocket, el `cancelOnDispose` puede no cubrir la desconexión correctamente
```

---

### Compleja (refactor arquitectónico, feature multi-módulo, investigación técnica extensa)

```markdown
## Update — [YYYY-MM-DD]

### Contexto
[Descripción del problema o iniciativa. Incluir por qué se inició este trabajo y qué lo motivó. 3–5 oraciones.]

### Diagnóstico / Investigación
[Análisis realizado. Qué se descubrió, qué datos se revisaron, qué hipótesis se probaron.]

### Alternativas exploradas
| Alternativa | Evaluación | Resultado |
|---|---|---|
| [Opción A] | [pros/cons tal como se discutieron] | Descartada — [razón] |
| [Opción B] | [pros/cons tal como se discutieron] | **Elegida** — [razón] |
| [Opción C] | [pros/cons tal como se discutieron] | Descartada — [razón] |

### Qué se hizo
- [bullet 1 — acción concreta con contexto suficiente]
- [bullet 2 — acción concreta]
- [bullet 3 — acción concreta]
- [bullet 4 — acción concreta]

### Decisiones técnicas
- **[decisión 1]** — [razón tal como se discutió, incluyendo tradeoffs]
- **[decisión 2]** — [razón]

### Archivos modificados
**Nuevos:**
- `path/to/new_file.dart` — [propósito]

**Modificados:**
- `path/to/file.dart` — [qué cambió y por qué]

**Eliminados:**
- `path/to/old_file.dart` — [razón de eliminación]

### Commits
- `hash1` — `mensaje del commit 1`
- `hash2` — `mensaje del commit 2`

### Datos cuantitativos
- [métrica 1: ej. "El pipeline bajó de 40 min a 10 min"]
- [métrica 2: ej. "Se eliminaron 340 líneas de código duplicado"]

### Deuda técnica identificada
- [deuda 1 — qué se descubrió que necesita arreglo pero no se resolvió en esta sesión]
- [deuda 2]

### Pendientes
- [ ] [pendiente concreto 1]
- [ ] [pendiente concreto 2]
- [ ] [pendiente concreto 3]

### Riesgos
- [riesgo 1 — condición y posible impacto]
- [riesgo 2]

### Dependencias
- [depende de PR / tarea / servicio externo]
```

---

## Reglas del Task Update

1. **Siempre incluir fecha** — el update es un snapshot temporal.
2. **Usar el nivel de complejidad correcto** — no generar un template complejo para un bugfix trivial.
3. **Archivos con path completo** — nunca "el archivo del bloc", siempre `lib/features/auth/bloc/login_bloc.dart`.
4. **Pendientes como checkboxes** — ClickUp los renderiza como subtareas interactivas.
5. **Eliminar secciones vacías** — si "Riesgos" no aplica, eliminar la sección. No escribir "N/A".
6. **Cada bullet debe ser autocontenido** — otro developer debe entender el bullet sin leer la conversación.
7. **No repetir la descripción original de la tarea** — el update es nuevo progreso, no contexto heredado.