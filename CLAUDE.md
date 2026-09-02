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

## Fusionar a `main` — política del owner (02-sep-2026)

Si el entorno de la sesión impone trabajar sobre una rama de revisión
(branch-per-task, típico de Claude Code on the web/Cowork), **fusionar esa
rama a `main` en cuanto el trabajo quede listo, sin pedir confirmación cada
vez** — instrucción explícita y permanente del owner ("mergea a main ahora
y siempre, en este y todos los proyectos"). Aplica a todos los repos del
ecosistema del owner, no solo a este.

