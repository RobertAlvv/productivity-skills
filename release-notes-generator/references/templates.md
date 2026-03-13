# Release Notes Templates

## Template A — Standard feature/mixed release (default)

```markdown
# Release Notes — {APP_NAME}
**Versión:** {TAG}  
**Fecha de lanzamiento:** {DATE}  
**Plataforma:** Android

---

## Resumen

{EXECUTIVE_SUMMARY — 2 to 4 sentences. Describe the "theme" of this release: what was the team focused on? What is the most important thing this release delivers? Connect it to user or business value. This is the only part many stakeholders will read.}

---

## ✨ Nuevas funcionalidades

### {Module or feature area}
- **{Feature name}**: {Description focused on user benefit. What can they do now that they couldn't before?} {[ALTO IMPACTO] if applicable}

### {Module or feature area}
- **{Feature name}**: {Description}

---

## 🚀 Mejoras

- **{Area o pantalla}**: {What got better for the user. Focus on experience, not implementation.}
- **{Area o pantalla}**: {Description}

---

## 🐛 Correcciones

- **{What was broken, from user's perspective}**: {How it was resolved and what the user can now do normally.} {[ALTO IMPACTO] if this was a critical bug}

---

## 🗺 Flujos visuales

> Incluir esta sección SOLO si hay módulos con nuevas implementaciones o flujos modificados (ver Step 5.5).  
> Omitir completamente si no aplica — no dejar sección vacía.

### {Nombre del módulo — ej. "Proceso de pago"}

```mermaid
flowchart TD
    %% 🟢 Verde: nuevo en esta versión  |  🟡 Amarillo: flujo modificado
    A([{Punto de entrada}]) --> B[{Pantalla 1}]
    B --> C{¿{Condición}?}
    C -->|Sí| D[{Pantalla 2}]
    C -->|No| E[{Pantalla alternativa}]
    D --> F([{Resultado final}])
```

*{1–2 oraciones describiendo qué cambió en este flujo respecto a la versión anterior, en lenguaje no técnico.}*

---

## ⚠️ Consideraciones de esta versión

{Include this section ONLY if any of the following apply. Remove entirely if none apply.}

- **Actualización requerida**: {If users must update to continue using a feature}
- **Cambios en el comportamiento**: {If something works differently than before, even if improved}
- **Problemas conocidos**: {Be transparent. Example: "Estamos al tanto de una lentitud ocasional en la pantalla de reportes bajo conexiones lentas. Lo resolveremos en la próxima versión."}

---

*Preparado por el equipo de desarrollo el {DATE}.*  
*Para consultas sobre esta versión, contacta a {CONTACT — or remove this line}.*
```

---

## Template B — Hotfix / patch release

```markdown
# Actualización de Urgencia — {APP_NAME} {TAG}
**Fecha:** {DATE}  
**Plataforma:** Android

---

## Qué resolvimos

{2-3 sentences. Describe the problem that affected users and that it has now been resolved. Be reassuring. Example: "Esta actualización resuelve un problema que impedía a algunos usuarios completar el proceso de pago. El flujo ya funciona con normalidad para todos los usuarios."}

## Correcciones incluidas

- **{User-facing description of the bug}**: {Brief description of what's fixed.} {[ALTO IMPACTO] if applicable}

## Lo que no cambia

Esta versión no introduce nuevas funcionalidades ni modifica flujos existentes. Su único propósito es restablecer el comportamiento correcto de la app.

---

*Actualización preparada el {DATE}.*
```

---

## Template C — Maintenance / performance release

```markdown
# Mejoras de Rendimiento — {APP_NAME} {TAG}
**Fecha:** {DATE}  
**Plataforma:** Android

---

## Resumen

Esta versión no introduce nuevas funcionalidades. Está enfocada en mejorar la estabilidad, velocidad, y confiabilidad general de la aplicación.

## Mejoras incluidas

- **{Area}**: {User-perceivable improvement. E.g., "La app responde más rápido al navegar entre pantallas."}
- **{Area}**: {Description}

---

*Preparado por el equipo de desarrollo el {DATE}.*
```

---

## Choosing the right template

| Condition | Use template |
|---|---|
| Release has new features (with or without fixes) | A — Standard |
| Release has ONLY 1–3 bug fixes, no features | B — Hotfix |
| Release is purely performance/infra, no user-visible features | C — Maintenance |
| Patch version (x.y.Z) with only 1 critical fix | B — Hotfix |
| Minor version bump (x.Y.0) with features | A — Standard |
| Major version bump (X.0.0) | A — Standard (consider a longer executive summary) |