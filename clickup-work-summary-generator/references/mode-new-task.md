# Mode: New Task — Template Reference

Este reference contiene las plantillas completas para el modo New Task. Usar cuando el usuario quiere crear una tarea nueva en ClickUp basada en lo que se discutió en la sesión técnica.

---

## Principios del New Task

Una New Task es una **definición de trabajo**, no un resumen de sesión. Debe:

- Ser ejecutable — otro developer puede tomarla y empezar a trabajar sin preguntar nada
- Tener criterios de aceptación — define cuándo la tarea está "terminada"
- Incluir contexto suficiente — el "por qué" es tan importante como el "qué"
- Escalar con la complejidad — una tarea de config necesita 5 líneas, una tarea de feature arquitectónica necesita secciones completas con scope técnico

---

## Template por nivel de complejidad

### Trivial (tarea de configuración, cleanup, fix puntual)

```markdown
## [Título descriptivo de la tarea]

**Objetivo:** [qué debe lograrse, en una oración]

**Scope:**
- [acción concreta 1]
- [acción concreta 2]

**Criterios de aceptación:**
- [ ] [criterio verificable]
```

Ejemplo:

```markdown
## Actualizar versión de Flutter SDK a 3.24.0 en CI

**Objetivo:** Actualizar la versión de Flutter en el workflow de CI para habilitar native test sharding.

**Scope:**
- Cambiar `flutter-version` de `3.19.0` a `3.24.0` en `.github/workflows/ci.yml`
- Verificar que todos los jobs pasen con la nueva versión

**Criterios de aceptación:**
- [ ] CI pipeline pasa en verde con Flutter 3.24.0
- [ ] No hay regresiones en los 4 test shards
```

---

### Estándar (feature, bugfix planificado, refactor de un módulo)

```markdown
## [Título descriptivo de la tarea]

### Contexto
[Por qué se necesita esta tarea. Qué problema resuelve o qué valor agrega. 2–4 oraciones. Incluir background técnico relevante.]

### Objetivo
[Qué debe lograrse cuando esta tarea esté completa. Resultado esperado claro.]

### Scope técnico
- [cambio o implementación 1]
- [cambio o implementación 2]
- [cambio o implementación 3]

### Archivos involucrados
- `path/to/file.dart` — [qué necesita cambiar]
- `path/to/other_file.dart` — [qué necesita cambiar]

### Criterios de aceptación
- [ ] [criterio verificable 1]
- [ ] [criterio verificable 2]
- [ ] [criterio verificable 3]
- [ ] Tests unitarios cubren el cambio
- [ ] No hay regresiones en tests existentes

### Notas técnicas
- [contexto técnico relevante para quien tome la tarea]
- [referencia a decisión técnica tomada en la sesión, si aplica]

### Dependencias
- [depende de tarea/PR/servicio — si aplica]
```

Ejemplo:

```markdown
## Implementar cancelación de stream en ProductDetailBloc al hacer dispose

### Contexto
La pantalla de detalle de producto crashea al rotar el dispositivo en Android. El error `StateError: Cannot emit new states after calling close` indica que el Bloc sigue recibiendo eventos del WebSocket después de `dispose()`. Esto afecta a todos los usuarios Android y ocurre consistentemente en landscape → portrait rotation.

### Objetivo
El `ProductDetailBloc` debe cancelar su suscripción al WebSocket stream cuando se cierre, sin romper el cache de datos que se usa al navegar entre tabs.

### Scope técnico
- Agregar `cancelOnDispose` del package `bloc_concurrency` al constructor del Bloc
- Escribir unit test que verifica que no se emiten estados después de `close()`
- Verificar que el cache de producto sigue funcionando al navegar entre tabs

### Archivos involucrados
- `lib/features/product/bloc/product_detail_bloc.dart` — agregar `cancelOnDispose`
- `test/features/product/bloc/product_detail_bloc_test.dart` — nuevo test de dispose

### Criterios de aceptación
- [ ] No se produce `StateError` al rotar el dispositivo en la pantalla de detalle
- [ ] El cache de producto persiste al navegar entre tabs
- [ ] Unit test verifica que no hay emisiones después de `close()`
- [ ] Smoke test manual en Android confirma el fix

### Notas técnicas
- Se eligió `cancelOnDispose` en lugar de `autoDispose` porque `autoDispose` destruiría el Bloc al salir del widget, perdiendo el cache
- Verificar si el mismo pattern aplica al `CartBloc` que tiene un stream similar
```

---

### Compleja (feature arquitectónica, epics, investigación con implementación)

```markdown
## [Título descriptivo de la tarea]

### Contexto
[Background completo del problema o iniciativa. Incluir: motivación, estado actual del sistema, por qué el approach actual es insuficiente. 4–6 oraciones.]

### Objetivo
[Resultado esperado al completar la tarea. Debe ser medible o verificable.]

### Alcance
**Incluye:**
- [concepto o componente 1]
- [concepto o componente 2]
- [concepto o componente 3]

**No incluye (fuera de scope):**
- [exclusión explícita 1 — para evitar scope creep]
- [exclusión explícita 2]

### Diseño técnico
[Descripción del approach técnico acordado. Incluir la decisión y su razón. Si hubo alternativas descartadas, mencionarlas brevemente.]

### Scope técnico
- [cambio o implementación 1 — con suficiente detalle para ejecutar]
- [cambio o implementación 2]
- [cambio o implementación 3]
- [cambio o implementación 4]

### Archivos involucrados
**Nuevos:**
- `path/to/new_file.dart` — [propósito del archivo]

**Modificados:**
- `path/to/existing_file.dart` — [qué necesita cambiar]

**Potencialmente eliminados:**
- `path/to/old_file.dart` — [razón de eliminación]

### Criterios de aceptación
- [ ] [criterio verificable 1]
- [ ] [criterio verificable 2]
- [ ] [criterio verificable 3]
- [ ] [criterio de testing]
- [ ] [criterio de performance, si aplica]

### Riesgos identificados
- **[riesgo 1]** — [posible impacto y mitigación sugerida]
- **[riesgo 2]** — [posible impacto]

### Deuda técnica relacionada
- [deuda existente que esta tarea descubrió o amplifica]

### Dependencias
- [depende de tarea/PR/servicio]
- [bloquea a tarea/feature]

### Sub-tareas sugeridas
Si la tarea es lo suficientemente grande, proponer una descomposición:
- [ ] Sub-tarea 1: [descripción]
- [ ] Sub-tarea 2: [descripción]
- [ ] Sub-tarea 3: [descripción]
```

---

## Reglas del New Task

1. **Título accionable** — el título debe describir el trabajo, no el problema. "Implementar cancelación de stream en ProductDetailBloc" > "El Bloc crashea".
2. **Contexto con "por qué"** — otro developer necesita entender la motivación, no solo los pasos.
3. **Criterios de aceptación verificables** — cada criterio debe poder verificarse con un sí/no. "El Bloc funciona bien" no es verificable. "No se produce StateError al rotar" sí lo es.
4. **Scope explícito** — en tareas complejas, definir qué está **fuera** de scope es tan importante como definir qué está **dentro**.
5. **Archivos con path completo** — otro developer debe poder encontrar los archivos sin buscar.
6. **No incluir implementación detallada** — la tarea describe el "qué" y el "por qué", no el "cómo" paso a paso. El developer que la tome decide el approach exacto.
7. **Eliminar secciones vacías** — si "Riesgos" no aplica, eliminar la sección completa.
8. **Un tema por tarea** — si la sesión cubrió 2+ temas, crear una tarea separada para cada uno.