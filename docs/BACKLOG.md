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

### 2026-09-22 — "Crear sesión" no hacía nada (causa raíz: reglas de Firebase, + falta de manejo de error)
- **Reportado por Anvir:** "priori-zen no deja crear la nueva sesión, no hace
  nada" — el botón "Crear sesión →" no mostraba ningún error ni cambiaba de
  pantalla, simplemente no pasaba nada.
- **Causa raíz real (confirmada con Playwright contra el sitio en producción):**
  la Realtime Database de Firebase (`priori-zen-default-rtdb`) está
  **rechazando la escritura** en `/sessions/{code}` con
  `PERMISSION_DENIED: Permission denied` — son las reglas de seguridad de la
  base de datos, no un bug de la app en sí. **Pendiente que Angel actualice
  las reglas** desde la consola de Firebase (no se puede desde aquí, no hay
  acceso a la consola de Firebase vía esta sesión):
  https://console.firebase.google.com/project/priori-zen/database/priori-zen-default-rtdb/rules
  — reglas sugeridas (mismo nivel de confianza que el resto del ecosistema,
  sin auth real, solo el código de sesión como "llave"):
  ```json
  {
    "rules": {
      "sessions": {
        ".read": true,
        ".write": true
      }
    }
  }
  ```
- **Bug real de la app, corregido aparte:** NINGUNA de las 5 escrituras a
  Firebase (`PZ._create`, `_start`, `_close`, `_join`, `_submit`) tenía
  `try/catch` — cuando `set()`/`update()` fallaba (por las reglas, o
  cualquier otro motivo: sin internet, cuota excedida, etc.), la promesa
  rechazada quedaba sin manejar y la pantalla se quedaba "muerta" sin avisar
  nada al usuario — exactamente el síntoma reportado ("no hace nada"). Ahora
  las 5 envuelven la llamada en `try/catch` y muestran un toast
  `❌ ...: {mensaje}` con el error real de Firebase — así cualquier fallo
  futuro (de reglas o de cualquier otra causa) se ve en pantalla en vez de
  fallar en silencio.
- **Archivo(s):** `index.html`.
- **Cómo se verificó:** Playwright contra `https://angeldeleon-tech-priori-zen.vercel.app`
  reprodujo el `PERMISSION_DENIED` real (confirmando la causa raíz); luego,
  sirviendo el `index.html` con el fix localmente, el mismo flujo de "Crear
  sesión" ahora muestra el toast de error y se queda en la pantalla de
  configuración en vez de no hacer nada. **El fix de reglas de Firebase
  sigue pendiente** — hasta que Angel las actualice, crear sesión seguirá
  fallando, pero ahora avisando por qué en vez de verse "congelado".

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
