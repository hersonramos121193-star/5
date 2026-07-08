# Dashboard 5S — Notaría 22 y 36

Aplicación web (PWA) para evaluar semanalmente las 5S (Seiri, Seiton, Seiso, Seiketsu, Shitsuke) de cada empleado, con sincronización automática en la nube vía Google Sheets.

## Archivos

- `index.html` — la app completa (no requiere instalación, funciona en cualquier navegador).
- `manifest.json` — configuración de la PWA (para poder "instalarla" en el celular/escritorio).
- `sw.js` — service worker (permite trabajar sin internet y mantiene la app actualizada).
- `apps-script.gs` — código para el backend en Google Sheets (nube).

## 1. Desplegar en GitHub Pages

1. Crea un repositorio nuevo en GitHub (ej. `dashboard-5s-notaria`).
2. Sube estos 3 archivos: `index.html`, `manifest.json`, `sw.js` a la raíz del repositorio.
3. Ve a **Settings > Pages**.
4. En "Source" selecciona la rama `main` y carpeta `/ (root)`.
5. Guarda. En un par de minutos tu app estará disponible en:
   `https://TU-USUARIO.github.io/dashboard-5s-notaria/`

## 2. Configurar el backend en Google Sheets (para trabajar en la nube)

1. Crea un Google Sheet nuevo, por ejemplo `5S_Notaria22y36_DB`.
2. Ve a **Extensiones > Apps Script**.
3. Borra el contenido de `Code.gs` y pega **todo** el contenido del archivo `apps-script.gs`.
4. Guarda el proyecto (ícono de disco).
5. Clic en **Implementar > Nueva implementación**.
   - Tipo de implementación: **Aplicación web**.
   - Descripción: la que quieras.
   - Ejecutar como: **Yo (tu cuenta)**.
   - Quién tiene acceso: **Cualquier usuario**.
6. Clic en **Implementar**. Google pedirá autorizar permisos — acepta (es tu propio script).
7. Copia la URL que termina en `/exec`. Ejemplo:
   `https://script.google.com/macros/s/AKfycb.../exec`

## 3. Conectar la app con el Sheet

1. Abre tu Dashboard 5S (el link de GitHub Pages).
2. Ve a la pestaña **⚙ Configuración**.
3. Pega la URL de Apps Script en el campo correspondiente.
4. Clic en **Guardar y sincronizar**.

A partir de este momento, **cada vez que agregues empleados o captures una evaluación, se guardará automáticamente en el Google Sheet**. Si abres el mismo link desde otra computadora o celular (con la misma URL de Apps Script configurada), verás los mismos datos actualizados automáticamente.

> 💡 Tip: puedes compartir el link de GitHub Pages con quien vaya a capturar las evaluaciones. La primera vez tendrá que pegar la misma URL de Apps Script en Configuración.

## Nota técnica: por qué a veces no sincronizaba

Google Apps Script no siempre agrega el permiso necesario (encabezado CORS) para que otra página web (como tu Dashboard en GitHub Pages) pueda leer su respuesta directamente. Por eso la app usa dos trucos:

- Para **leer** datos del Sheet: la app inserta un `<script>` con la URL (técnica JSONP), lo cual sí está permitido por los navegadores y evita el bloqueo de CORS.
- Para **guardar** datos: la app envía la información en modo `no-cors`. Esto significa que el navegador no puede confirmar la respuesta, pero la petición sí llega y se guarda correctamente en el Sheet.

Si alguna vez ves "Failed to fetch" en la pestaña Configuración, verifica primero que:
1. Estás abriendo la app desde el link de **GitHub Pages** (`https://...github.io/...`), no desde un archivo descargado.
2. La URL de Apps Script termina en `/exec` (no en `/dev`).
3. El script está implementado con acceso "Cualquier usuario" y fue vuelto a implementar (Nueva versión) después de cualquier cambio al código.

## 4. Actualizar la app en el futuro

Si vuelves a modificar `index.html` (o cualquier archivo) y lo subes a GitHub:

1. Abre la app en el dispositivo.
2. Ve a **⚙ Configuración > Forzar actualización de la app**.
3. Esto limpia la caché del dispositivo y carga la versión más reciente.

## 5. Uso diario

- **Empleados**: agrega a cada empleado con su nombre y departamento (Registro, Escrituras, Catastro, etc.).
- **Evaluar**: cada viernes, selecciona al empleado, confirma la fecha (por defecto toma el viernes más reciente) y desliza cada una de las 5S del 1 al 20.
- **Dashboard**: verás un "sello" de color por empleado (verde/amarillo/rojo) con el promedio de la semana seleccionada.
- **Historial**: consulta todas las evaluaciones pasadas, filtra por empleado o departamento.
- **Exportar a Excel**: desde Historial, botón "Exportar a Excel" — descarga un `.xlsx` con la calificación de cada empleado y evaluación.

## Escala de calificación

Cada una de las 5S se califica de **1 a 20**:

- 🟢 **Verde (16–20):** cumple el estándar.
- 🟡 **Amarillo (11–15):** requiere atención.
- 🔴 **Rojo (1–10):** crítico, requiere plan de acción inmediato.
