# Portafolio con Supabase + GitHub Pages

## Qué contiene este paquete

| Archivo | Para qué sirve | Dónde va en tu repositorio |
|---|---|---|
| `index.html` | Portafolio público. Lee el contenido desde Supabase. | Raíz (reemplaza el actual) |
| `admin.html` | Panel privado: inicio de sesión con Supabase Auth y el mismo editor visual que ya usabas. | Raíz (archivo nuevo) |
| `assets/style.css` | Estilos (los mismos de tu diseño actual). | Carpeta `assets/` (nueva) |
| `assets/app.js` | Código compartido: conexión a Supabase, lectura, edición y guardado. | Carpeta `assets/` |
| `sql/supabase-setup.sql` | Diagnóstico y configuración de tablas, RLS y Storage. | NO se sube a GitHub; se pega en el SQL Editor de Supabase |
| `contenido_inicial.json` | Tu contenido actual (textos, ajustes, imágenes y CV). | NO se sube a GitHub (tiene tus datos personales); se importa desde `admin.html` |

**Cómo funciona.** `index.html` y `admin.html` usan el mismo `app.js`. En `index.html` el contenido es solo lectura. En `admin.html`, después de iniciar sesión, aparece la barra de edición. Al pulsar **Guardar cambios**, se actualiza la fila `id = 1` de `portfolio_content`. Las imágenes y el CV se suben a Supabase Storage (bucket público `portfolio`), y en la base solo se guarda su URL.

**Qué se puede editar:** textos, imágenes y fondos (con ajuste), certificados y sellos, historial laboral con logos, recomendaciones, iconos, etiquetas emergentes, bloques nuevos, campos nuevos, enlaces y contacto, CV, **colores principales** y **visibilidad de secciones del menú**.

**Límites actuales:** el orden de las secciones del menú es fijo (solo se pueden ocultar). Los colores configurables son los 6 de la paleta (menú, títulos, enlaces, botones, texto, fondo claro); el degradado oscuro de la portada usa colores fijos, se cambia con su imagen de fondo.

---

## Pasos de implementación

### 1. Respalda lo que ya tienes
1. En tu repositorio de GitHub: **Code > Download ZIP** y guárdalo.
2. Si tu página actual tiene datos que no estén en el editor que usabas aquí, avísame antes de reemplazar nada.

### 2. Archivos a reemplazar o crear
- Reemplazar: `index.html`.
- Crear: `admin.html`, carpeta `assets/` con `style.css` y `app.js`.
- Si tu CV actual está en el repositorio, déjalo ahí; no estorba (el nuevo CV se sube desde el panel).

### 3. Dónde colocarlos
```
tu-repositorio/
├── index.html
├── admin.html
└── assets/
    ├── style.css
    └── app.js
```
(No subas `sql/` ni `contenido_inicial.json`.)

### 4. Configurar las URL de Supabase Auth
1. Supabase > **Authentication > URL Configuration**.
2. **Site URL:** `https://TU-USUARIO.github.io/TU-REPOSITORIO/`
3. **Redirect URLs:** agrega `https://TU-USUARIO.github.io/TU-REPOSITORIO/**`
4. **Authentication > Sign In / Providers:** desactiva **Allow new users to sign up** (así nadie puede crearse una cuenta).
5. Verifica en **Authentication > Users** que existe tu usuario administrador (correo y contraseña). Si no, créalo con **Add user**.

### 5. Ejecutar el SQL (por partes)
1. Supabase > **SQL Editor > New query**. Abre `sql/supabase-setup.sql`.
2. **Parte 0 (diagnóstico):** ejecútala y revisa:
   - `id` y `content` de tipo **jsonb**. Si `content` NO es jsonb, avísame antes de seguir.
   - RLS activado (`true`).
   - Copia el UUID de la política `Only Luis can update portfolio`.
3. **Parte 1:** solo si falta `updated_at`.
4. **Parte 2:** crea la lectura pública solo si no existe ya una política SELECT.

### 6. Permisos RLS (qué hacen)
- **Lectura pública:** cualquier visitante puede leer `portfolio_content`.
- **Actualización:** la política existente `Only Luis can update portfolio` se conserva; solo tu UUID puede modificar.
- **Sin INSERT/DELETE** para visitantes.
- **Storage:** solo tu UUID puede subir, cambiar o borrar archivos (Parte 4).

### 7. Registro inicial
1. Ejecuta la **Parte 3**. Crea `id = 1` con `{}` solo si no existía (no sobrescribe nada).
2. Con `{}`, el portafolio muestra los textos base de tu HTML. Tu contenido real se carga en el paso 8.

