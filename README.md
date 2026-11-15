# CapCut Club — Edición 2026

Landing de venta del CapCut Club (curso de edición en CapCut, 6 módulos, 23 USD
pago único, cobro por Hotmart). Sitio estático, sin build ni dependencias: los
archivos se sirven tal cual.

| | |
|---|---|
| **Producción** | https://capcutclub.planediciontotal.com — subdominio en Hostinger. La URL viene del README anterior; el código no trae `canonical` ni `og:url` que la confirme [por confirmar que esta versión esté desplegada ahí]. |
| **Hosting** | Hostinger, subida manual a la carpeta del subdominio (sin Git conectado) |
| **Repo** | https://github.com/davidprietooficial-ux/pet-capcutclub (antes `capcut-club-landing`) |
| **Rama** | `main` |
| **Estado** | En revisión: `noindex, nofollow` en `index.html` y `Disallow: /` en `robots.txt` |

El boceto anterior, de un solo archivo, sigue archivado en `pet-capcutclub-preview`
(GitHub Pages). Este repo no lo sustituye.

## Stack

- HTML + CSS + JavaScript sin frameworks (ES5, sin módulos, sin `package.json`).
- Tipografía de titulares autoalojada (`Special Gothic Expanded One`, woff2). El
  cuerpo usa Helvetica del sistema: no se descarga nada.
- Videos MP4 autoalojados con un reproductor propio (sin YouTube ni iframes).
- Terceros: Meta Pixel, Microsoft Clarity, checkout de Hotmart, WhatsApp.

## Estructura

```
index.html        todo el marcado (15 secciones numeradas en comentarios ══ NN ·)
estilos.css       toda la hoja de estilo; colores de acento en :root (bloque ACENTO)
app.js            comportamiento: aparición, barra fija, cupos, reproductores,
                  antes/después, pantalla completa de la VSL, muro de cookies
robots.txt        Disallow: / mientras esté en revisión
.gitignore        ignora ._*, .DS_Store, Thumbs.db, node_modules/
activos/
  favicon.svg
  fuentes/        special-gothic-expanded-one.woff2 (precargada: es el LCP)
  hero-capcut-club.webp, mockup-caja.webp, juan-perfil.webp
  vsl.mp4 + vsl.webp            video de presentación (2:19) y su póster
  antes-despues/  antes.mp4/.webp, despues.mp4/.webp (misma toma, en bucle)
  testimonios/    4 testimonios: <nombre>.mp4 + <nombre>.webp (póster)
```

Secciones de `index.html`: 01 Hero · 02 Cinta de la oferta · 03 Video de
presentación · 04 El problema · 05 Qué vas a lograr · 06 CTA intermedio ·
07 Temario (`#modulos`) · 08 Quién te enseña · 09 Antes y después ·
10 Testimonios · 11 La oferta (`#oferta`) · 12 Preguntas frecuentes · 13 Cierre ·
14 Cinta otra vez · 15 Pie. Además: barra fija de compra, botón flotante de
WhatsApp y muro de entrada (cookies).

## Correr en local

No hay build ni dependencias. Basta con un servidor estático en la raíz del repo
(abrir `index.html` con `file://` también funciona, pero los videos y la fuente se
comportan mejor por HTTP):

```bash
python3 -m http.server 8000
# o: npx serve .
```

Para volver a ver el muro de cookies, borra la clave
`capcut-club:consentimiento` de `localStorage`.

## Despliegue

Hostinger no está conectado a GitHub: el despliegue es subir archivos.

1. Haz los cambios y súbelos a `main`.
2. **Regenera los `?v=`** si cambiaste `estilos.css` o `app.js` (ver abajo).
3. Sube a la carpeta del subdominio `capcutclub` en Hostinger (hPanel → Archivos,
   o la API/MCP de Hostinger): `index.html`, `estilos.css`, `app.js`,
   `robots.txt` y la carpeta `activos/` completa.
4. No subas `.git/`, `README.md`, `.gitignore` ni archivos `._*` de macOS.
5. Abre la URL en ventana privada y comprueba muro, videos y botones de compra.

### Versionado de CSS y JS (obligatorio en cada despliegue)

