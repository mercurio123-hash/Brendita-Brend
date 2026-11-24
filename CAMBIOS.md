# Brendita Brend — Cambios para funcionar offline

Versión corregida el 23 de noviembre de 2026, a partir del archivo
`Brendita_Brend__0_lpyw.html` que Mercurio compartió en el chat.

## Qué problema resolvía

La app original dependía de internet para abrir: cargaba la fuente
Roboto desde Google Fonts y los iconos desde Font Awesome (cdnjs).
En la calle, sin datos, eso podía dejarla en blanco o sin iconos,
igual que les pasaba a Parking Julius y a Juliu's Special 5.
Después de este cambio, la primera visita se hace con wifi y la app
queda instalada en el celular; después abre sin datos móviles.

## Archivos listos para subir

Sube estos 4 archivos a la **raíz** del repo de GitHub Pages
(crea un repo nuevo si aún no tiene uno, por ejemplo `BrenditaBrend`):

| Archivo | Qué es |
|---|---|
| `index.html` | App, modificada (ver abajo) |
| `manifest.json` | **Nuevo.** Datos para instalar la app en el celular |
| `sw.js` | **Nuevo.** Service Worker con caché offline, `brendita-brend-v1` |
| `icon-512.png` | **Nuevo.** Icono 512x512, fondo azul #3498db con marca blanca |

No subas este archivo `CAMBIOS.md` ni `README.md` si no quieres;
no son parte de la app.

## Qué cambió para funcionar offline

### 1. Sin recursos externos (`index.html`)
- Se quitó el enlace a Google Fonts (Roboto). Ahora la app usa la
  fuente del sistema del celular
  (`system-ui, -apple-system, "Segoe UI", Arial, sans-serif`),
  que se ve igual de limpia y no necesita internet.
- Se quitó el enlace a Font Awesome (cdnjs). Los iconos de
  prioridad ahora son símbolos tipográficos dentro de un círculo
  (`!`, `▲`, `i`), el botón de agregar usa `+`, editar usa `✎`
  y eliminar usa `✕`. No hay ningún archivo externo.
- Se añadió `<link rel="manifest" href="manifest.json">`,
  el meta `theme-color #3498db` y el icono.

### 2. Manifiesto para instalar (`manifest.json` + `index.html`)
- Nuevo `manifest.json`: nombre `Brendita Brend - Recordatorios`,
  nombre corto `Brendita Brend`, `start_url: ./index.html`,
  `display: standalone`, `theme_color: #3498db`, idioma `es`,
  icono `icon-512.png` (512x512, `any` y `maskable`).
- Con esto Chrome en Android ofrece "Añadir a la pantalla de
  inicio" / "Instalar app" y la app abre a pantalla completa,
  como una app normal.

### 3. Caché offline real (`sw.js`)
- Nuevo `sw.js`, caché `brendita-brend-v1`.
- En `install` guarda en caché: `./`, `index.html`,
  `manifest.json` e `icon-512.png`.
- En `fetch` usa estrategia **cache-first**: si el archivo está
  en caché, lo sirve desde ahí sin internet; si no está, intenta
  la red y guarda la respuesta (solo peticiones GET del mismo
  origen). Si no hay red ni caché y se pide la página, sirve
  `index.html`.
- En `activate` borra las cachés de versiones anteriores.

### 4. Registro del Service Worker (`index.html`)
- Se añadió un registro seguro al cargar la página:
  `navigator.serviceWorker.register('./sw.js')` dentro de
  `if ('serviceWorker' in navigator)` y con `.catch()`.
- Si el registro falla (por ejemplo, al abrir el archivo con
  `file://`), la app sigue funcionando normal; solo se pierde
  la caché offline.

## Mejoras que se agregaron (todas funcionan offline)

