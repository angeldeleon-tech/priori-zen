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

### 2026-09-22 — Pegar varios renglones = varias tareas de una vez
- **Qué:** pedido de Anvir: "permitir pegar algo de varios renglones y
  determinar cada renglón como una tarea a priorizar". El input de
  "Agregar tarea..." (pantalla "Configura tu sesión") gana `onpaste="PZ._pt(event)"`
  — si el texto pegado trae saltos de línea, se intercepta el pegado
  normal, se parte por renglón (`\r?\n`), se descartan líneas vacías, y
  cada línea se agrega como una tarea aparte (reusa el mismo `tasks.push`
  que ya usaba `_at()`). Un pegado de una sola línea sigue el
  comportamiento normal del input (no se intercepta). Toast confirma
  cuántas tareas se agregaron. Se agregó una nota chica debajo del input
  explicando el atajo.
- **Archivo(s):** `index.html` (`showAdminSetup()`: nueva `PZ._pt`, atributo
  `onpaste` en `#ti`, hint de texto, registro en el objeto `window.PZ`).
- **Por qué:** antes solo se podía agregar una tarea a la vez tecleando o
  pegando (un pegado con saltos de línea entraba completo como el texto de
  UNA sola tarea, con los saltos de línea incluidos) — con una lista larga
  copiada de notas/Excel/WhatsApp era muy lento cargarla tarea por tarea.
- **Cómo se verificó:** revisión manual del flujo (parseo de líneas,
  filtro de vacíos, reutilización de `tagsHTML()`/`tasks` existentes) y
  chequeo de sintaxis del `<script type="module">` con `node --check`.

### 2026-07-31 — Se crea la bitácora + reglas obligatorias en CLAUDE.md
- **Qué:** se agrega este `docs/BACKLOG.md` y un bloque de **Reglas
  Obligatorias** al inicio de `CLAUDE.md` (todo cambio va directo a `main`;
  todo cambio se documenta aquí antes de terminar).
- **Archivo(s):** `docs/BACKLOG.md` (nuevo), `CLAUDE.md`.
- **Por qué:** dejar un lugar fijo para registrar cambios y que la regla de
  documentación no dependa de la memoria de una sesión de chat.
- **Cómo se verificó:** archivo presente en `main` tras el push.