`index.html` carga `estilos.css?v=XXXXXXXX` y `app.js?v=XXXXXXXX`. El valor son los
8 primeros caracteres del sha256 del archivo. Según el comentario del HTML, el
servidor manda CSS y JS con caché de 7 días y revalida siempre `index.html`. Sin
cambiar el `?v=`, quien ya visitó la página recibe el HTML nuevo con el CSS y el
JS viejos.

```bash
shasum -a 256 estilos.css app.js | cut -c1-8
```

Pega cada valor en su `?v=` de `index.html` (la línea del `<link rel="stylesheet">`
en el `<head>` y la del `<script src="app.js" defer>` al final del body).
Para comprobar que coinciden antes de subir:

```bash
shasum -a 256 estilos.css app.js | cut -c1-8
grep -oE '(estilos\.css|app\.js)\?v=[0-9a-f]+' index.html
```

En un disco ExFAT, macOS crea copias `._*` de cada archivo. Bórralas antes de
subir: `find . -name '._*' -not -path './.git/*' -delete`.

## Tracking e integraciones

| Integración | Dónde | ID / dato público |
|---|---|---|
| Meta Pixel | `<head>` de `index.html`, se carga siempre | `1555727432954581` (solo `PageView`) |
| Microsoft Clarity | `<head>` de `index.html`, se carga siempre | `y46j54itlv` |
| Hotmart (checkout) | todos los `<a data-checkout>` (5 botones: barra fija, hero, CTA intermedio 06, oferta 11 y cierre 13) | `https://pay.hotmart.com/M107158341L?off=oynghv9l&checkoutMode=10&bid=…` |
| WhatsApp (soporte) | botón flotante y pie | enlaces `wa.me/…` en `index.html` |
| GA4 / GTM | — | No hay |
| Formularios | — | No hay; la compra se hace en Hotmart |

- **Medición sin condiciones.** Decisión del cliente (18/08/2026): el Pixel y
  Clarity se piden con la página y **no** esperan al consentimiento. El muro de
  cookies solo recoge la aceptación y bloquea el uso del sitio sin ella. No
  gobierna los scripts.
- No hay eventos de conversión (`InitiateCheckout`, `Purchase`) en el código: la
  compra ocurre en Hotmart. Las conversiones de compra se configuran en Hotmart
  [por confirmar].
- **Cupos simulados.** El contador "Quedan N cupos" es urgencia simulada, no
  inventario real. Empieza en `data-cupos-inicial` (100) y baja de a uno hasta
  `data-cupos-piso` (7), nunca llega a 0, a intervalos aleatorios de 6 a 20 s. Se
  configura en el `<p data-cupos-config>` del hero y se pinta en todos los
  `<span data-cupos>` (hero y oferta). Al recargar vuelve a empezar en 100.

## Dónde se cambia lo que más se edita

| Dato | Dónde |
|---|---|
| Enlace de compra Hotmart | todos los `href` de `a[data-checkout]` en `index.html`; búscalos y reemplázalos todos a la vez |
| Precio (23 USD, "antes 67") | texto de los 5 botones de compra, barra fija (`.precio-mini`), cintas 02 y 14, oferta (`.cifra`, `.precio-antes`, `.precio-nota`), FAQ, titular del cierre y `<meta name="description">`. Busca `23` y `67` en `index.html` |
| "Acceso por 12 meses renovable" y "7 días de garantía" | hero, cintas 02 y 14, lista `.incluye` y `.sellos` de la oferta ("Garantía de 7 días · Devolución automática"), FAQ y `<meta name="description">` (solo los 12 meses) |
| Cupos | atributos `data-cupos-inicial` / `data-cupos-piso` en el hero |
| Temario | sección 07; el módulo 5 lleva `<span class="proximamente">` |
| Videos | sustituir el archivo en `activos/` con el mismo nombre y regenerar el `.webp` del póster |
| Testimonios | sección 10: un bloque `.reproductor` por persona, con su `.mp4` y `.webp` |
| WhatsApp | enlaces `wa.me` del botón flotante y del pie |
| Textos legales del pie | sección 15: razón social (Mirai Group Co S.A.S.), año, enlaces legales y aviso de no afiliación con CapCut/ByteDance |
| Medios de pago y entrega | oferta 11: `Pago seguro por Hotmart · Visa · Mastercard · PayPal` y "Acceso entregado por correo"; deben coincidir con la configuración del producto en Hotmart |
| Texto del muro de cookies | bloque "Muro de entrada" al final de `index.html` (nombra a Meta y Microsoft Clarity: actualízalo si cambian los terceros) |
| IDs de Pixel / Clarity | los dos `<script>` del bloque "Medicion" en el `<head>` |
| Colores | bloque `ACENTO` de `:root` en `estilos.css`, siete variables (`--acento-claro`, `--acento`, `--acento-hondo`, `--acento-texto`, `--acento-tenue`, `--acento-neon`, `--acento-tinta`). Hoy es el azul del key visual «CAPCUT CLUB by @juan.edita». El comentario del CSS dice que no hay acentos literales fuera del bloque, pero los degradados derivados justo debajo llevan paradas azules fijas (`#155fc0`, `#9bd8ff`, `#3f9df0`): cámbialas también si cambias de paleta |