1. **Fecha límite y hora de recordatorio.** Cada formulario tiene
   ahora fecha y hora opcionales. Se guardan como `dueDate`
   (AAAA-MM-DD) y `dueTime` (HH:MM) en cada tarea, junto con
   `reminded` (si ya sonó el aviso). Debajo del texto de la tarea
   se muestra la fecha en español, por ejemplo
   "Vence: lun, 24 nov 2026 · 09:00". Las tareas vencidas (fecha
   y hora pasadas, sin completar) llevan la etiqueta roja
   "Vencida" y borde rojo.
2. **Alarma de recordatorio con la app abierta.** Cada 30 segundos
   (y al cargar) revisa las tareas con fecha y hora vencidas, sin
   completar y sin aviso previo. Cuando suena una, se escucha una
   alarma con Web Audio (pitidos de 880 Hz en ciclos de 3, hasta
   6 ciclos o hasta tocar "Detener alarma"), vibra si el celular
   lo permite, y aparece el aviso en pantalla con el texto de la
   tarea y su sección. Después queda marcada como avisada
   (`reminded = true`) para no repetir. Ojo: como en Parking
   Julius, esto solo suena con la app abierta; con la app cerrada
   no hay sonido, porque el navegador no puede despertar la página.
   El aviso de tareas críticas al cargar, que ya existía, se mantiene.
3. **Editar tarea.** Botón `✎` en cada tarea: el texto se convierte
   en un campo editable; se guarda con Enter o al salir del campo,
   y se cancela con Escape.
4. **Confirmación al eliminar.** Antes de borrar una tarea pregunta
   con `confirm()` mostrando su texto.
