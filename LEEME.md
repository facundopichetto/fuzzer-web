# fuzzer.com.ar — sitio

## Estructura
La raiz es solo lo general (este LEEME, notas, scripts). **Todo lo que se sirve
vive en `public/`**: ese es el output directory del proyecto de Cloudflare Pages.

## Archivos (dentro de `public/`)
- `index.html` — la landing entera, un solo archivo autocontenido (346 KB).
  La tapa de CROTO va embebida en base64, no depende de ningun servidor de imagenes.
- `_redirects` — vacio a proposito, solo un comentario. Ver "Atajos y como se mide".
- `croto/`, `amsterdam/`, `enrio/`, `notmybestnight/`, `eva/`, `nosvanamatar/`,
  `spotify/`, `ep/`, `yt/`, `video/`, `ig/`, `qr1/` — los atajos, uno por carpeta,
  cada uno un `index.html` que redirige solo.
- `qr-show.html` — las hojas de QR para imprimir.

## Atajos y como se mide
Antes los atajos eran 302 en `_redirects`. Un 302 lo resuelve Cloudflare: el
navegador nunca carga una pagina nuestra, no corre ningun script, **no se puede
contar cuanta gente pasa de fuzzer.com.ar a Spotify**. Y ese numero es justo el
que importa para saber si la pauta sirve.

Ahora cada atajo es una pagina html:
1. carga (ahi dispara el pageview de Cloudflare Web Analytics, que Cloudflare
   inyecta solo cuando esta activado en el proyecto de Pages),
2. muestra "abriendo croto en spotify..." con el link visible,
3. a los **700 ms** hace `location.replace(...)` al destino.

Ademas queda un `<meta http-equiv="refresh" content="2; url=...">` como fallback:
si el navegador tiene el js apagado, redirige igual a los 2 segundos.

Para el usuario es practicamente igual de rapido. La diferencia es que ahora
queda medido: en Cloudflare > Web Analytics, los pageviews de `/croto`,
`/amsterdam`, etc. son los clicks a Spotify.

**Falta un paso, y lo tiene que hacer quien administra el Cloudflare:** activar
Web Analytics en el proyecto de Pages (Settings > Web Analytics > Enable). Sin
eso no se inyecta el beacon y las paginas redirigen igual pero no cuentan nada.

Los 302 viejos estan en `git show c119f30:_redirects` por si hay que volver.

## Como se publica
Manual: Workers & Pages -> el proyecto `fuzzer` -> Create new deployment ->
Upload assets -> subir el contenido de `public/`.

Mejor: conectar Pages al repo (Workers & Pages -> Create -> Pages -> Connect to
Git -> `facundopichetto/fuzzer-web`, branch `main`, sin build command, output directory
`public`).
Con eso cada push a `main` deploya solo y no hay que subir zips nunca mas.

Custom domains -> fuzzer.com.ar y www.fuzzer.com.ar.

El DNS ya esta en Cloudflare, asi que el 522 se arregla solo cuando haya algo
detras del dominio.

## Datos verificados que usa la pagina
Todos los IDs los saque en vivo, no de memoria:
- Album CROTO: `05ohAcEIfyKA0jQqSGU1H3`
- Artista: `7HnBNXzvxFQcb9de2TyYhK`
- Tracks, en orden: CROTO `0P1hQTyyVKHrjTVnA1BwT3`, AMSTERDAM `17uSbF8gsSDHOsfbwROwDb`,
  EN RIO `0lNgYYBGLuH3EpJ0MO7Bgt`, NOT MY BEST NIGHT `0J4lTAlYSRFlP5JZpW0gw5`,
  EVA `0QB5MWyRuoOBLRXve1Algt`, NOS VAN A MATAR `5FRyEtpUj1Yw5tlPqjuhtF`
  (4 de los 6 los confirma ademas el destino de los anuncios de Meta)
- Video CROTO: `xXG2ids9n90` · Canal: `@fuzzerTV` · IG: `@fu777er`

## Lo que FALTA y por que
- **Pixel de Meta**: no esta puesto porque no tengo el ID del pixel. Cuando lo tengas
  es pegar el snippet y listo. Sin esto la pauta sigue optimizando a ciegas.
- **Captura de mail**: no la puse. Un formulario necesita un endpoint (Cloudflare
  Worker, Formspree, Google Form) y esa decision es tuya. Un form que no manda a
  ningun lado es peor que no tenerlo.
- **Apple Music / Bandcamp / Deezer**: no puse los botones porque no tengo esas URLs
  confirmadas y no las voy a inventar.
- La fecha del Ciclo Cosmos esta hardcodeada. Cuando pase el 10/9 hay que editarla.