### Responsive

Tres etapas: móvil (base), `768px` y `1024px`. No hay más puntos de corte (hay un
`max-width: 767px` puntual para ajustes solo de móvil).

| | móvil | tableta | escritorio |
|---|---|---|---|
| Módulos de iniciación | 2 col | 2 col | 4 col |
| Módulos CapCut Club | 2 col | 2 col | 2 col |
| Preguntas frecuentes | 1 col | 2 col | 3 col |
| Testimonios | carrusel | 2 col | 4 col |

### Comportamiento que conviene conocer (`app.js`)

- Cada pieza es independiente y la página sigue siendo usable sin JS: los videos
  traen `controls` en el HTML y `app.js` los quita solo cuando su reproductor ya
  funciona.
- Solo un video suena a la vez. La VSL y los testimonios comparten reproductor.
- En celular, el primer play con sonido de la VSL abre pantalla completa (y pide
  horizontal en Android).
- Los clips de antes/después corren sincronizados y sin controles.
- Con `prefers-reduced-motion` no hay autoplay ni animaciones, y el contador de
  cupos no baja.

## Seguridad

- En el repo no hay `.htaccess`, CSP ni cabeceras de seguridad. La caché de 7
  días para CSS/JS que menciona el HTML vendría de la configuración del servidor
  en Hostinger [por confirmar].
- No hay formularios ni datos del usuario en el sitio. Lo único que guarda es la
  aceptación de cookies en `localStorage`.
- Los enlaces externos llevan `target="_blank" rel="noopener noreferrer"`.

## Problemas conocidos y pendientes

- **Indexación bloqueada.** Al aprobar, quita el `noindex` de `index.html` y el
  `Disallow` de `robots.txt`. Si el sitio ya está en producción con estos
  bloqueos, no aparece en buscadores.
- **Páginas legales:** los tres enlaces del pie (Aviso legal, Términos,
  Privacidad) apuntan a `#`.
- **Módulo 5 (CapCut para celular)** marcado «Próximamente».
- **Sin metadatos sociales:** no hay `og:*`, `twitter:*` ni `canonical`, así que
  la vista previa al compartir el enlace la decide cada red.
- **Peso de los videos:** unos 30 MB en total (VSL 8 MB). Usan
  `preload="none"`/`metadata`, pero se recomienda comprimirlos antes de cambiarlos.
- El `?v=` se regenera a mano. Si se olvida, aparecen errores visuales en
  visitantes recurrentes.
- **Comentario desactualizado en la sección 03 de `index.html`:** dice que los
  controles nativos de la VSL "vuelven en cuanto suena", pero `app.js` los quita
  siempre (play, pausa y pantalla completa propios, sin barra para adelantar).
  Manda el código.
- **Cupos y garantía:** el contador es simulado y la garantía de 7 días con
  "devolución automática" depende de la configuración del producto en Hotmart
  [por confirmar que coincida].

## Repos relacionados

Todos en `github.com/davidprietooficial-ux`, con la convención `pet-<sitio>`.

| Repo | Sitio |
|---|---|
| pet-form-webinar | form-webinar.planediciontotal.com (Hostinger) |
| pet-clase | clase.planediciontotal.com (Hostinger) |
| pet-plandedicion-venta | pet-plandedicion-venta.vercel.app (Vercel) |
| **pet-capcutclub** | capcutclub.planediciontotal.com (Hostinger), este repo |
| pet-capcutclub-preview | boceto de revisión en GitHub Pages |
| pet-workshopexclusivo | workshopexclusivo.planediciontotal.com (Hostinger, privado) |
