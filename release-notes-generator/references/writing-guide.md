# Writing Guide — Stakeholder Release Notes

## Core philosophy

A release note is not a changelog. A changelog is for developers. A release note is a **business communication** that answers one question: *"What does this release mean for our users and for the business?"*

Every sentence must pass this filter: *"Would a non-technical stakeholder understand this and find it meaningful?"*

---

## Tone calibration by release type

### Feature release (e.g. v2.3.0)
- Tone: enthusiastic, forward-looking, confident
- Lead with the most impactful feature
- Connect features to user or business value when possible
- Example opener: *"Esta versión llega con mejoras importantes en la experiencia de pago y una nueva función que nuestros usuarios han solicitado durante meses."*

### Hotfix / patch (e.g. v2.2.1)
- Tone: calm, reassuring, factual
- Acknowledge the impact of what was fixed without dramatizing
- Be specific about what was broken and that it is now resolved
- Example opener: *"Esta actualización resuelve un problema que afectaba la carga de facturas para un grupo de usuarios. El acceso ya ha sido restaurado con normalidad."*

### Maintenance / performance release
- Tone: brief, factual, confidence-building
- Emphasize stability and speed without over-explaining
- Example opener: *"Esta versión no introduce nuevas funcionalidades, pero incluye mejoras de rendimiento y estabilidad que impactan positivamente la experiencia general."*

---

## Transformation rules

### Commits → stakeholder language

| Don't say | Say instead | Why |
|---|---|---|
| "Fixed NPE in InvoiceParser" | "Se corrigió un error que impedía abrir ciertas facturas" | El stack trace no le importa al stakeholder |
| "Implemented lazy loading for FlatList" | "La pantalla de historial carga más rápido, incluso con muchos registros" | Describe la experiencia, no la técnica |
| "Migrated auth to OAuth2.0" | "El inicio de sesión es ahora más seguro y compatible con más proveedores" | Beneficio > implementación |
| "Refactored payment service layer" | *(omitir — sin impacto de usuario)* | Solo confunde |
| "Added retry logic for API timeouts" | "La app se recupera mejor cuando la conexión es inestable" | El usuario entiende "conexión inestable", no "timeout" |
| "Fixed race condition in notification scheduler" | "Las notificaciones ahora llegan en el momento correcto, sin duplicados" | Describe el síntoma que sufría el usuario |
| "chore: bump firebase_auth to 4.15.3" | *(omitir)* | Invisble al usuario |
| "Enabled feature flag X for 100% rollout" | Puede incluirse si X era una feature anunciada previamente | Depende del contexto |

### Grouping related commits
Multiple commits touching the same screen or flow should become **one consolidated note**, not a list of micro-changes.

**Bad:**
- Se mejoró el padding del botón de pago
- Se ajustó el color del estado "pendiente"
- Se corrigió el alineamiento del ícono en pantalla de confirmación

**Good:**
- **Pantalla de pago**: Se refinaron detalles visuales para una experiencia más clara y coherente.

---

## Impact levels

Assign an impact level to help stakeholders know where to focus:

| Level | Criteria | Visual indicator |
|---|---|---|
| 🔴 Alto | Afecta flujos críticos (pago, login, datos), muchos usuarios, o resuelve incidente activo | `[ALTO IMPACTO]` |
| 🟡 Medio | Mejora notable pero no crítica, afecta flujos secundarios | `[MEJORA NOTABLE]` |
| ⚪ Bajo | Ajuste menor, cosmético, o de nicho | *(sin etiqueta — solo incluir si aporta valor)* |

Only label HIGH and MEDIUM items explicitly. Low-impact items should either be omitted or grouped silently.

---

## Extracting ticket references from commits

If a commit message contains a ticket ID (e.g. `JIRA-123`, `CU-abc123`, `#456`, `fixes #789`):
- Extract and store in an internal list
- Do NOT show ticket IDs in the stakeholder document
- If generating an internal/technical appendix (requested separately), include them there

---

## Module/area naming conventions

Use the **user-facing name** of the module, not the code module name.

| Code name | Stakeholder name |
|---|---|
| `auth_module`, `login_service` | Inicio de sesión / Autenticación |
| `payment_gateway`, `checkout_flow` | Proceso de pago |
| `notification_scheduler` | Notificaciones |
| `user_profile_screen` | Perfil de usuario |
| `dashboard_widget` | Pantalla principal / Inicio |
| `invoice_parser`, `ocr_service` | Gestión de facturas |
| `settings_screen` | Configuración |

When in doubt, use the name that appears in the app's navigation menu or tab bar.