5. **Contadores por sección.** Debajo de cada título ("Pendientes
   de Liomont" / "Pendientes de Brenda") se ven: pendientes,
   críticas y vencidas.
6. **Limpiar completadas.** Botón por sección que borra solo las
   tareas completadas de esa sección, con confirmación si hay alguna.
7. **Respaldo.** Botones arriba de las secciones: "Exportar
   respaldo" descarga un archivo JSON con todas las tareas, e
   "Importar respaldo" lee un archivo JSON, verifica que tenga
   los arreglos `liomont` y `brenda`, pide confirmación y reemplaza
   las tareas actuales. Sirve para cambiar de celular sin perder
   los pendientes.
8. **Enter agrega la tarea.** Al escribir la descripción y pulsar
   Enter, se agrega sin tocar el botón.
9. **Prioridad por defecto: Media (2).** Al abrir la app y después
   de agregar una tarea, la prioridad seleccionada es Media, para
   no tener que elegirla cada vez.
10. **Orden mejorado.** Primero las incompletas por prioridad
    (crítica primero), luego por fecha y hora (la más cercana
    primero; sin fecha al final); las completadas van al final.
11. **Seguridad: sin inyección de HTML.** Las tareas ya no se
    insertan con `innerHTML`; se construyen con métodos del DOM
    y `textContent`, así que un texto con `<` o etiquetas no puede
    romper la página ni ejecutar código.

## Qué NO cambió

- Las dos secciones, sus nombres y sus colores: "Pendientes de
  Liomont" (empresa) y "Pendientes de Brenda" (personal).
- Prioridades 1 crítica (rojo), 2 media (naranja), 3 baja (verde).
- Avance 0 / 25 / 50 / 75 / 100%, barra de progreso de color,
  y al llegar a 100% la tarea se tacha y se marca completada.
- Fecha y hora en el encabezado, aviso de tareas críticas.
- Los datos se siguen guardando solo en el celular, en
  `localStorage`, con la **misma clave de siempre:
  `brenditaBrendTasks`**, con la forma
  `{ liomont: [], brenda: [] }` y tareas
  `{ id, text, priority, progress, completed }`.
- **Compatibilidad con tareas viejas:** si una tarea guardada no
  tiene `dueDate`, `dueTime` o `reminded`, se le ponen valores
  vacíos / `false` al cargar, sin borrarla. Subir estos archivos
  no borra tus tareas: están ligadas al dominio de GitHub Pages,
  no a los archivos en sí (ojo: si la app se publica en un dominio
  o repo distinto al que ya usabas, las tareas no se verán, porque
  el `localStorage` es distinto por dominio).

## Cómo subirlo a GitHub (desde la web, sin terminal)

1. Crea el repo si aún no existe (por ejemplo `BrenditaBrend`),
   o entra al repo que vayas a usar.
2. Haz clic en **Add file > Upload files**.
3. Arrastra o selecciona estos 4 archivos de la carpeta
   `offline-fix/brendita-brend/`: `index.html`, `manifest.json`,
   `sw.js`, `icon-512.png`.
4. Escribe un mensaje de commit, por ejemplo:
   `Brendita Brend offline: manifest, service worker y mejoras`
5. Pulsa **Commit changes**.
6. Espera 1 o 2 minutos a que GitHub Pages publique la nueva
   versión en `https://mercurio123-hash.github.io/BrenditaBrend/`
   (la dirección exacta depende del nombre del repo).
7. Si GitHub Pages no está activado en ese repo: **Settings >
   Pages > Branch: main, carpeta / (root) > Save**.

## Cómo probarlo en el Galaxy A17

1. **Con wifi**, abre en Chrome (Android) la dirección de GitHub
   Pages de la app. Espera a que cargue y recarga una vez, para
   que el Service Worker se instale y guarde todo en caché.
2. En el menú de Chrome (⋮) toca **"Añadir a la pantalla de
   inicio"** o **"Instalar app"**. Verás el icono azul "BB" en tu
   pantalla principal.
3. **Activa el modo avión** (sin wifi y sin datos).
4. Abre la app desde su icono en la pantalla de inicio.
   Debe abrir completa, sin error.
5. Prueba sin internet:
   - Agrega una tarea en Liomont y otra en Brenda; la prioridad
     debe empezar en Media.
   - Agrega una tarea con fecha de hoy y hora dentro de 1 minuto:
     al llegar la hora, con la app abierta, debe sonar la alarma
     y aparecer el aviso; toca "Detener alarma".
   - Agrega una tarea con fecha de ayer: debe marcarse "Vencida"
     en rojo y el contador debe contar 1 vencida.
   - Edita una tarea con el lápiz, borra otra (debe preguntar),
     cambia un avance a 100% (debe tacharse) y usa "Limpiar
     completadas".
   - Exporta un respaldo y vuelve a importarlo.
   - Cierra la app y vuelve a abrirla en modo avión: tus tareas
     deben seguir ahí.
6. Desactiva el modo avión. Todo sigue igual, solo que ahora
   ya no necesitas conexión para abrirla.

## Si ves la versión vieja

- Cierra la app por completo y vuelve a abrirla con wifi;
  el Service Worker se actualiza solo en la siguiente visita.
- Si sigue apareciendo la vieja, en Chrome mantén pulsado el
  icono de la app > **Información de la app > Almacenamiento >
  Borrar caché** (no borres los datos, solo la caché), o
  desinstala el acceso directo y vuelve a añadirlo con wifi.

## Pruebas hechas en esta corrección

- `node --check` OK para `sw.js` y para el `<script>` extraído
  de `index.html`.
- `manifest.json` es JSON válido.
- `index.html` ya no referencia ningún dominio externo
  (sin Google Fonts, sin cdnjs, sin Font Awesome, sin unpkg,
  sin `http://` ni `https://`).
- Servida la carpeta con un servidor HTTP local, los 4 archivos
  responden 200: `index.html`, `manifest.json`, `sw.js`,
  `icon-512.png`.
- `icon-512.png` es un PNG válido de 512x512.
- No se pudo probar el Service Worker en un navegador real desde
  este equipo, ni la alarma en un celular real; la prueba
  definitiva es el paso 4 de arriba, en el Galaxy A17 en modo avión.
