# TRACKEADOR_HABITOS_V4

Dashboard sci-fi de hábitos en HTML autocontenido. V4 trae el rediseño de tareas: log inmutable, auto-archivado con DESHACER y panel de archivadas.

## Lo nuevo en V4

| Cambio | Detalle |
|---|---|
| **Auto-archivado al completar** | Marcas el check → la tarea hace fade-out y se va sola al panel **ARCHIVADAS**. Lista activa siempre limpia. |
| **Toast con DESHACER (5s)** | Apenas completas, sale un toast con barra de progreso y botón **DESHACER**. Si lo presionas, la tarea vuelve a activas y el evento se borra del log. |
| **Panel ARCHIVADAS colapsable** | Lista de tareas completadas con hora ("HOY 14:32", "AYER 09:15"). Cada una con botón **↺ reabrir**. |
| **Botón VACIAR ARCHIVADAS** | Limpia el panel sin tocar las métricas — el log queda intacto. |
| **Log inmutable de eventos** | `state.taskLog` guarda cada completado como un evento independiente. **Borrar una tarea o vaciar archivadas NO mueve los números.** |
| **Reabrir tarea (↺)** | Saca de archivadas, vuelve a activas, y **elimina** el evento del log (cuenta como "no realizada"). Distinto de VACIAR. |

## Modelo de datos

```
state.tasks         → [{id, text, createdAt}]                       — sólo activas
state.archivedTasks → [{id, text, createdAt, completedAt}]          — completadas visibles
state.taskLog       → [{eventId, taskId, text, completedAt}]        — log inmutable
```

**Regla de oro:** las métricas (contador semanal + gráfico de líneas) leen sólo de `taskLog`. Las acciones de UI (borrar, vaciar archivadas) no tocan el log.

## Acciones y su efecto en las métricas

| Acción | tasks | archivedTasks | taskLog | Métricas |
|---|---|---|---|---|
| Crear tarea (+ AÑADIR) | +1 | — | — | No mueve |
| Completar (check ✓) | −1 | +1 | +1 evento | +1 realizada |
| DESHACER (≤5s) | +1 | −1 | −1 evento | −1 realizada |
| Reabrir (↺) | +1 | −1 | −1 evento | −1 realizada |
| Eliminar pendiente (X) | −1 | — | — | No mueve |
| Vaciar archivadas | — | =0 | — | No mueve |

## Vistas (sin cambios respecto a V3)

| Vista | Qué muestra |
|---|---|
| Resumen | 4 KPIs + tabla semanal de hábitos |
| Hábitos | CRUD de hábitos con categorías |
| Estadísticas | Por hábito + selector SEMANAL/MENSUAL/ANUAL |
| Calendario | Vista mensual con % cumplimiento |
| **Tareas** | Contador semanal + gráfico líneas + ACTIVAS + ARCHIVADAS |
| Ajustes | Clima (Requínoa), frase, exportar/importar, reset |

## Compatibilidad

- Storage key independiente (`habitos-explosivos-v4`). Convive con V1/V3 sin romper nada.
- **Importar JSON de V3:** `migrateTasksV3()` corre automático. Las tareas con `done=true` migran a `archivedTasks` y generan eventos en `taskLog`.

## Tech

- HTML/CSS/JS vanilla. Sin frameworks ni build step.
- Persistencia: `localStorage` clave `habitos-explosivos-v4`.
- Clima: Open-Meteo API (Requínoa, lat -34.2833, lon -70.7333).
- Chart: SVG nativo. Sin librerías.

---

Marcelo Medina · 2026
