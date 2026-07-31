# Priori-Zen — Backlog / Bitácora de cambios

Documento vivo del repo. **Regla obligatoria (ver `CLAUDE.md`):** todo cambio
—fix, feature, cambio de config o bug encontrado— se registra aquí **antes de
dar por terminada la tarea**: qué se hizo, en qué archivo y por qué (causa raíz
si fue un bug). Lo más reciente va arriba.

## Formato de entrada

```
### AAAA-MM-DD — Título breve
- **Qué:** descripción del cambio.
- **Archivo(s):** rutas tocadas.
- **Por qué / causa raíz:** motivo (si fue bug, la causa real).
- **Cómo se verificó:** prueba o comprobación.
```

---

### 2026-07-31 — Se crea la bitácora + reglas obligatorias en CLAUDE.md
- **Qué:** se agrega este `docs/BACKLOG.md` y un bloque de **Reglas
  Obligatorias** al inicio de `CLAUDE.md` (todo cambio va directo a `main`;
  todo cambio se documenta aquí antes de terminar).
- **Archivo(s):** `docs/BACKLOG.md` (nuevo), `CLAUDE.md`.
- **Por qué:** dejar un lugar fijo para registrar cambios y que la regla de
  documentación no dependa de la memoria de una sesión de chat.
- **Cómo se verificó:** archivo presente en `main` tras el push.