### 8. Imágenes y CV (Storage)
1. Ejecuta la **Parte 4** del SQL, **reemplazando `PEGA-AQUI-TU-UUID` (3 veces) por tu UUID**. O crea el bucket a mano: **Storage > New bucket > `portfolio` > Public bucket**, y luego ejecuta solo las 3 políticas.
2. Sube los archivos del paso 9 y abre `admin.html` (paso 11).
3. En el panel: **Colores y secciones > Importar contenido (.json)** y elige `contenido_inicial.json`. La página sube todas tus imágenes y tu CV a Storage, guarda el contenido en Supabase y se recarga sola.
4. Para cambiar el CV después: **Enlaces y contacto > Subir CV** (PDF o .docx, máximo 8 MB) y **Guardar cambios**. El botón **Descargar CV** funciona sin iniciar sesión.

### 9. Subir a GitHub
- **Desde la web:** repositorio > **Add file > Upload files** > arrastra `index.html`, `admin.html` y la carpeta `assets` > **Commit changes**.
- **Con Git:** `git add . && git commit -m "Portafolio con Supabase" && git push`.

### 10. Comprobar que GitHub Pages publicó
1. Repositorio > **Actions**: espera el despliegue "pages build and deployment" en verde (1 a 3 minutos).
2. Abre `https://TU-USUARIO.github.io/TU-REPOSITORIO/` y recarga con **Ctrl + F5**.

### 11. Entrar al panel
1. Abre `https://TU-USUARIO.github.io/TU-REPOSITORIO/admin.html`.
2. Inicia sesión con tu correo y contraseña de Supabase.
3. Aparece la barra azul inferior. **Salir** cierra la sesión.

### 12. Editar, guardar y comprobar
1. Edita (textos, imágenes, colores, secciones, etc.).
2. Pulsa **Guardar cambios**. El mensaje "Cambios guardados en Supabase" aparece **solo** si Supabase confirmó la actualización. Si falla, ves el motivo y conservas tus ediciones en pantalla.
3. Abre `index.html` en otra pestaña y recarga: debes ver el cambio.

### 13. Verificar desde otro equipo
Abre la URL pública en tu celular (con datos móviles) o en otro navegador. Debe mostrar la última versión guardada. Los cambios solo aparecen al **volver a cargar** la página.

### 14. Errores comunes
| Mensaje o síntoma | Causa y solución |
|---|---|
| "Correo o contraseña incorrectos" | Revisa el usuario en Authentication > Users. |
| "No se guardó: no existe el registro id=1 o no tienes permiso" | Ejecuta la Parte 3; confirma que la política de UPDATE usa tu UUID. |
| "new row violates row-level security policy" al subir imágenes | Faltan las políticas de Storage o el UUID no coincide (Parte 4). |
| "Bucket not found" | Crea el bucket `portfolio` (paso 8). |
| "No existe el registro id=1" al abrir el panel | Ejecuta la Parte 3 del SQL. |
| El portafolio muestra los textos base | La fila tiene `{}`. Importa `contenido_inicial.json`. |
| `invalid input syntax for type json` al guardar | `content` no es jsonb. Avísame. |
| Cambios no visibles en el público | Ctrl + F5. Revisa que el despliegue de Pages terminó. |
| El CV se abre en vez de descargarse | Recarga con Ctrl + F5; el enlace usa `?download=` para forzar la descarga. |

---

## Guardar vs. recargar vs. tiempo real
- **Guardar:** escribe en la base de datos de Supabase. Es lo único que hace permanentes los cambios.
- **Recargar:** el navegador vuelve a pedir el contenido a Supabase. Los visitantes ven los cambios al abrir o recargar.
- **Tiempo real (Supabase Realtime):** actualizaría páginas ya abiertas sin recargar. **No está activado**: no es necesario para este portafolio. Se puede añadir después si lo quieres.

## Pruebas recomendadas (las ejecutas tú)
1. **Conexión y carga:** abre `index.html` con la consola del navegador (F12). No debe haber errores en rojo y debe verse tu contenido.
2. **Inicio de sesión:** `admin.html` con credenciales correctas entra; con incorrectas muestra el error.
3. **Usuario no autorizado:** crea un usuario de prueba, inicia sesión e intenta guardar: debe aparecer "no tienes permiso". Después bórralo.
4. **Persistencia:** cambia un texto y un color, guarda, abre en otro navegador: deben verse.
5. **Imagen:** reemplaza una imagen, guarda y revisa en otro equipo.
6. **CV:** en ventana de incógnito (sin sesión), pulsa **Descargar CV**.
7. **Navegadores:** Chrome, Edge, Firefox, Safari y un celular.
8. **Sin conexión:** desactiva internet y abre `index.html`: debe mostrar la versión base sin romperse. En `admin.html`, el guardado queda desactivado.

## Seguridad
- Solo se usa la **publishable key**, que es pública por diseño. La protección real está en RLS y en las políticas de Storage.
- Nunca pegues la clave `service_role` ni contraseñas en estos archivos.
- Usa una contraseña larga y activa MFA en tu cuenta de Supabase si está disponible.
- `admin.html` tiene `noindex`, pero eso no es seguridad: la seguridad es el inicio de sesión y RLS.
