# Trenes AMBA — Horarios en vivo

App web de una sola página con los horarios de trenes del Área Metropolitana de Buenos Aires (AMBA): líneas **Mitre**, **Sarmiento**, **Roca** y **San Martín**, con sus principales ramales. Reloj en vivo, búsqueda por estación, próximas salidas e ida y vuelta. Sin dependencias de build, sin publicidad — es un único archivo `index.html`.

## Por qué no es "tiempo real" con GPS

Investigué antes de construir esto: **no existe hoy una API pública oficial de tiempo real (GTFS-RT)** para los trenes del AMBA.

- El dataset de "Transporte Público en tiempo real" de BA Data (que incluía trenes) está **suspendido** por la Ciudad, en revisión.
- Lo que sí está disponible públicamente es el **GTFS estático** (horarios programados, paradas y recorridos) en [data.buenosaires.gob.ar](https://data.buenosaires.gob.ar/dataset/trenes-gtfs) y [datos.gob.ar](https://datos.gob.ar).
- La app oficial de Trenes Argentinos sí muestra la posición de las formaciones en algunos ramales, pero no publica ese endpoint como API abierta — scrapearlo sería frágil y probablemente violaría sus términos de uso, así que **no lo usamos acá**.

Por eso esta app calcula las **próximas salidas a partir de un modelo de frecuencias programadas** (franjas horarias pico/valle por línea, día hábil vs. fin de semana) combinado con el reloj en vivo, en vez de simular una posición GPS falsa. Cada estación muestra "sale a las HH:MM" / "llega en X min" según ese modelo, nunca una posición inventada del tren.

## Cómo conectar un feed GTFS real (opcional)

Si conseguís acceso a un feed GTFS estático o GTFS-RT oficial (por ejemplo cuando BA Data reactive su API), podés reemplazar el modelo de frecuencias de `index.html`:

1. El objeto `LINES` en el `<script>` define líneas, ramales y estaciones — reemplazá esas listas por las paradas reales del GTFS (`stops.txt`).
2. Las funciones `terminalDepartureMinutes()` y `nextDepartures()` son las que generan los horarios — podés sustituirlas por una llamada `fetch()` a tu propio backend que sirva `stop_times.txt` (o, si conseguís un feed GTFS-RT, al `TripUpdate` correspondiente) en vez de calcular con frecuencias.
3. Como el front es estático, un feed GTFS-RT normalmente requiere un pequeño proxy/backend propio (el protobuf de GTFS-RT no se puede leer directo desde el navegador por CORS y por el formato binario).

## Estaciones incluidas

Las listas de estaciones son las **paradas principales** de cada ramal, no exhaustivas — pensadas para que la cartelera sea legible. Si querés el listado completo de paradas, se puede ampliar desde `stops.txt` del GTFS oficial.

## Ícono en pantalla de inicio

El proyecto incluye `manifest.json`, `favicon-32.png`, `apple-touch-icon.png`, `icon-192.png` e `icon-512.png` para que, al usar "Agregar a pantalla de inicio" desde Chrome o Safari, se use el ícono del tren en vez del ícono genérico con la letra del dominio. Subí estos archivos junto con `index.html` al mismo repositorio (mismo nivel, sin subcarpetas) para que funcione.

## Uso

Abrí `index.html` en cualquier navegador, o publicalo con GitHub Pages (Settings → Pages → Deploy from branch → `main` / `root`).

## Licencia

MIT — usalo, modificalo y compartilo libremente.
