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

### 2026-09-22 — Modo "3 rondas graduadas" para juntas regionales (backlog-global#375)
- **Contexto/pedido (Anvir, vía RecuerdaKA → backlog-global#375):** un
  artefacto para la junta de la región donde "primero revisamos dos cosas y
  luego revisamos tres cosas y luego priorizamos cinco cosas pero con
  tiempo y cada quien vea solamente su tarjeta de órdenes pero luego
  publicamos la de los 10... liga sin password, como lo del calendario".
  Interpretación de los números (2+3=5 descartados de 10 → quedan 5 para
  priorizar, confirmado con el texto literal del issue): **Ronda 1** cada
  quien descarta 2 de los 10 temas en privado; **Ronda 2** descarta 3 más
  de los 8 sobrevivientes; **Ronda 3** prioriza (ordena) los 5 finales. Entre
  cada ronda se revela el agregado (conteos + qué se descartó) para
  discutir en grupo antes de seguir — nunca el voto individual de nadie,
  igual que el modo clásico.
- **Decisión de diseño:** se extendió `priori-zen` (no Tas-K) agregando un
  **modo adicional** (`sessions/{code}.mode==='rounds'`) que reutiliza el
  mismo nodo de Firebase, `joined/`, `genCode()`, `toast()` y el algoritmo
  de promedio de rangos de `renderResults()` (reimplementado inline para
  la ronda 3 con la misma fórmula suma/conteo). **El modo clásico de una
  sola ronda no se tocó** — sigue siendo `showAdminSetup`/`showGame` tal
  cual, ahora accesible desde una pantalla nueva "¿Qué formato usamos?"
  (`showModeSelect`, `index.html:117-132`) en vez de ir directo desde Home.
- **Ambas opciones de origen de temas** (`index.html:530` en adelante,
  `showRoundsSetup`): botón "Ya tengo mis temas" (captura previa, como el
  modo clásico) o "Recolectar en vivo" — en ese caso la sesión nace en
  `status:'collecting'`, cada participante manda su lista a
  `topicSubs/{pid}` (`showRoundsGame` rama `collecting`), y el facilitador
  cierra la recolección (`PZ._closeCollecting`) que deduplica y arma
  `allTopics`/`remaining` (mínimo 6 temas o avisa con toast).
- **Ambas opciones de tiempo:** botón "Modo manual" (el facilitador cierra
  la ronda con "Cerrar ronda y revelar →" cuando quiera) o "Cronómetro
  automático" con minutos configurables — guarda `roundDeadline` en la
  sesión; el panel del facilitador corre un `setInterval` que muestra la
  cuenta regresiva y llama a `closeCurrentRound()` sola al llegar a 0
  (`index.html`, dentro de `showRoundsAdminPanel`). El cierre siempre
  revisa `status==='active'` antes de escribir, para no duplicar el cierre
  si el facilitador también le da clic manual justo cuando expira.
- **Flujo de ronda para el participante** (`showRoundsGame`): rondas 1-2
  muestran los temas restantes como tarjetas tocables — marcar exactamente
  2 (o 3) para descartar, envían a `roundVotes/{ronda}/{pid}`; ronda 3
  reusa la mecánica de arrastrar/▲▼ del modo clásico para ordenar los 5
  finales. Cada participante solo ve su propia tarjeta — el agregado
  (`roundResults/{ronda}`) solo se calcula y revela cuando el facilitador
  (o el cronómetro) cierra la ronda.
- **Archivo(s):** `index.html` (CSS: `.choice-row/.choice-btn/.discard-marked/
  .round-pill/.countdown/.elim-tag/.mini-input-row`; JS: `showModeSelect`,
  `showRoundsSetup`, `showRoundsAdminPanel`, `closeCurrentRound`,
  `showRoundsGame`, y las nuevas entradas en `window.PZ`).
- **Cómo iniciar cada modo:** Home → "Crear sesión →" → elegir "Ronda
  única" (como antes) o "3 rondas graduadas" (nuevo). En el nuevo, elegir
  origen de temas y modo de tiempo, crear, compartir el link/código (sin
  password, igual que siempre) y avanzar Ronda 1 → 2 → 3 desde el panel.
- **Cómo se verificó:** revisión manual del flujo de datos en Firebase
  (nombres de nodos consistentes entre lectura/escritura) y chequeo de
  sintaxis del `<script type="module">` completo con `node --check`
  (quitando las líneas `import`, mismo método usado en la entrada anterior
  de este backlog) — sin errores.
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
