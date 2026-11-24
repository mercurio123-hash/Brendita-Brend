# Brendita Brend — versión offline

App de recordatorios en dos secciones: **Pendientes de Liomont**
(empresa) y **Pendientes de Brenda** (personal). Funciona instalada
en el celular, sin wifi ni datos móviles, después de abrirla una
vez con wifi.

## Archivos

- `index.html` — la app (sin recursos externos)
- `manifest.json` — datos para instalarla en Android
- `sw.js` — Service Worker con caché offline (`brendita-brend-v1`)
- `icon-512.png` — icono 512x512
- `CAMBIOS.md` — qué cambió, cómo subirla y cómo probarla

## Para subirla a GitHub

Sube `index.html`, `manifest.json`, `sw.js` e `icon-512.png` a la
raíz del repo y activa GitHub Pages (Settings > Pages, rama main,
carpeta raíz). Detalles y prueba en modo avión en `CAMBIOS.md`.

## Mejoras incluidas

Fecha y hora de recordatorio con alarma sonora (con la app abierta),
tareas vencidas en rojo, editar tarea, confirmación al eliminar,
contadores por sección, limpiar completadas, exportar e importar
respaldo en JSON, Enter para agregar, prioridad por defecto Media,
orden por prioridad y fecha, y tareas construidas sin `innerHTML`
para que el texto no pueda inyectar código.

Las tareas se guardan en `localStorage` con la clave
`brenditaBrendTasks`, igual que la versión original, y las tareas
viejas sin los campos nuevos se conservan.
