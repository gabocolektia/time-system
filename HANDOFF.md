# CEO Time System — Handoff a Claude Code

## Contexto del usuario

**Gabriel Monroy** — CEO y cofundador de **Colektia** (infraestructura de IA para cobranzas en LATAM).

Trabajamos siempre en **español**. Background técnico de producto, decisiones rápidas, estilo directo.

Metodología CEO basada en **"Great CEOs Are Lazy" de Jim Schleckser**:
- Foco en el constraint principal
- Modelo 95/5 (5% operar, 95% pensar/construir sistemas)
- Sistemas sobre heroísmo
- 5 sombreros: Architect, Player, Coach, Prospector, Visionary

## Qué estamos construyendo

**CEO Time System** — Aplicación web HTML single-file para gestión semanal del tiempo del CEO basada en la metodología de Schleckser. Conecta a Airtable como backend.

**Estado actual:** v36 funcional pero no probado en producción todavía. Ya viene de un proceso de 36 iteraciones de diseño + 4 fases de arquitectura.

## Arquitectura final (Modelo B — Multi-tenant)

- **HTML single-file** servido desde GitHub Pages (URL fija, sin servidor)
- **Cada usuario tiene su propia base de Airtable** (aislamiento total — no tabla Users compartida)
- **Hats y Stages también viven en cada base** (no globales)
- **Sin polling automático** — sync manual via botón en topbar
- **Dirty-tracking** para minimizar writes a Airtable
- **Credenciales locales** en window.storage (base ID + token)

## Schema de Airtable (Gabriel — su base actual)

**Base ID:** `appGeJ4iIW365QYUl`

**Tablas:**

### Constraints (`tblt3h6upLFzCmU0j`)
- Code, Name, Description, Default Hat (link → Hats), Archived
- Activities link inverso

### Teams (`tbl9JGs6P5kMAhHDS`)
- Team ID, Name, Role, Color, Archived
- Activities link inverso

### Stages (`tbl7bhpDd5QsQoxWN`)
- Stage, Order, Description
- Activities link inverso
- Records: Ready, Building, Training, Live

### Activities (`tblc8zOA5vJGii49S`)
- Name, Constraint (link), Hat (link), Team (link), Stage (link)
- Delegated, Completed, Priority, Priority Week
- Date, Duration (min), Source, Sync Status
- Calendar Event ID, Last Synced At, Description, Archived, Log

### Weekly Rituals (`tblu2q7pWGJvT7QRz`)
- Week ID, Started At, Closed At, Current Step, Decisions

### Hats (`tbl3IGGYcX5kBQnvP`)
- Hat, Order, Summary, What You Do, What You Don't Do, Signals, Examples, When To Switch, Archived
- Activities link inverso, Constraints link inverso
- Records: Architect, Player, Coach, Prospector, Visionary

### Settings (`tblwJiiWaE7ClRNUN`)
- Key, Value
- Records: `workspace_name = "Colektia · CEO Time"`

**Field IDs completos:** ver constante `MAP` en el HTML (sección 2.1).

## Datos seed actuales en Airtable

- 15 Activities reales del CEO
- 3 Constraints (C1 Growth Acceleration, C2 BPO Profitability, C3 Financial Intelligence)
- 6 Teams (Delta/BI, UX, AI, Product, CoS, People)
- 5 Hats con descripciones completas
- 4 Stages
- 1 Setting (workspace_name)

## Estado del HTML v36

**~5590 líneas, 1 archivo, sin dependencias externas**

Estructura del JS:
1. STATE
2. AIRTABLE DATA LAYER (MAP, atFetch, mappers, dirty-tracking, save functions)
3. UTILS
4. RENDER
5. ACTIVITY EDIT MODAL
6. CONFIG RENDER
7. DATA EXPORT (local backup)
8. MODAL & TOAST
9. WIZARD (5-step weekly ritual)
10. INIT / LOGIN / LOGOUT

## El problema que estamos resolviendo AHORA

El HTML está terminado y funciona en teoría, pero no puede ejecutarse desde:
- ❌ Artefacto de Claude (sandbox bloquea fetch externo)
- ❌ Archivo `file://` (Airtable bloquea CORS desde origen file)

**Solución:** servir desde GitHub Pages para tener una URL pública y estable.

## Lo primero que necesito de Claude Code

1. **Inicializar repo Git** en una carpeta dedicada (sugerido: `~/Projects/ceo-time-system/`)
2. **Renombrar el HTML a `index.html`** (para URL corta)
3. **Crear repo en GitHub** llamado `ceo-time-system` (público, para GitHub Pages free)
4. **Hacer commit inicial + push**
5. **Activar GitHub Pages** (Settings → Pages → Source: main branch / root)
6. **Confirmarme la URL pública** (será tipo `https://USERNAME.github.io/ceo-time-system/`)

## Después de eso

Probaremos el login real en el navegador:
- Base ID: `appGeJ4iIW365QYUl`
- Token: generar uno nuevo en airtable.com/create/tokens con scopes `data.records:read` + `data.records:write`, acceso solo a la base de CEO Time System

Si carga las 15 activities, las constraints, etc — está terminado el milestone.

## Próximos hitos planeados

1. **Validar v36 funcional en GitHub Pages** ← ACÁ ESTAMOS
2. **Configurar Project en Claude.ai** para sync de Google Calendar (custom instructions con baseId + estructura)
3. **Documentar onboarding para líderes** (cómo duplican la base, generan token, etc.)
4. **Iteración funcional** — bugs que aparezcan en uso real, nuevas features

## Reglas de trabajo

- **Idioma:** español siempre
- **Tono:** directo, conciso. Decisiones rápidas con opciones cuando hay ambigüedad
- **Sombreros:** preguntar al inicio "¿en qué sombrero estoy hoy?" si aplica
- **Cambios al HTML:** mantener versionado incrementando (v37, v38...)
- **Cuando haya múltiples enfoques posibles:** presentar 2-3 opciones con pros/contras y recomendación clara, no más
- **Code quality:** priorizar legibilidad sobre cleverness. Comentarios donde aportan, no donde son ruido
- **Esta es la base que va a usarse en producción:** cada cambio importa
