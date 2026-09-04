# fuzzer.com.ar — sitio

## Archivos
- `index.html` — la landing entera, un solo archivo autocontenido (346 KB).
  La tapa de CROTO va embebida en base64, no depende de ningun servidor de imagenes.
- `_redirects` — atajos para Cloudflare Pages. Da un 302 real, sin pantalla intermedia.
- `croto/`, `amsterdam/`, `enrio/`, `notmybestnight/`, `eva/`, `nosvanamatar/`,
  `spotify/`, `ep/`, `yt/`, `video/`, `ig/` — el mismo atajo pero como pagina HTML,
  para hosting estatico que NO lee `_redirects` (GitHub Pages, por ejemplo).
  Cada una hace meta refresh + location.replace + deja un link visible por si falla.

**Al pasar a Cloudflare** se pueden borrar esas 11 carpetas y queda solo `_redirects`,
que es mas prolijo porque redirige a nivel servidor. Si se dejan, tampoco rompe nada.

## Como se publica
1. Entrar al Cloudflare de fuzzer.com.ar (falta el acceso, se lo pedi a Santi).
2. Workers & Pages -> Create -> Pages -> Upload assets.
3. Subir esta carpeta entera. Cloudflare lee `_redirects` solo.
4. Custom domains -> agregar fuzzer.com.ar y www.fuzzer.com.ar.

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
