# 🎮 Alcanzar las Estrellas — Del código al móvil

Guía paso a paso para llevar el juego (Python + Pygame, exportado con **pygbag**) desde tu ordenador hasta una APK instalable en Android, usando **GitHub Pages** como servidor web y una herramienta **Web to App** para el empaquetado final.

---

## 📋 Índice

1. [Qué necesitas antes de empezar](#1-qué-necesitas-antes-de-empezar)
2. [Exportar el juego a web con pygbag](#2-exportar-el-juego-a-web-con-pygbag)
3. [Subir el build a GitHub Pages](#3-subir-el-build-a-github-pages)
4. [Convertir la web en una APK](#4-convertir-la-web-en-una-apk)
5. [Probar el resultado](#5-probar-el-resultado)
6. [Notas técnicas — qué se ajustó para que funcione bien en móvil](#6-notas-técnicas--qué-se-ajustó-para-que-funcione-bien-en-móvil)
7. [Problemas comunes](#7-problemas-comunes)

---

## 1. Qué necesitas antes de empezar

- **Python 3.12** instalado en tu ordenador.
- **pygbag** instalado:
  ```bash
  pip install pygbag
  ```
- Una **cuenta de GitHub** (gratuita).
- **Git** instalado, o simplemente subir archivos desde la web de GitHub.
- El proyecto del juego con esta estructura mínima:
  ```
  alcanzar/
  ├── main.py
  ├── botones_movil.py
  ├── teclado_movil.py
  ├── configuracion_juego.py
  ├── ...(el resto de módulos .py)
  └── assets/            <- imágenes, sonidos, fuentes, etc.
  ```

---

## 2. Exportar el juego a web con pygbag

Desde la carpeta raíz del proyecto (donde está `main.py`), ejecuta:

```bash
py 3.12 -m pygbag main.py
```

Esto hace dos cosas importantes:

- Levanta un servidor local (normalmente en `http://localhost:8000`) para que puedas **probar el juego en el navegador de tu propio ordenador** antes de subir nada.
- Una vez levantado el servidor local para ver errores es en `http://localhost:8000#debug` 
- Genera una carpeta `build/web/` con todo lo necesario para publicarlo: el `index.html`, el `.apk`/`.tar.gz` con tus assets empaquetados, y los scripts de arranque de pygbag.

> ⚠️**Importante.** Si al compilar da un error como **Error browserfs.min.js** hay que buscar en dentro de la carpeta `build/web-cache/` el archivo *27613e24ba16d44f2a5c88150c6d64e5.tmpl* y borrad toda esta línea **< script src="{{cookiecutter.cdn}}/browserfs.min.js">< /script>** y volver a compilar con:

```bash
py -3.12 -m pygbag --template ".\build\web-cache\27613e24ba16d44f2a5c88150c6d64e5.tmpl" .
```

> 💡 Si ya tienes tu propio `index.html` personalizado (con los ajustes de botones táctiles, teclado virtual y orientación que hemos ido preparando), **sustituye el `index.html` generado por el tuyo** dentro de `build/web/` antes de subirlo.

Comprueba en local que todo funciona (sonido, controles de teclado, que carga sin errores en la consola del navegador) antes de pasar al siguiente paso.

---

## 3. Subir el build a GitHub Pages

### 3.1. Crea el repositorio

1. Ve a [github.com/new](https://github.com/new) y crea un repositorio nuevo (puede ser público o privado, aunque GitHub Pages gratuito requiere que sea **público** salvo que tengas GitHub Pro).
2. Nómbralo, por ejemplo, `juego-web`.

### 3.2. Sube el contenido de `build/web/`

**Opción A — con Git:**
```bash
cd build/web
git init
git remote add origin https://github.com/TU_USUARIO/alcanzar-web.git
git add .
git commit -m "Primera version web del juego"
git branch -M main
git push -u origin main
```

**Opción B — sin Git:**
Arrastra todos los archivos de `build/web/` directamente a la página del repositorio en GitHub (botón *"Add file" → "Upload files"*).

>⚠️**Advertencia.** Manualmente subir los archivos directamente, el peso máximo entre 25 - 50 MB. Para mayores usar `git` o *GitHub Desktop*.


### 3.3. Activa GitHub Pages

1. En el repositorio, ve a **Settings → Pages**.
2. En *"Branch"*, selecciona `main` y la carpeta `/ (root)`.
3. Guarda. GitHub te dará una URL parecida a:
   ```
   https://TU_USUARIO.github.io/alcanzar-web/
   ```
4. Espera 1-2 minutos y abre esa URL — debería cargar tu juego exactamente igual que en local.

> ⚠️ **Importante**: prueba esta URL directamente desde el navegador del **móvil real** antes de pasar al siguiente paso. Si algo falla en el móvil (controles, teclado, rotación), es mucho más fácil depurarlo aquí que una vez esté empaquetado en una APK.

---

## 4. Convertir la web en una APK

Con la web ya funcionando desde GitHub Pages, el siguiente paso es empaquetarla como app instalable. Hay varias herramientas "Web to App"; estas son las más usadas:

| Herramienta | Tipo | Notas |
|---|---|---|
| [PWABuilder](https://www.pwabuilder.com/) | Gratuita, de Microsoft | La más recomendable para empezar. Genera un proyecto Android listo para compilar o firmar. |
| [WebIntoApp](https://www.webintoapp.com/) | Gratuita, Pago | Generá proyectos desde páginas y por archivos si pesa menos de 2 mb. Sencilla y rapida. 
| [Median.co](https://median.co/) (antes GoNative) | Freemium | Interfaz muy sencilla, buena para pruebas rápidas. |
| [Appilix](https://appilix.com/) | Freemium | Pensada específicamente para "convertir una web en app" sin código. |

### Pasos generales (con PWABuilder, como ejemplo):

1. Entra en [pwabuilder.com](https://www.pwabuilder.com/).
2. Pega la URL de tu GitHub Pages (`https://TU_USUARIO.github.io/alcanzar-web/`).
3. PWABuilder analizará la web. Como es un juego pygbag y no un PWA "de manual", es normal que marque avisos sobre manifest/service worker — puedes ignorarlos para este uso (no afectan a que la APK funcione, solo son recomendaciones para app stores).
4. Ve a la pestaña **"Package for stores" → Android**.
5. Configura:
   - **Nombre de la app**: el que quieras (ej. "Alcanzar las Estrellas").
   - **Package ID**: identificador único, tipo `com.tuusuario.alcanzar`.
   - **Orientación**: déjala en *"any"* o *"sensor"*, ya que el juego ya se adapta solo a vertical/horizontal.
   - **Pantalla completa**: actívala (*display: fullscreen* o *standalone*) para que no se vean barras del navegador.
6. Descarga el paquete generado (te dará un `.aab` o un proyecto Android Studio, según la herramienta).
7. Si te da un `.aab` y necesitas un `.apk` instalable directamente (sin pasar por Google Play), puedes:
   - Usar [bundletool](https://github.com/google/bundletool) de Google para convertir `.aab` → `.apk`, o
   - Elegir la opción de exportación directa a `.apk` si la herramienta la ofrece (Appilix y Median suelen darte el `.apk` directamente).

---

## 5. Probar el resultado

1. Copia el `.apk` al móvil (por USB, Google Drive, o un enlace de descarga).
2. Activa **"Instalar apps de orígenes desconocidos"** en Ajustes → Seguridad (el nombre exacto varía según el Android).
3. Instala y abre la app.
4. Comprueba:
   - Que carga el juego correctamente.
   - Que los botones táctiles responden en toda su superficie, en vertical y en horizontal.
   - Que el teclado virtual aparece al escribir el nombre / responder adivinanzas / consola de trucos.
   - Que al rotar el móvil los controles se recolocan sin que se corte nada.

---

## 6. Notas técnicas — qué se ajustó para que funcione bien en móvil

Si en algún momento tocas el `index.html` o `botones_movil.py`, ten en cuenta estos puntos (ya resueltos, pero útiles si algo se rompe al modificar código):

- **`#canvas3d`** (canvas auxiliar de pygbag para gráficos 3D, que este juego no usa) debe llevar siempre `pointer-events: none !important;`, porque de lo contrario intercepta toques por encima del canvas real y crea una "zona muerta" táctil.
- El **aspect-ratio del canvas** (CSS) debe coincidir siempre exactamente con la resolución interna real de `pantalla` (`pygame.display.set_mode(...)`). Si no coinciden, las coordenadas de `FINGERDOWN` se desajustan. Esto se gestiona dinámicamente con la función JS `actualizar_aspecto_canvas(w, h)`, llamada desde Python cada vez que cambia la orientación.
- El **teclado virtual** funciona mediante un `<input>` HTML oculto (`#teclado_movil`) que se enfoca/desenfoca desde Python vía `teclado_movil.mostrar()` / `teclado_movil.ocultar()`, sincronizado siempre con `pygame.key.start_text_input()` / `stop_text_input()`.
- Los **botones táctiles** (`botones_movil.py`) se recolocan solos al detectar un cambio de orientación real del dispositivo (`platform.window.innerWidth/innerHeight`), añadiendo una franja extra fuera de la imagen del juego (cruz de dirección a un lado, botones de acción al otro) sin invadir el área jugable de 800×600.

---

## 7. Problemas comunes

**El juego se ve pero los botones no responden bien / hay una zona que no funciona**
→ Revisa que `#canvas3d` tenga `pointer-events: none` y que no haya quedado ningún elemento (`#infobox`, `#transfer`, `#pyconsole`) capturando toques por encima del canvas.

**El teclado del móvil no aparece al escribir el nombre**
→ En iOS Safari, el `focus()` a veces solo funciona si ocurre muy pegado a un toque real del usuario. Si falla solo en iPhone, es una limitación conocida del navegador, no un bug del código.

**Al rotar el móvil, el juego se ve distorsionado o los botones desaparecen un instante**
→ Es esperable un pequeño parpadeo mientras `pygame.display.set_mode()` se recalcula; si el problema persiste, revisa que `actualizar_aspecto_canvas()` se esté llamando **después** de cada `set_mode()`.

**La APK se instala pero se queda en pantalla blanca / negra**
→ Casi siempre es que la app está intentando cargar la web sin conexión, o la URL configurada en la herramienta Web to App no es exactamente la de GitHub Pages. Verifica la URL y que el móvil tiene internet.

**GitHub Pages da error 404**
→ Asegúrate de que el `index.html` está en la **raíz** del repositorio (o de la carpeta configurada en Settings → Pages), no dentro de una subcarpeta como `build/web/build/web/`.

---

¡Y ya está! Con esto tienes el camino completo: **pygbag → GitHub Pages → Web to App → APK en el móvil**. 🚀
