# TVs del local — Guía de instalación y operación

Mini-PC con internet, 3 TVs de 55" en fila sobre el mostrador, URLs live en modo kiosco + caché offline. El dueño no toca la PC nunca: cambia precios en `/admin` desde donde sea y las TVs se actualizan solas.

## 1. Arquitectura

```
products.json (una sola fuente)
 ├── web pública
 ├── TV1 ?pantalla=1 → solo PROMO + logo marca
 ├── TV2 ?pantalla=2 → Chajá / Zingarella / Mousse / Selva Negra
 └── TV3 ?pantalla=3 → Lemon Pie / Bocado Griego / Mixta / Reina
```

* Precios: **red primero, caché después**. Con internet trae lo nuevo y lo guarda; sin internet muestra lo último guardado con leyenda "precios al HH:MM".
* App shell (HTML/CSS/fotos base): caché primero, casi no se pide a red.
* Polling cada 60 s + recarga programada de madrugada: el cambio del admin llega solo.

## 2. Código (una vez, en el proyecto)

**`sw.js`** — service worker con las dos estrategias (shell cache-first, `products.json` network-first con fallback).

Registro en cada TV y en la web:

```html
<script>
if ('serviceWorker' in navigator) navigator.serviceWorker.register('/sw.js');
</script>
```

Lectura de precios con fallback:

```js
fetch('/products.json')
  .then(r => r.json())
  .then(p => { localStorage.setItem('precios', JSON.stringify(p)); mostrar(p); })
  .catch(() => mostrar(JSON.parse(localStorage.getItem('precios'))));
```

## 3. Instalación — Windows (una sola vez, día del montaje)

### 3.1 Ordenar pantallas

1. 3 HDMI conectados, TVs en su entrada HDMI.
2. Click derecho escritorio → Configuración de pantalla → **Extender estas pantallas** (NO duplicar).
3. Arrastrar 1-2-3 en orden físico real. Anotar resolución (típico 1920×1080).
4. Posiciones: TV1 `0,0` — TV2 `1920,0` — TV3 `3840,0`.

### 3.2 Tres lanzadores (uno por TV, con perfil separado)

```
TV1:
"C:\Program Files\Google\Chrome\Application\chrome.exe" --kiosk --window-position=0,0 --window-size=1920,1080 --user-data-dir=C:\kiosco\tv1 --no-first-run --disable-session-crashed-bubble --disable-features=Translate https://elreydelchaja.com/tv.html?pantalla=1

TV2:
... --window-position=1920,0 --user-data-dir=C:\kiosco\tv2 ... ?pantalla=2

TV3:
... --window-position=3840,0 --user-data-dir=C:\kiosco\tv3 ... ?pantalla=3
```

Flags: `--kiosk` (pantalla completa sin controles), `--window-position` (a qué TV va), `--user-data-dir` (perfil separado, obligatorio para que no choquen), `--disable-session-crashed-bubble` (tras corte de luz arranca directo, sin preguntar restaurar). Salir del kiosco: `Alt+F4`.

### 3.3 Arranque automático

1. `Win+R` → `shell:startup` → copiar los 3 accesos directos ahí.
2. `Win+R` → `netplwiz` → inicio de sesión automático (sin contraseña).
3. Energía: nunca suspender, nunca apagar pantalla ni discos. Plan Alto rendimiento.
4. Windows Update con horas activas 8–22 (que no se reinicie al mediodía).
5. TVs: encendido automático / última entrada si lo tienen, temporizador de sueño OFF, cables etiquetados HDMI 1-2-3.

### 3.4 Prueba de aceptación del montaje

Reiniciar la PC sin tocar nada → las 3 TVs aparecen solas en ~30 s → cambiar un precio en `/admin` → en ≤60 s cambia en las 3 TVs.

## 4. Instalación — Linux / Ubuntu (alternativa)

Mismo concepto, script en "Aplicaciones al inicio":

```bash
#!/bin/bash
sleep 5
chromium --kiosk --window-position=0,0 https://elreydelchaja.com/tv.html?pantalla=1 --user-data-dir=/home/local/kiosco/tv1 &
chromium --kiosk --window-position=1920,0 https://elreydelchaja.com/tv.html?pantalla=2 --user-data-dir=/home/local/kiosco/tv2 &
chromium --kiosk --window-position=3840,0 https://elreydelchaja.com/tv.html?pantalla=3 --user-data-dir=/home/local/kiosco/tv3 &
```

Más login automático y suspensión/protector desactivados.

## 5. Operación diaria

* Mañana: prenden la zapatilla → aparecen las TVs. Nada más.
* Precios: se editan en `/admin` desde cualquier lado. La PC del local no se toca.
* Si algo se congela: desenchufar y enchufar. Vuelve solo.
* Si se corta internet: las TVs muestran lo último conocido. Al volver, se actualizan solas.

## 6. Por qué no archivos locales sueltos

Con `file://`, Chrome aisla cada HTML en un origen distinto: el `localStorage` del admin NO lo ven los otros archivos. Actualizar sería editar a mano o pasar pendrive por TV. Live + caché = una edición actualiza todo.
