# Priori-Zen — Guía para Claude

## ⚠️ Angel trabaja 100% desde el iPhone — nada de consola/terminal

Angel desarrolla estos proyectos **exclusivamente desde el iPhone**. **No** le
pidas correr comandos, `curl`, DevTools de Safari, ni editar `localStorage` a
mano: no le es fácil desde el teléfono. Toda configuración (claves API, tokens,
ajustes) debe hacerse **desde la UI de la app, a puros toques** — si hace falta,
agrega un campo en Ajustes/Config. Para paneles externos (Google Cloud, Vercel,
Supabase, etc.) dale **ligas directas ya pre-llenadas** y hazle el trabajo pesado
tú (buscar IDs, armar las URLs). Idioma: **español (México)**.

PWA de una sola página (`index.html`) para priorización colaborativa de tareas.

## ⚠️ REGLAS OBLIGATORIAS (no negociables, aplican a TODO cambio, sin excepción)

1. **TODO cambio va directo a `main`.** Commitea y haz `push` a `main`
   directamente — nunca ramas, nunca Pull Requests, a menos que Angel lo pida
   explícitamente en ese momento.
2. **TODO cambio se documenta en `docs/BACKLOG.md` ANTES de terminar la tarea.**
   Sin excepción — fix chico, feature grande, cambio de config, lo que sea:
   qué se hizo, en qué archivo, y por qué (causa raíz si fue un bug). Si no
   quedó en `docs/BACKLOG.md`, la tarea no está terminada.

## Reglas de trabajo

1. **Subir directo a `main`.** Sin ramas ni PRs salvo que se pida explícitamente.
2. **Documentar SIEMPRE en `docs/BACKLOG.md`:** cualquier cambio (fix, feature, bug encontrado) se agrega como entrada en `docs/BACKLOG.md` (crearlo si no existe) — causa real, qué se cambió, y cómo se verificó.
3. **Cerrar con botones** (`AskUserQuestion`) proponiendo el siguiente paso.
4. Idioma de trabajo: **español**.

## ⚠️ Vercel: SOLO plan Hobby — nada de Pro, nada que obligue a pagar (regla del owner, 10-oct-2026)

**Por qué existe:** el 10-oct-2026 Vercel pausó TODA la cuenta (`402 DEPLOYMENT_DISABLED`,
incluido Tas-K, que usa personal de tienda) por pasar el uso gratuito de Hobby: **Fast
Origin Transfer 17.96 GB / 10 GB**. La causa fueron dos proxies de audio (`api/stream.js` de
evangelioKA y NotiKA) que descargaban el MP3 completo en la función. Angel puede subir a Pro
**un mes** como salida de emergencia y **regresar a Hobby** — por eso TODO lo que se construya
debe seguir funcionando en Hobby. Si algo requiere Pro, **no se hace**: se avisa a Angel.

**Límites de Hobby que hay que respetar siempre (cuenta completa, no por proyecto):**
- **≤12 funciones serverless por proyecto** (cada `api/*.js` cuenta 1; los `_archivos` y `lib/` no). Si ya hay 12, consolidar por `?action=`.
- **≤1 cron por día** en `vercel.json`; para sub-diarios, Cron-Job.org. `maxDuration` ≤60 s.
- **≤100 despliegues al día en TODA la cuenta.** Un push = un despliegue (y otro más si es rama). Agrupar cambios en pocos commits; un cambio solo de docs/CLAUDE.md va con `[skip ci]` en el mensaje; poner la rama de la sesión en `git.deploymentEnabled:false` de `vercel.json`.
- **Transferencia: Fast Origin Transfer 10 GB/mes, Fast Data Transfer 100 GB/mes.** NUNCA reenviar audio, video, PDFs ni archivos grandes por una función (`arrayBuffer()` → `res.send`): responder **302 al archivo** en R2/Supabase Storage, o servirlo estático. Respuestas repetidas con `Cache-Control: s-maxage` para que las cachee la CDN. Los listados (episodios, tareas…) piden solo las columnas que usan (`select=`/`fields=`), nunca guiones o blobs completos.
- **Invocaciones ≈1,000,000/mes y ≈360 GB-Hrs de duración.** Nada de polling más rápido que ~60 s, y pausarlo con la pestaña oculta (`document.hidden`). Evitar funciones que esperen (sleep, bucles de espera): lo largo va a Railway/Modal/Cloud, no a Vercel.
- **No usar funciones de Pro:** protección de despliegues con contraseña, más de un miembro de equipo, crons por minuto/hora, Speed Insights/Web Analytics de pago, firewall avanzado, rolling releases, microfrontends, logs largos, etc. Antes de activar cualquier ajuste nuevo del panel de Vercel, verificar que sea Hobby.
- Si un sitio responde **402**: no es un bug de código; revisar Usage/Billing antes de tocar nada (https://vercel.com/dashboard → Usage).

## Fusionar a `main` — política del owner (02-sep-2026)

Si el entorno de la sesión impone trabajar sobre una rama de revisión
(branch-per-task, típico de Claude Code on the web/Cowork), **fusionar esa
rama a `main` en cuanto el trabajo quede listo, sin pedir confirmación cada
vez** — instrucción explícita y permanente del owner ("mergea a main ahora
y siempre, en este y todos los proyectos"). Aplica a todos los repos del
ecosistema del owner, no solo a este.

